---
title: "Azure IoT 中心设备管理入门 (Node)| Microsoft Docs"
description: "如何使用 IoT 中心设备管理进行远程设备重启。 使用 Azure IoT SDK for Node.js 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: ecc6f4a1a8cbb07d9f610e8f6fb5ca66b7532513
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-with-device-management-node"></a>设备管理入门 (Node)
## <a name="introduction"></a>介绍
IoT 云应用程序可以使用 Azure IoT 中心中的基元（即设备克隆和直接方法）远程启动和监视设备上的设备管理操作。 此文章提供有关 IoT 云应用程序和设备如何使用 IoT 中心协同工作来启动和监视远程设备重新启动的指导和代码。

若要从基于云的后端应用远程启动和监视设备上的设备管理操作，请使用 Azure IoT 中心内的基元，例如[设备克隆][lnk-devtwin]和[直接方法][lnk-c2dmethod]。 本教程说明后端应用和设备如何协同工作，实现从 IoT 中心启动和监视远程设备重新启动。

使用直接方法可从云中的后端应用启动设备管理操作，例如重新启动、恢复出厂设置以及固件更新。 设备负责以下操作：

* 处理从 IoT 中心发送的方法请求。
* 在设备上启动相应的设备特定操作。
* 通过向 IoT 中心报告的属性，提供状态更新。

可以使用云中的后端应用运行设备克隆查询，以报告设备管理操作的进度。

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。
* 创建包含重新启动该设备的直接方法的模拟设备应用。 直接方法是从云中调用的。
* 创建一个 .NET 控制台应用，其通过 IoT 中心在模拟设备应用上调用重新启动直接方法。

在本教程结束时，将会创建两个 Node.js 控制台应用：

**dmpatterns_getstarted_device.js**，它使用先前创建的设备标识连接到 IoT 中心，接收重新启动直接方法，模拟物理重新启动，并报告上次重新启动的时间。

**dmpatterns_getstarted_service.js**，它调用模拟设备应用中的直接方法，显示响应，并显示更新后的报告属性。

若要完成本教程，需要以下各项：

* Node.js 版本 0.12.x 或更高版本， <br/>  [准备开发环境][lnk-dev-setup]介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分中，你将

* 创建一个 Node.js 控制台应用，用于响应通过云调用的直接方法
* 触发模拟设备重新启动
* 通过报告的属性，设备克隆查询可标识设备及设备上次重新启动的时间

1. 创建名为 **manageddevice** 的空文件夹。  在 **manageddevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **manageddevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 设备 SDK 包和 **azure-iot-device-mqtt** 包：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 在 **manageddevice** 文件夹中，使用文本编辑器创建 **dmpatterns_getstarted_device.js** 文件。
4. 在 **dmpatterns_getstarted_device.js** 文件开头添加以下“require”语句：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. 添加 **connectionString** 变量，并使用它创建一个**客户端**实例。  将连接字符串替换为设备连接字符串。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 添加以下函数，实现设备上的直接方法
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. 打开与 IoT 中心的连接并启动直接方法侦听器：
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. 保存并关闭 **dmpatterns_getstarted_device.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动
在本部分中，你将创建一个 .NET 控制台应用（使用 C#）以使用直接方法在设备上启动远程重新启动。 该应用使用设备孪生查询来搜索该设备的上次重新启动时间。

1. 创建一个名为 **triggerrebootondevice** 的空文件夹。  在 **triggerrebootondevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **triggerrebootondevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iothub** 设备 SDK 包和 **azure-iot-device-mqtt** 包：
   
    ```
    npm install azure-iothub --save
    ```
3. 在 **triggerrebootondevice** 文件夹中，使用文本编辑器创建 **dmpatterns_getstarted_service.js** 文件。
4. 在 **dmpatterns_getstarted_service.js** 文件开头添加以下“require”语句：
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 添加以下变量声明并替换占位符值：
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. 添加以下函数以调用设备方法来重新启动目标设备：
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. 添加以下函数以查询设备并获取上次重新启动时间：
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. 添加以下代码以调用函数，触发重新启动直接方法并查询上次重新启动时间：
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. 保存并关闭 **dmpatterns_getstarted_service.js** 文件。

## <a name="run-the-apps"></a>运行应用
现在可以运行应用了。

1. 在 **manageddevice** 文件夹的命令提示符处，运行以下命令以开始侦听重新启动直接方法。
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. 在 **triggerrebootondevice** 文件夹的命令提示符处，运行以下命令以触发远程重新启动并查询设备孪生以查找上次重新启动时间。
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. 可在控制台查看对直接方法的设备响应。

## <a name="customize-and-extend-the-device-management-actions"></a>自定义和扩展设备管理操作
IoT 解决方案可扩展已定义的设备管理模式集，或通过使用设备孪生和云到设备方法基元启用自定义模式。 设备管理操作的其他示例包括恢复出厂设置、固件更新、软件更新、电源管理、网络和连接管理以及数据加密。

## <a name="device-maintenance-windows"></a>设备维护时段
通常情况下，将设备配置为在某一时间执行操作，以最大程度减少中断和停机时间。  设备维护时段是一种常用模式，用于定义设备应更新其配置的时间。 后端解决方案使用设备克隆所需属性在设备上定义并激活策略，以启用维护时段。 当设备收到维护时段策略时，它可以使用设备克隆报告属性报告策略状态。 然后，后端应用可以使用设备克隆查询来证明设备和每个策略的符合性。

## <a name="next-steps"></a>后续步骤
在本教程中，将使用直接方法触发设备上的远程重新启动。 使用报告属性报告设备上次重新启动时间，并查询设备孪生从云中发现设备上次重新启动时间。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅：

[教程：如何进行固件更新][lnk-fwupdate]

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

若要继续完成 IoT 中心入门内容，请参阅 [IoT 网关 SDK 入门][lnk-gateway-SDK]。

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

