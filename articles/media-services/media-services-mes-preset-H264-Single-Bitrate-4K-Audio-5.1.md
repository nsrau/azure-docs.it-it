---
title: Codec video H.264 a bitrate singolo con risoluzione 4K e audio 5.1 | Documentazione Microsoft
description: "Questo argomento offre una panoramica del set di impostazioni di attività **Codec video H.264 a bitrate singolo con risoluzione 4K e audio 5.1**."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 55bfd5b283e6159e0a06b3107de31d1722796bf1
ms.openlocfilehash: 9f145fb942ce213b2c59c63b8279edda84a9ebd8
ms.contentlocale: it-it
ms.lasthandoff: 11/29/2016

---

# <a name="h264-single-bitrate-4k-audio-51"></a>Codec video H.264 a bitrate singolo con risoluzione 4K e audio 5.1
`Media Encoder Standard` definisce un set di impostazioni di codifica che è possibile usare per la creazione di processi di codifica. È possibile usare un `preset name` per specificare il formato in cui codificare il file multimediale oppure creare set di impostazioni basati su JSON o XML personalizzati, con codifica UTF-8 o UTF-16. Dopodiché, occorre trasmettere il set di impostazioni personalizzato al codificatore. Per un elenco di tutti i nomi dei set di impostazioni supportati dal codificatore `Media Encoder Standard`, vedere [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Set di impostazioni di attività per Media Encoder Standard).  
  
 Questo argomento illustra il set di impostazioni `H264 Single Bitrate 4K Audio 5.1` nei formati XML e JSON.  
  
 Il set di impostazioni genera un unico file MP4 con una velocità in bit di 18.000 kbps e audio AAC 5.1. Per informazioni dettagliate su profilo, velocità in bit, frequenza di campionamento e così via di questo set di impostazioni, esaminare il codice XML o JSON definito di seguito. Per informazioni sul significato di ogni elemento e sui valori possibili per ciascuno, vedere lo [schema di Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  È necessario ottenere il tipo di unità riservata Premium con codifica 4K. Per altre informazioni, vedere [Come scalare la codifica](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```

