---
title: "Media Encoder Standard 架构 | Microsoft Docs"
description: "本主题概述 Media Encoder Standard 架构。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: a526610f5b09ce73a9c192ec45ae8aafab001401


---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard 架构
本主题描述 [Media Encoder Standard 预设](media-services-mes-presets-overview.md)基于的 XML 架构的一些元素和类型。 本主题说明元素及其有效值。 完整架构将在以后发布。  

## <a name="a-namepreseta-preset-root-element"></a><a name="Preset"></a> 预设（根元素）
定义编码预设。  

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **编码** |[编码](media-services-mes-schema.md#Encoding) |根元素，指示要进行编码的输入源。 |
| **输出** |[输出](media-services-mes-schema.md#Output) |所需输出文件的集合。 |

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **版本**<br/><br/> 必选 |**xs:decimal** |预设版本。 以下限制适用：xs:fractionDigits 值 =“1”和 xs:minInclusive 值 =“1”，例如，**版本 =“1.0”**。 |

## <a name="a-nameencodinga-encoding"></a><a name="Encoding"></a> 编码
包含以下元素序列。  

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |视频的 H.264 编码设置。 |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |音频的 AAC 编码设置。 |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp 图像设置。 |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png 图像设置。 |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg 图像设置。 |

## <a name="a-nameh264videoa-h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs=“0” |**xs: boolean** |目前，仅支持单步编码。 |
| **KeyFrameInterval**<br/><br/> minOccurs=“0”<br/><br/> **默认值 =“00:00:02”** |**xs: time** |确定 IDR 帧之间的（默认）间距。 |
| **SceneChangeDetection**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“false” |**xs: boolean** |如果设置为 true，编码器尝试在视频中检测场景更改，并插入 IDR 帧。 |
| **复杂性**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“平衡” |**xs:string** |控制编码速度和视频质量之间的平衡。 可能是以下值之一：**速度****平衡**或**质量**<br/><br/> 默认值：**平衡** |
| **SyncMode**<br/><br/> minOccurs=“0” | |将在未来版本中公开功能。 |
| **H264Layers**<br/><br/> minOccurs=“0” |[H264Layers](media-services-mes-schema.md#H264Layers) |输出视频层的集合。 |

## <a name="a-nameh264layersa-h264layers"></a><a name="H264Layers"></a> H264Layers
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs=“0” maxOccurs =“unbounded” |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 层的集合。 |

## <a name="a-nameh264layera-h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> 视频限制基于 [H264 级别](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)表中描述的值。  
> 
> 

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **配置文件**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“自动” |**xs:string** |可能是以下 **xs:string** 值之一：**自动**、**基线**、**主要**、**高**。 |
| **级别**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“自动” |**xs:string** | |
| **Bitrate**<br/><br/> minOccurs=“0” |**xs:int** |此视频层使用的比特率，以 kbps 为单位指定。 |
| **MaxBitrate**<br/><br/> minOccurs=“0” |**xs:int** |此视频层使用的最大比特率，以 kbps 为单位指定。 |
| **BufferWindow**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“00:00:05” |**xs: time** |视频缓冲区的长度。 |
| **宽度**<br/><br/> minOccurs=“0” |**xs:int** |输出视频帧的宽度，以像素为单位。<br/><br/> 请注意，目前必须指定“宽度”和“高度”。 “宽度”和“高度”需为偶数。 |
| **高度**<br/><br/> minOccurs=“0” |**xs:int** |输出视频帧的高度，以像素为单位。<br/><br/> 请注意，目前必须指定“宽度”和“高度”。 “宽度”和“高度”需为偶数。|
| **BFrames**<br/><br/> minOccurs=“0” |**xs:int** |参考帧之间的 B 帧数。 |
| **ReferenceFrames**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“3” |**xs:int** |GOP 中的参考帧数。 |
| **EntropyMode**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“Cabac” |**xs:string** |可能是以下值之一：**Cabac** 和 **Cavlc**。 |
| **FrameRate**<br/><br/> minOccurs=“0” |有理数 |确定输出视频的帧速率。 使用默认值“0/1”，允许编码器使用与输入视频相同的帧速率。 允许的值应为常规视频帧速率，如下所示。 但是，允许使用任何有效有理数。 例如 1/1 表示 1 fps 且有效。<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29.97 fps) |
| **AdaptiveBFrame**<br/><br/> minOccurs=“0” |**xs: boolean** |从 Azure 媒体编码器复制 |
| **切片**<br/><br/> minOccurs=“0”<br/><br/> 默认值 =“0” |**xs:int** |确定将帧切分成多少片。 建议使用默认值。 |

## <a name="a-nameaacaudioa-aacaudio"></a><a name="AACAudio"></a> AACAudio
 包含以下元素和组的序列。  

 有关 AAC 的详细信息，请参阅 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)。  

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **配置文件**<br/><br/> minOccurs="0"<br/><br/> 默认值 =“AACLC” |**xs:string** |可能是以下值之一：**AACLC**、**HEAACV1** 或 **HEAACV2**。 |

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **条件** |**xs:string** |若要强制编码器在输入不包含音频时生成包含静音曲目的资产，请指定“InsertSilenceIfNoAudio”值。<br/><br/> 默认情况下，如果你要向编码器发送仅包含视频而不包含音频的输入，输出资产将包含仅有视频数据的文件。 某些播放器可能无法处理此类输出流。 对于这种方案，你可以使用此设置来强制编码器将静音曲目添加到输出。 |

### <a name="groups"></a>组
| 引用 | 说明 |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs=“0” |请参阅 [ AudioGroup](media-services-mes-schema.md#AudioGroup) 的说明，了解可为每个配置文件设置的适当通道数、采样率和比特率。 |

## <a name="a-nameaudiogroupa-audiogroup"></a><a name="AudioGroup"></a> AudioGroup
有关每个配置文件的有效值的详细信息，请参阅后面的“音频编解码器详细信息”表。  

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **通道**<br/><br/> minOccurs=“0” |**xs:int** |音频通道数。 以下是有效选项：1、2、5、6、8。<br/><br/> 默认值：2。 |
| **SamplingRate**<br/><br/> minOccurs=“0” |**xs:int** |音频采样率，以 Hz 为单位。 |
| **Bitrate**<br/><br/> minOccurs=“0” |**xs:int** |对音频进行编码时使用的比特率，以 kbps 为单位指定。 |

### <a name="audio-codec-details"></a>音频编解码器详细信息
音频编解码器|详细信息  
-----------------|---  
**AACLC**|1:<br/><br/> - 11025 : 8 &lt;= 比特率 &lt; 16<br/><br/> - 12000 : 8 &lt;= 比特率 &lt; 16<br/><br/> - 16000 : 8 &lt;= 比特率 &lt;32<br/><br/>- 22050 : 24 &lt;= 比特率 &lt; 32<br/><br/> - 24000 : 24 &lt;= 比特率 &lt; 32<br/><br/> - 32000 : 32 &lt;= 比特率 &lt;= 192<br/><br/> - 44100 : 56 &lt;= 比特率 &lt;= 288<br/><br/> - 48000 : 56 &lt;= 比特率 &lt;= 288<br/><br/> - 88200 : 128 &lt;= 比特率 &lt;= 288<br/><br/> - 96000 : 128 &lt;= 比特率 &lt;= 288<br/><br/> 2:<br/><br/> - 11025 : 16 &lt;= 比特率 &lt; 24<br/><br/> - 12000 : 16 &lt;= 比特率 &lt; 24<br/><br/> - 16000 : 16 &lt;= 比特率 &lt; 40<br/><br/> - 22050 : 32 &lt;= 比特率 &lt; 40<br/><br/> - 24000 : 32 &lt;= 比特率 &lt; 40<br/><br/> - 32000 :  40 &lt;= 比特率 &lt;= 384<br/><br/> - 44100 : 96 &lt;= 比特率 &lt;= 576<br/><br/> - 48000 : 96 &lt;= 比特率 &lt;= 576<br/><br/> - 88200 : 256 &lt;= 比特率 &lt;= 576<br/><br/> - 96000 : 256 &lt;= 比特率 &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000 : 160 &lt;= 比特率 &lt;= 896<br/><br/> - 44100 : 240 &lt;= 比特率 &lt;= 1024<br/><br/> - 48000 : 240 &lt;= 比特率 &lt;= 1024<br/><br/> - 88200 : 640 &lt;= 比特率 &lt;= 1024<br/><br/> - 96000 : 640 &lt;= 比特率 &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= 比特率 &lt;= 1024<br/><br/> - 44100 : 384 &lt;= 比特率 &lt;= 1024<br/><br/> - 48000 : 384 &lt;= 比特率 &lt;= 1024<br/><br/> - 88200 : 896 &lt;= 比特率 &lt;= 1024<br/><br/> - 96000 : 896 &lt;= 比特率 &lt;= 1024  
**HEAACV1**|1:<br/><br/> - 22050 : 比特率 = 8<br/><br/> - 24000 : 8 &lt;= 比特率 &lt;= 10<br/><br/> - 32000 : 12 &lt;= 比特率 &lt;= 64<br/><br/> - 44100 : 20 &lt;= 比特率 &lt;= 64<br/><br/> - 48000 : 20 &lt;= 比特率 &lt;= 64<br/><br/> - 88200 : 比特率 = 64<br/><br/> 2:<br/><br/> - 32000 : 16 &lt;= 比特率 &lt;= 128<br/><br/> - 44100 : 16 &lt;= 比特率 &lt;= 128<br/><br/> - 48000 : 16 &lt;= 比特率 &lt;= 128<br/><br/> - 88200 : 96 &lt;= 比特率 &lt;= 128<br/><br/> - 96000 : 96 &lt;= 比特率 &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= 比特率 &lt;= 320<br/><br/> - 44100 : 64 &lt;= 比特率 &lt;= 320<br/><br/> - 48000 : 64 &lt;= 比特率 &lt;= 320<br/><br/> - 88200 : 256 &lt;= 比特率 &lt;= 320<br/><br/> - 96000 : 256 &lt;= 比特率 &lt;= 320<br/><br/> 8:<br/><br/> - 32000 : 96 &lt;= 比特率 &lt;= 448<br/><br/> - 44100 : 96 &lt;= 比特率 &lt;= 448<br/><br/> - 48000 : 96 &lt;= 比特率 &lt;= 448<br/><br/> - 88200 : 384 &lt;= 比特率 &lt;= 448<br/><br/> - 96000 : 384 &lt;= 比特率 &lt;= 448  
**HEAACV2**|2:<br/><br/> - 22050 : 8 &lt;= 比特率 &lt;= 10<br/><br/> - 24000 : 8 &lt;= 比特率 &lt;= 10<br/><br/> - 32000 : 12 &lt;= 比特率 &lt;= 64<br/><br/> - 44100 : 20 &lt;= 比特率 &lt;= 64<br/><br/> - 48000 : 20 &lt;= 比特率 &lt;= 64<br/><br/> - 88200 : 64 &lt;= 比特率 &lt;= 64  
  

## <a name="a-nameclipa-clip"></a><a name="Clip"></a> 剪辑
### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **StartTime** |**xs:duration** |指定演示文稿的开始时间。 StartTime 的值需与输入视频的绝对时间戳匹配。 例如，如果输入视频第一帧的时间戳为 12:00:10.000，则 StartTime 应大于或等于 12:00:10.000。 |
| **持续时间** |**xs:duration** |指定演示文稿的持续时间（例如，视频中覆盖的外观）。 |

## <a name="a-nameoutputa-output"></a><a name="Output"></a> 输出
### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **FileName** |**xs:string** |输出文件的名称。<br/><br/> 可以使用下表中描述的宏来生成输出文件名。 例如：<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>宏
| 宏 | 说明 |
| --- | --- |
| **{Basename}** |如果正在进行 VoD 编码，则 {Basename} 是输入资产中主文件的 AssetFile.Name 属性的前 32 个字符。<br/><br/> 如果输入资产是实时存档，则 {Basename} 从服务器清单中的 trackName 属性派生。 如果使用 TopBitrate 提交子剪辑作业，如：“<VideoStream\>TopBitrate</VideoStream\>”，并且输出文件包含视频，则 {Basename} 是具有最高比特率视频层的 trackName 的前32个字符。<br/><br/> 如果改为使用所有输入比特率提交子剪辑作业，例如“<VideoStream\>*</VideoStream\>”，并且输出文件包含视频，则 {Basename} 是对应视频层的 trackName 的前32个字符。 |
| **{Codec}** |到视频的“H264”以及音频的“AAC”的映射。 |
| **{Bitrate}** |如果输出文件包含视频和音频，则为目标视频比特率；如果输出文件仅包含音频，则为目标音频比特率。 使用的值是以 kbps 为单位的比特率。 |
| **{Channel}** |如果文件包含音频，则为音频通道计数。 |
| **{Width}** |如果文件包含视频，则为输出文件中视频的宽度（以像素为单位）。 |
| **{Height}** |如果文件包含视频，则为输出文件中视频的高度（以像素为单位）。 |
| **{Extension}** |继承自输出文件的“类型”属性。 输出文件名将具有以下扩展名之一：“mp4”、“ts”、“jpg”、“png”或“bmp”。 |
| **{Index}** |对于缩略图是必需的。 只应存在一次。 |

## <a name="a-namevideoa-video-complex-type-inherits-from-codec"></a><a name="Video"></a> 视频（继承自编解码器的复杂类型）
### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **启动** |**xs:string** | |
| **步骤** |**xs:string** | |
| **范围** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs: boolean** |有关详细说明，请参阅以下部分：[PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="a-namepreserveresolutionafterrotationa-preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
建议使用 PreserveResolutionAfterRotation 标志结合以百分比表示的分辨率值（宽度 =“100％”，高度 =“100％”）。  

默认情况下，Media Encoder Standard (MES) 预设中的编码分辨率设置（宽度、高度）针对 0 度旋转的视频。 例如，如果输入视频为 1280x720，且为零度旋转，则默认预设确保输出具有相同的分辨率。 请参阅下图。  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

但是，这意味着如果输入视频是以非零旋转捕获（例如， 垂直持握的智能手机或平板电脑），则默认情况下，MES 将对输入视频应用编码分辨率设置（宽度、高度），然后补偿旋转。 有关示例，请参阅下图。 预设使用宽度 =“100％”，高度 =“100％”，MES 将此理解为所需输出为 1280 像素宽和 720 像素高。 旋转视频后，它会缩小图片适应窗口，左右两侧会出现黑边区域。  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

如果不想采用上述行为，则可以使用 PreserveResolutionAfterRotation 标志并将其设置为“true”（默认值为“false”）。 因此，如果预设为宽度 =“100％”，高度 =“100％”，并将 PreserveResolutionAfterRotation 设置为“true”，则宽为 1280 像素、高为 720 像素，且 90 度旋转的输入视频将生成 0 度旋转、但宽为 720 像素、高为 1280 像素的输出视频。 请参阅下图。  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="a-nameformatgroupa-formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup（组）
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="a-namebmplayera-bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **宽度**<br/><br/> minOccurs=“0” |**xs:int** | |
| **高度**<br/><br/> minOccurs=“0” |**xs:int** | |

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **条件** |**xs:string** | |

## <a name="a-namepnglayera-pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **宽度**<br/><br/> minOccurs=“0” |**xs:int** | |
| **高度**<br/><br/> minOccurs=“0” |**xs:int** | |

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **条件** |**xs:string** | |

## <a name="a-namejpglayera-jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **宽度**<br/><br/> minOccurs=“0” |**xs:int** | |
| **高度**<br/><br/> minOccurs=“0” |**xs:int** | |
| **质量**<br/><br/> minOccurs=“0” |**xs:int** |有效值：1（最差）- 100（最好） |

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **条件** |**xs:string** | |

## <a name="a-namepnglayersa-pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs=“0” maxOccurs =“unbounded” |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="a-namebmplayersa-bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs=“0” maxOccurs =“unbounded” |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="a-namejpglayersa-jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs=“0” maxOccurs =“unbounded” |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="a-namebmpimagea-bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage（继承自视频的复杂类型）
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs=“0” |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="a-namejpgimagea-jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage（继承自视频的复杂类型）
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs=“0” |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="a-namepngimagea-pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage（继承自视频的复杂类型）
### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs=“0” |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="examples"></a>示例
查看根据此架构生成的 XML 预设示例，请参阅 [MES (Media Encoder Standard) 的任务预设](media-services-mes-presets-overview.md)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO3-->

