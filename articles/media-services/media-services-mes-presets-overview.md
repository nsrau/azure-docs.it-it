---
title: "Set di impostazioni delle attività MES (Media Encoder Standard) | Microsoft Docs"
description: Questo argomento fornisce una panoramica dei set di impostazioni per MES (Media Encoder Standard).
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: e9018ffa74c2895560adbd4c0d2b83ee67e9eb1c
ms.contentlocale: it-it
ms.lasthandoff: 03/03/2017


---

# <a name="task-presets-for-mes-media-encoder-standard"></a>Set di impostazioni delle attività MES (Media Encoder Standard)

**Media Encoder Standard** definisce un set di impostazioni di codifica che è possibile usare per la creazione di processi di codifica. Si consiglia di usare il set di impostazioni di "Flusso adattivo" se si vuole codificare un video per lo streaming con Servizi multimediali. Quando si specifica il set di impostazioni Media Encoder Standard [genera automaticamente un bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md). 

Tuttavia, se è necessario personalizzare un set di impostazioni di codifica, è necessario prendere uno dei set di impostazioni di codifica definiti in questa sezione come modello per la configurazione personalizzata. Per informazioni sul significato di ogni elemento in questi set di impostazioni e sui valori possibili per ciascuno, vedere lo [schema di Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Quando si usa un set di impostazioni per codifiche 4K, si dovrebbe ottenere il tipo di unità riservata `S3`. Per altre informazioni, vedere [Come scalare la codifica](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
Quando si usa Media Encoder Standard, la rotazione è abilitata per impostazione predefinita. Se il video è stato registrato con uno smartphone o un altro dispositivo mobile in modalità verticale, per impostazione predefinita prima di codificarlo i set di impostazioni lo ruoteranno in modalità orizzontale (a differenza di quando si usa Azure Media Encoder, in cui la rotazione del video deve essere eseguita manualmente, come documentato in [questo](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/) blog, nella sezione sulla rotazione del video).  
  
Set di impostazioni disponibili:  
  
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

