---
title: "Set di impostazioni di attività per Media Encoder Standard (MES) | Microsoft Docs"
description: Questo argomento offre una panoramica dei set di impostazioni di esempio definiti dal servizio per Media Encoder Standard (MES).
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 5753b1dffe5a1a4ee069b83f58e9c2dac433b89d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Set di impostazioni di esempio per Media Encoder Standard (MES)

**Media Encoder Standard** definisce un set di impostazioni di codifica di sistema predefinite che è possibile usare per la creazione di processi di codifica. Si consiglia di usare il set di impostazioni di "Flusso adattivo" se si vuole codificare un video per lo streaming con Servizi multimediali. Quando si specifica il set di impostazioni Media Encoder Standard [genera automaticamente un bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Creazione di set di impostazioni personalizzati da esempi
Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Se è necessario personalizzare un set di impostazioni di codifica, iniziare con uno dei set di impostazioni di sistema seguenti, forniti in questa sezione come modello per la configurazione personalizzata. Per informazioni sul significato di ogni elemento in questi set di impostazioni e sui valori possibili per ciascuno, vedere lo [schema di Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Quando si usa un set di impostazioni per codifiche 4K, si dovrebbe ottenere il tipo di unità riservata `S3`. Per altre informazioni, vedere [Come scalare la codifica](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Impostazione predefinita di rotazione del video nei set di impostazioni:
Quando si usa Media Encoder Standard, la rotazione del video è abilitata per impostazione predefinita. Se il video è stato registrato in un dispositivo mobile in modalità verticale, questi set di impostazioni lo ruotano in modalità orizzontale prima della codifica.
 
## <a name="available-presets"></a>Set di impostazioni disponibili: 

 [Codec video H.264 a bitrate multiplo con risoluzione 1080p e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) genera un set di 8 file MP4 con audio AAC 5.1 e allineamento GOP, con velocità compresa fra 6.000 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 6.000 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 16x9 per iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 8.500 e 200 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 16x9 SD e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) genera un set di 5 file MP4 con audio AAC 5.1 e allineamento GOP, con velocità compresa fra 1.900 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) genera un set di 5 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 1.900 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 4K e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) genera un set di 12 file MP4 con audio AAC 5.1 e allineamento GOP, con velocità compresa fra 20.000 e 1.000 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) genera un set di 12 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 20.000 e 1.000 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 4x3 per iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 8500 e 200 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 4x3 SD e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) genera un set di 5 file MP4 con audio AAC 5.1 e allineamento GOP, con velocità compresa fra 1.600 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) genera un set di 5 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 1.600 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 720p e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) genera un set di 6 file MP4 con audio AAC 5.1 e allineamento GOP, con velocità compresa fra 3.400 e 400 kbps.  
  
 [Codec video H.264 a bitrate multiplo con risoluzione 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) genera un set di 6 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 3.400 e 400 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 1080p e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) genera un file MP4 con audio AAC 5.1 e una velocità in bit di 6.750 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 6.750 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 4K e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) genera un file MP4 con audio AAC 5.1 e una velocità in bit di 18.000 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 18.000 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 4x3 SD e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) genera un file MP4 con audio AAC 5.1 e una velocità in bit di 1800 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 1800 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 16x9 SD e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) genera un file MP4 con audio AAC 5.1 e una velocità in bit di 2.200 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 2.200 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 720p e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) genera un file MP4 con audio AAC 5.1 e una velocità in bit di 4.500 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 720p per Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) genera un file MP4 con audio AAC 5.1 stereo e una velocità in bit di 2.000 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 4.500 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione SD di alta qualità per Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 500 kbps.  
  
 [Codec video H.264 a bitrate singolo con risoluzione SD di bassa qualità per Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) genera un file MP4 con audio AAC stereo e una velocità in bit di 56 kbps.  
  
 Per altre informazioni sui codificatori di servizi multimediali, vedere [Codifica su richiesta con i servizi multimediali di Azure](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).
