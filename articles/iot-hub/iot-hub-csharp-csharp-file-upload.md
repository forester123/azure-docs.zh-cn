---
title: "使用 Azure IoT 中心从设备上传文件 | Microsoft Docs"
description: "如何使用用于 .NET 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 78fce5e464e065620e2f2da7f001d34b5cfd7a9f
ms.lasthandoff: 03/10/2017


---
# <a name="upload-files-from-your-simulated-device-to-the-cloud-with-iot-hub"></a>使用 IoT 中心将文件从模拟设备上载到云
## <a name="introduction"></a>介绍
Azure IoT 中心是一项完全托管的服务，可在数百万个设备和一个解决方案后端之间实现安全可靠的双向通信。 （[IoT 中心入门]和[使用 IoT 中心发送云到设备消息]）教程介绍了 IoT 中心提供的基本的设备到云和云到设备消息传递功能。 [处理设备到云的消息]教程介绍了一种在 Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，你无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如，包含图像、视频、以高频率采样的震动数据或者某种形式的预处理数据的大型文件。 通常使用 [Azure 数据工厂]或 [Hadoop] 堆栈等工具在云中批处理这些文件。 如果你偏好通过从设备上载文件来发送事件，仍可以使用 IoT 中心的安全性与可靠性功能。

本教程的内容基于[使用 IoT 中心发送云到设备消息]教程中所述的代码，演示如何使用 IoT 中心的文件上载功能。 其中了说明了如何：

- 安全提供具有 Azure blob URI 的设备，用于上传文件。
- 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

在本教程结束时，会运行 2 个 .NET 控制台应用：

* **SimulatedDevice**，这是[使用 IoT 中心发送云到设备消息]教程中创建的应用的修改版本。 该应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。
* **ReadFileUploadNotification**，它可以接收来自 IoT 中心的文件上载通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、Java 和 Javascript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心]。
> 
> 

若要完成本教程，您需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>将 Azure 存储帐户关联到 IoT 中心
由于模拟设备应用将文件上传到 Blob，因此必须拥有与 IoT 中心关联的 [Azure 存储]帐户。 将 Azure 存储帐户与 IoT 中心相关联时，IoT 中心会生成一个 SAS URI。 设备可以使用此 SAS URI 安全地将文件上载到 Blob 容器。 IoT 中心服务和设备 SDK 协调生成 SAS URI 的过程，并使其可供设备用来上载文件。

按照[通过 Azure 门户配置文件上传][lnk-configure-upload]中的说明，将 Azure 存储帐户关联到 IoT 中心。 确保有一个 Blob 容器与你的 IoT 中心关联并且文件通知已启用。 
   
![在门户中启用文件通知][3]


## <a name="upload-a-file-from-a-simulated-device-app"></a>从模拟设备应用上传文件
在本部分中，你将修改在[使用 IoT 中心发送云到设备消息]中创建的模拟设备应用，以接收来自 IoT 中心的云到设备消息。

1. 在 Visual Studio 中，右键单击 **SimulatedDevice** 项目，单击“**添加**”，然后单击“**现有项**”。 导航到某个图像文件并将它包含在项目中。 本教程假设图像名称为 `image.jpg`。
2. 右键单击该图像，然后单击“**属性**”。 确保“**复制到输出目录**”设置为“**始终复制**”。
   
    ![][1]
3. 在 **Program.cs** 文件的顶部添加以下语句：
   
        using System.IO;
4. 将以下方法添加到 **Program** 类：
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    `UploadToBlobAsync` 方法获取要上载的文件的文件名与流源，然后处理上载到存储的任务。 控制台应用会显示上传文件所需的时间。
5. 在 **Main** 方法中的 `Console.ReadLine()` 行前面添加以下方法：
   
        SendToBlobAsync();

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 
> 

## <a name="receive-a-file-upload-notification"></a>接收文件上载通知
在本部分中，会编写一个 .NET 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 在当前的 Visual Studio 解决方案中，通过使用“**控制台应用程序**”项目模板创建 Visual C# Windows 项目。 将项目命名为 **ReadFileUploadNotification**。
   
    ![Visual Studio 中的新项目][2]
2. 在“解决方案资源管理器”中，右键单击 **ReadFileUploadNotification** 项目，然后单击“管理 NuGet 包...”。
       
3. 在“NuGet 包管理器”窗口中，搜索“Microsoft.Azure.Devices”，然后单击“安装”并接受使用条款。 
   
    此操作将会下载、安装 [Azure IoT 服务 SDK NuGet 包]，并在 **ReadFileUploadNotification** 项目中添加对它的引用。

4. 在 **Program.cs** 文件的顶部添加以下语句：
   
        using Microsoft.Azure.Devices;
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在 [IoT 中心入门]中获取的 IoT 中心连接字符串：
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. 将以下方法添加到 **Program** 类：
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    请注意，此接收模式与用于从设备应用接收云到设备消息的模式相同。
7. 最后，在 **Main** 方法中添加以下行：
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 在 Visual Studio 中，右键单击你的解决方案并选择“**设置启动项目**”。 选择“**多个启动项目**”，然后针对 **ReadFileUploadNotification** 和 **SimulatedDevice** 应用选择“**启动**”操作。
2. 按 **F5**。 这两个应用程序应该都会启动。 你将在其中一个控制台应用中看到上载已完成，同时还会看到另一个控制台应用收到的上载通知消息。 可使用 [Azure 门户]或 Visual Studio 服务器资源管理器检查 Azure 存储帐户中是否存在上传的文件。
   
   ![][50]

## <a name="next-steps"></a>后续步骤
在本教程中，你已学习了如何使用 IoT 中心的文件上载功能来简化从设备进行的文件上载。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心][lnk-create-hub]
* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->

[Azure 门户]: https://portal.azure.com/

[Azure 数据工厂]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[使用 IoT 中心发送云到设备消息]: iot-hub-csharp-csharp-c2d.md
[处理设备到云的消息]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中心入门]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT 开发人员中心]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 存储]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT 服务 SDK NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-windows-gateway-sdk-simulated-device.md



