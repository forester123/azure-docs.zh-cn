---
title: "在 mbed 上使用 C 连接设备 | Microsoft Docs"
description: "介绍如何使用在 mbed 设备上运行的以 C 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: zh-cn
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>将设备连接到远程监视预配置解决方案 (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>生成并运行 C 示例解决方案

以下说明介绍用于将[启用 mbed 的 Freescale FRDM-K64F][lnk-mbed-home] 设备连接到远程监视解决方案的步骤。

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>将 mbed 设备连接到网络和桌面计算机

1. 使用以太网电缆将 mbed 设备连接到网络。 此步骤是必需的，因为示例应用程序需要 Internet 访问。

1. 请参阅 [mbed 入门][lnk-mbed-getstarted]以将 mbed 设备连接到桌面 PC。

1. 如果桌面 PC 在运行 Windows，请参阅 [PC 配置][lnk-mbed-pcconnect]以配置对 mbed 设备的串行端口访问。

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>创建 mbed 项目并导入示例代码

执行以下步骤，向 mbed 项目添加一些示例代码。 用户导入远程监视初学者项目，然后将项目更改为使用 MQTT 协议而非 AMQP 协议。 目前，用户需通过 MQTT 协议使用 IoT 中心的设备管理功能。

1. 在 Web 浏览器中，转到 mbed.org [开发人员站点](https://developer.mbed.org/)。 如果尚未注册，则会看到一个用于创建帐户的选项（它是免费的）。 否则，使用你的帐户凭据登录。 然后在页面右上角单击“**编译器**”。 通过此操作将转到“*工作区*”界面。

1. 请确保你使用的硬件平台出现在窗口右上角，或单击右角的图标以选择你的硬件平台。

1. 在主菜单上单击“**导入**”。 然后单击“单击此处从 URL 导入”。
   
    ![开始导入到 mbed 工作区][6]

1. 在弹出窗口中，输入示例代码 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ 的链接，然后单击“**导入**”。
   
    ![将示例代码导入到 mbed 工作区][7]

1. 可以在 mbed 编译器窗口中看到导入此项目也会导入各种库。 一些库由 Azure IoT 团队提供并维护（[azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)），而另其他库是 mbed 库目录中可用的第三方库。
   
    ![查看 mbed 项目][8]

1. 在“项目工作区”中，右键单击 **iothub\_amqp\_transport** 库，单击“删除”，然后单击“确定”进行确认。

1. 在“项目工作区”中，右键单击 **azure\_amqp\_c** 库，单击“删除”，然后单击“确定”进行确认。

1. 右键单击“项目工作区”中的 **remote_monitoring** 项目，选择“导入库”，然后选择“从 URL”。
   
    ![开始将库导入到 mbed 工作区][6]

1. 在弹出窗口中，输入 MQTT 传输库的链接 https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/，然后单击“导入”。
   
    ![将库导入到 mbed 工作区][12]

1. 重复上一步骤，从 https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/ 添加 MQTT 库。

1. 工作区现在如下所示：

    ![查看 mbed 工作区][13]

1. 打开 remote\_monitoring\remote_monitoring.c 文件，将现有的 `#include` 语句替换为以下代码：

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. 删除 remote\_monitoring\remote\_monitoring.c 文件中所有剩余的代码。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>生成并运行示例

添加调用 **remote\_monitoring\_run** 函数的代码，然后生成并运行设备应用程序。

1. 添加 **main** 函数，将以下代码置于 remote\_monitoring.c 文件末尾以调用 **remote\_monitoring\_run** 函数：
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. 单击“**编译**”以生成程序。 可以安全地忽略任何警告，但是如果生成产生错误，请在更正它们之后再继续进行。

1. 如果生成成功，则 mbed 编译器网站会生成一个具有项目名称的 .bin 文件，并将它下载到本地计算机。 将 .bin 文件复制到设备。 将 .bin 文件保存到设备会导致设备重新启动并运行 .bin 文件中包含的程序。 可以通过在 mbed 设备上按重置按钮，来随时手动重新启动程序。

1. 使用 SSH 客户端应用程序（如 PuTTY）连接到设备。 可以通过检查 Windows 设备管理器来确定设备使用的串行端口。
   
    ![][11]

1. 在 PuTTY 中，单击“**串行**”连接类型。 设备通常以 9600 波特进行连接，因此在“**速度**”框中输入 9600。 然后单击“**打开**”。

1. 程序开始执行。 如果程序未在连接时自动启动，则可能必须重置板（按 CTRL+Break 或按板的重置按钮）。
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

