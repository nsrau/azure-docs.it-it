---
title: Schema dei metadati di input di servizi multimediali di Azure V3
description: Questo articolo fornisce una panoramica dello schema dei metadati di input di servizi multimediali di Azure V3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1ca526b7ecbe20a54ec115521cdfbc93c713e0da
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360055"
---
# <a name="input-metadata"></a>Metadati di input

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Un processo di codifica è associato uno (o più) asset di input in cui si desidera eseguire alcune attività di codifica.  Al termine di un'attività, viene generato un asset di output. L'asset di output contiene video, audio, anteprime, manifesti e altri file. 

L'asset di output include anche un file contenente i metadati dell'asset di input. Il nome del file JSON dei metadati ha un ID casuale, non usarlo per identificare l'asset di input a cui appartiene l'asset di output. Per identificare l'asset di input a cui appartiene, usare il `Uri` campo (per altre informazioni, vedere [altri elementi figlio](#other-child-elements)).  

Servizi multimediali non analizza preventivamente gli asset di input per generare i metadati. I metadati di input vengono generati solo come artefatti quando un asset di input viene elaborato in un processo. Questo elemento viene quindi scritto nell'asset di output. Per generare metadati per gli asset di input e gli asset di output vengono usati strumenti diversi. Di conseguenza, i metadati di input hanno uno schema leggermente diverso rispetto ai metadati di output.

Questo articolo illustra gli elementi e i tipi dello schema JSON su cui si basa l'input metadati ( &lt; asset_id &gt;_metadata.json). Per informazioni sul file contenente i metadati sull'asset di output, vedere [metadati di output](output-metadata-schema.md).  

È possibile trovare l'esempio di schema JSON alla fine di questo articolo.  

## <a name="assetfile"></a>AssetFile  

Contiene una raccolta di elementi AssetFile per il processo di codifica.  

> [!NOTE]
> I quattro elementi figlio seguenti devono comparire in una sequenza.  
> 
> 

| Nome  | Descrizione |
| --- | --- | 
| **VideoTracks**|Ogni file di asset fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. Per ulteriori informazioni, vedere [VideoTracks](#videotracks). |
| **AudioTrack**|Ogni file di asset fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. Per ulteriori informazioni, vedere [AudioTracks](#audiotracks) |
| **Metadata**  |Metadati del file di asset rappresentati come stringhe chiave-valore. <br />ad esempio `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Altri elementi figlio

| Nome | Descrizione |
| --- | --- |
| **Nome**<br />Necessario |Nome del file di asset <br /><br />Esempio: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />Necessario |URL in cui si trova l'asset di input. Per identificare l'asset di input a cui appartiene l'asset di output, usare il `Uri` campo anziché ID.|
| **Size**<br />Necessario |Dimensioni del file di asset in byte.  <br /><br />Esempio: `"Size": 75739259`|
| **Duration**<br />Necessario |Durata della riproduzione del contenuto. <br /><br />Esempio: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Necessario |Numero di flussi nel file di asset.  <br /><br />Esempio: `"NumberOfStreams": 2`|
| **FormatNames**<br />Necessario |Nomi del formato.  <br /><br />Esempio: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Necessario |Nomi dettagliati del formato. <br /><br />Esempio: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Ora di inizio del contenuto.  <br /><br />Esempio: `"StartTime": "PT0S"` |
| **OverallBitRate** |Velocità in bit media del file di asset in bit al secondo.  <br /><br />Esempio: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Nome | Descrizione |
| --- | --- |
| **FourCC**<br />Necessario |Codec video FourCC codice riportato da ffmpeg.<br /><br />Esempio: `"FourCC": "avc1"` |
| **Profilo** |Profilo della traccia video. <br /><br />Esempio: `"Profile": "Main"`|
| **Level** |Livello della traccia video. <br /><br />Esempio: `"Level": "3.2"`|
| **PixelFormat** |Formato pixel della traccia video. <br /><br />Esempio: `"PixelFormat": "yuv420p"`|
| **Larghezza**<br />Necessario |Larghezza del video codificata in pixel. <br /><br />Esempio: `"Width": "1280"`|
| **Altezza**<br />Necessario |Altezza del video codificata in pixel.<br /><br />Esempio: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Necessario |Numeratore delle proporzioni della visualizzazione video.<br /><br />Esempio: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Necessario |Denominatore delle proporzioni della visualizzazione video. <br /><br />Esempio: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Numeratore delle proporzioni del campione video. <br /><br />Esempio: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Esempio: `"SampleAspectRatioDenominator": 1.0`|
| **FrameRate**<br />Necessario |Frequenza dei frame misurata in formato .3F. <br /><br />Esempio: `"FrameRate": 29.970`|
| **Bitrate** |Velocità in bit video media in bit al secondo, calcolata dal file di asset. Viene contato solo il payload del flusso elementare, mentre l'overhead di creazione dei pacchetti è escluso. <br /><br />Esempio: `"Bitrate": 8421583`|
| **HasBFrames** |Numero di traccia video dei fotogrammi B. <br /><br />Esempio: `"HasBFrames": 2`|
| **Metadata** |Stringhe chiave-valore generiche che possono essere usate per contenere una varietà di informazioni. <br />Vedere l'esempio completo alla fine dell'articolo. |
| **Id**<br />Necessario |Indice in base zero della traccia audio o video.<br /><br /> Questo **Id** non corrisponde necessariamente al TrackID usato in un file MP4. <br /><br />Esempio: `"Id": 2`|
| **Codec** |Stringa del codec della traccia video. <br /><br />Esempio: `"Codec": "h264"`|
| **CodecLongName** |Nome lungo del codec della traccia audio o video. <br /><br />Esempio: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |Stringa del codec della traccia video. <br /><br />Esempio: `"Codec": "h264"`|
| **TimeBase**<br />Necessario |Tempo base.<br /><br />Esempio: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Numero di frame (presenti per le tracce video). <br /><br />Esempio: `"NumberOfFrames": 2107`|
| **StartTime** |Ora di inizio della traccia.<br /><br />Esempio: `"StartTime": "PT0.033S"` |
| **Duration** |Durata della traccia. <br /><br />Esempio: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTrack

| Nome  | Descrizione |
| --- | --- | 
| **SampleFormat** |Formato del campione. <br /><br />Esempio: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Layout del canale. <br /><br />Esempio: `"ChannelLayout": "stereo"`|
| **Canali**<br />Necessario |Numero di canali audio (da 0 in su). <br /><br />Esempio: `"Channels": 2`|
| **SamplingRate**<br />Necessario |Frequenza di campionamento dell'audio in campioni/sec o Hz. <br /><br />Esempio: `"SamplingRate": 48000`|
| **Bitrate** |Velocità media in bit audio in bit al secondo, calcolata in base al file di asset. Viene contato solo il payload del flusso elementare, mentre l'overhead di creazione dei pacchetti è escluso dal conteggio. <br /><br />Esempio: `"Bitrate": 192080`|
| **Metadata** |Stringhe chiave-valore generiche che possono essere usate per contenere una varietà di informazioni.  <br />Vedere l'esempio completo alla fine dell'articolo. |
| **Id**<br />Necessario |Indice in base zero della traccia audio o video.<br /><br /> Non corrisponde necessariamente al TrackID usato in un file MP4. <br /><br />Esempio: `"Id": 1`|
| **Codec** |Stringa del codec della traccia video. <br /><br />Esempio: `"Codec": "aac"`|
| **CodecLongName** |Nome lungo del codec della traccia audio o video. <br /><br />Esempio: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />Necessario |Tempo base.<br /><br />Esempio: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Numero di frame (presenti per le tracce video). <br /><br />Esempio: `"NumberOfFrames": 3294`|
| **StartTime** |Ora di inizio della traccia. Per ulteriori informazioni, vedere [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Esempio: `"StartTime": "PT0S"` |
| **Duration** |Durata della traccia. <br /><br />Esempio: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadati

| Nome | Descrizione |
| --- | --- |
| **key**<br />Necessario |Chiave nella coppia chiave/valore. |
| **value**<br /> Necessario |Valore nella coppia chiave/valore. |

## <a name="schema-example"></a>Esempio di schema

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

[Metadati di output](output-metadata-schema.md)
