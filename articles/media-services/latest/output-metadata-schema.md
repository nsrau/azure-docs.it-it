---
title: Schema dei metadati di output dei Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dello schema dei metadati di output di servizi multimediali di Azure V3.
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
ms.openlocfilehash: aa6d4edc4348fa850eeb7e8d91ce0791ee4c7170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336412"
---
# <a name="output-metadata"></a>Metadati di output

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Un processo di codifica è associato uno (o più) asset di input in cui si desidera eseguire alcune attività di codifica. Ad esempio, codificare un file MP4 in set di velocità in bit adattivi MP4 H.264; creare un'anteprima; creare le sovrimpressioni. Al termine di un'attività, viene generato un asset di output.  L'asset di output contiene video, audio, anteprime e altri file. L'asset di output include anche un file contenente i metadati dell'asset di output. Il nome del file JSON dei metadati ha il formato seguente: `<source_file_name>_manifest.json` (ad esempio, `BigBuckBunny_manifest.json` ). Per trovare il nome del file di origine (senza troncamento), è necessario eseguire una ricerca di * _metadata.jsed eseguire una query sulla stringa FilePath all'interno di.

Servizi multimediali non analizza preventivamente gli asset di input per generare i metadati. I metadati di input vengono generati solo come artefatti quando un asset di input viene elaborato in un processo. Questo elemento viene quindi scritto nell'asset di output. Per generare metadati per gli asset di input e gli asset di output vengono usati strumenti diversi. Di conseguenza, i metadati di input hanno uno schema leggermente diverso rispetto ai metadati di output.

Questo articolo illustra gli elementi e i tipi dello schema JSON su cui si basano i metadati di output ( &lt; source_file_name &gt;_manifest.js). <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

È possibile trovare il codice completo dello schema e l'esempio JSON alla fine di questo articolo.  

## <a name="assetfile"></a>AssetFile

Raccolta di elementi AssetFile per il processo di codifica.  

| Nome | Description |
| --- | --- |
| **recenti** |Raccolta di file multimediali di input/origine elaborata per produrre questo AssetFile.<br />Esempio: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Ogni elemento AssetFile fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. <br />Vedere [VideoTracks](#videotracks). |
| **AudioTrack**|Ogni elemento AssetFile fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. Questa è la raccolta di tutte queste tracce audio.<br /> Per ulteriori informazioni, vedere [AudioTracks](#audiotracks). |
| **Nome**<br />Obbligatoria |Il nome dell'asset di file multimediale. <br /><br />Esempio: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Dimensione**<br />Obbligatoria |Dimensioni del file di asset in byte. <br /><br />Esempio: `"Size": 32414631`|
| **Duration**<br />Obbligatoria |Durata della riproduzione del contenuto. Per ulteriori informazioni, vedere il formato [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) . <br /><br />Esempio: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Ogni elemento AssetFile fisico può contenere da zero a più tracce video con interfoliazione in un formato contenitore appropriato. L'elemento **VideoTracks** rappresenta una raccolta di tutte le tracce video.  

| Nome | Description |
| --- | --- |
| **Id**<br /> Obbligatoria |Indice in base zero della traccia video. **Nota:**  Questo **ID** non corrisponde necessariamente al TrackID usato in un file MP4. <br /><br />Esempio: `"Id": 1`|
| **FourCC**<br />Obbligatoria | Codec video FourCC codice riportato da ffmpeg.  <br /><br />Esempio: `"FourCC": "avc1"`|
| **Profilo** |Profilo H264 (applicabile solo al codec H264).  <br /><br />Esempio: `"Profile": "High"` |
| **Level** |Livello H264 (applicabile solo al codec H264).  <br /><br />Esempio: `"Level": "3.2"`|
| **Larghezza**<br />Obbligatoria |Larghezza del video codificata in pixel.  <br /><br />Esempio: `"Width": "1280"`|
| **Altezza**<br />Obbligatoria |Altezza del video codificata in pixel.  <br /><br />Esempio: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Obbligatoria|Numeratore delle proporzioni della visualizzazione video.  <br /><br />Esempio: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Obbligatoria |Denominatore delle proporzioni della visualizzazione video.  <br /><br />Esempio: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />Obbligatoria |Frequenza dei frame misurata in formato .3F.  <br /><br />Esempio: `"Framerate": 29.970`|
| **Bitrate**<br />Obbligatoria |Velocità in bit video media in bit al secondo, calcolata da AssetFile. Conta solo il payload del flusso elementare e non include il sovraccarico dovuto alla creazione di pacchetti.  <br /><br />Esempio: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Obbligatoria |Velocità in bit media di destinazione per questa traccia video, come richiesto tramite il set di impostazioni di codifica, in bit al secondo. <br /><br />Esempio: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTrack 

Ogni elemento AssetFile fisico può contenere da zero a più tracce audio con interfoliazione in un formato contenitore appropriato. L'elemento **AudioTracks** rappresenta una raccolta di tutte le tracce audio.  

| Nome  | Description |
| --- | --- |
| **Id**<br />Obbligatoria  |Indice in base zero della traccia audio. **Nota:**  Questo non è necessariamente il TrackID usato in un file MP4.  <br /><br />Esempio: `"Id": 2`|
| **Codec**  |Stringa del codec della traccia audio.  <br /><br />Esempio: `"Codec": "aac"`|
| **Lingua**|Esempio: `"Language": "eng"`|
| **Canali**<br />Obbligatoria|Numero dei canali audio.  <br /><br />Esempio: `"Channels": 2`|
| **SamplingRate**<br />Obbligatoria |Frequenza di campionamento dell'audio in campioni/sec o Hz.  <br /><br />Esempio: `"SamplingRate": 48000`|
| **Bitrate**<br />Obbligatoria |Velocità media in bit audio in bit al secondo, calcolata in base all'elemento AssetFile. Conta solo il payload del flusso elementare e non include il sovraccarico dovuto alla creazione di pacchetti.  <br /><br />Esempio: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Esempio di schema JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md)
