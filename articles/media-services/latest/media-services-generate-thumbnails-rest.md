---
title: Come generare anteprime usando il codificatore di servizi multimediali di Azure standard con REST
description: Questo articolo illustra come usare REST per codificare un asset e generare anteprime allo stesso tempo usando Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: ec2782297f2659341c9fa7e87ce15d3dbceb022c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019563"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Come generare anteprime usando il codificatore standard con REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

È possibile usare Media Encoder Standard per generare una o più anteprime dal video di input in un formato file di immagine a scelta tra [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) e [BMP](https://en.wikipedia.org/wiki/BMP_file_format).

## <a name="recommended-reading-and-practice"></a>Letture e procedure consigliate

Si consiglia di acquisire familiarità con le trasformazioni personalizzate leggendo [come codificare con una trasformazione personalizzata-Rest](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Parametri di anteprima

È necessario impostare i parametri seguenti:

- **Start** : posizione nel video di input da cui iniziare a generare le anteprime. Il valore può essere nel formato ISO 8601 (ad esempio, PT05S per iniziare a 5 secondi) o un conteggio dei frame (ad esempio, 10 per iniziare al decimo frame) o un valore relativo per la durata del flusso (ad esempio, il 10% per iniziare al 10% della durata del flusso). Supporta anche una macro {Best}, che indica al codificatore di selezionare la migliore anteprima dai primi secondi del video e produrrà solo un'anteprima, indipendentemente dalle altre impostazioni per il passaggio e l'intervallo. Il valore predefinito è macro {Best}.
- **Step** : intervalli di generazione delle anteprime. Il valore può essere in formato ISO 8601 (ad esempio, PT05S per un'immagine ogni 5 secondi) o un numero di frame (ad esempio, 30 per un'immagine ogni 30 fotogrammi) o un valore relativo per la durata del flusso (ad esempio, 10% per un'immagine ogni 10% della durata del flusso). Il valore del passaggio influirà sulla prima anteprima generata, che potrebbe non corrispondere esattamente a quella specificata in fase di inizio del set di impostazioni di trasformazione. Ciò è dovuto al codificatore, che tenta di selezionare la migliore anteprima tra l'ora di inizio e la posizione del passaggio dall'ora di inizio come primo output. Poiché il valore predefinito è 10%, significa che se il flusso ha una durata prolungata, la prima anteprima generata potrebbe essere lontana da quella specificata all'ora di inizio. Provare a selezionare un valore ragionevole per step se si prevede che la prima anteprima sia prossima all'ora di inizio oppure impostare il valore di intervallo su 1 se è necessaria una sola anteprima all'ora di inizio.
- **intervallo** : posizione relativa all'ora di inizio della trasformazione del set di impostazioni nel video di input in corrispondenza della quale interrompere la generazione delle anteprime. Il valore può essere nel formato ISO 8601 (ad esempio, PT5M30S per arrestare a 5 minuti e 30 secondi dall'ora di inizio) oppure un conteggio dei frame, ad esempio 300, per arrestare il frame 1/300 dal frame all'ora di inizio. Se questo valore è 1, significa solo produrre un'anteprima all'ora di inizio o un valore relativo alla durata del flusso (ad esempio, 50% per arrestare a metà della durata del flusso dall'ora di inizio). Il valore predefinito è 100%, che significa arrestarsi alla fine del flusso.
- **Layers** : raccolta di livelli di immagine di output che devono essere generati dal codificatore.

## <a name="example-of-a-single-png-file-preset"></a>Esempio di set di impostazioni per un singolo file PNG

Il set di impostazioni JSON seguente può essere usato per produrre un singolo file PNG di output dai primi secondi del video di input, in cui il codificatore esegue il tentativo di ricerca di un frame "interessante". Osservare come le dimensioni dell'immagine di output siano state impostate su 100%, in modo da corrispondere alle dimensioni del video di input. Si noti anche che è necessario che l'impostazione "Format" in "Outputs" corrisponda all'uso di "PngLayers" nella sezione "Codec".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Esempio di set di impostazioni per una serie di immagini JPEG

Il set di impostazioni JSON seguente può essere usato per produrre un set di 10 immagini con timestamp del 5%, 15%,..., 95% della sequenza temporale di input, in cui le dimensioni dell'immagine sono specificate come un trimestre del video di input.

### <a name="json-preset"></a>Set di impostazioni JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Esempio di set di impostazioni per un'immagine in corrispondenza di un timestamp specifico

Il set di impostazioni JSON seguente può essere usato per produrre una singola immagine JPEG in corrispondenza del contrassegno di 30 secondi del video di input. Questo set di impostazioni prevede che il video di input abbia una durata maggiore di 30 secondi. In caso contrario, il processo non riuscirà.

### <a name="json-preset"></a>Set di impostazioni JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Esempio di set di impostazioni per anteprime con risoluzioni diverse

È possibile usare il set di impostazioni seguente per generare anteprime con risoluzioni diverse in un'unica attività. Nell'esempio, in corrispondenza delle posizioni 5%, 15%, …, 95% della sequenza temporale di input, il codificatore genera due immagini, una con risoluzione video di input al 100% e l'altra al 50%.

Si noti l'uso della macro {Resolution} in FileName, che indica al codificatore di usare la larghezza e l'altezza specificate nella sezioneEncoding del set di impostazioni durante la generazione del nome file delle immagini di output. Questo consente anche di distinguere facilmente tra le diverse immagini.

### <a name="json-preset"></a>Set di impostazioni JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Esempio di generazione di un'anteprima durante la codifica

Mentre tutti gli esempi precedenti hanno descritto come inviare un'attività di codifica che produce solo immagini, è anche possibile combinare la codifica audio/video con la generazione di anteprime. Il set di impostazioni JSON seguente indica al codificatore standard di generare un'anteprima durante la codifica.

### <a name="json-preset"></a>Set di impostazioni JSON

Per informazioni sullo schema, vedere [questo](../previous/media-services-mes-schema.md) articolo.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
[Generare anteprime usando .NET](media-services-generate-thumbnails-dotnet.md)