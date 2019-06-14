---
title: Codifica di trasformazione personalizzato con servizi multimediali v3 CLI - Azure | Microsoft Docs
description: Questo argomento illustra come usare servizi multimediali di Azure v3 per codificare una trasformazione personalizzata utilizzando CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762016"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Come codificare con una trasformazione personalizzata - CLI

Durante la codifica con servizi multimediali di Azure, è possibile iniziare a usare rapidamente uno dei consigliati predefiniti incorporati, basati sulle procedure consigliate del settore, come illustrato nel [Streaming i file](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Guida introduttiva. È anche possibile creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato.

## <a name="considerations"></a>Considerazioni

Durante la creazione di set di impostazioni personalizzati, si applicano le considerazioni seguenti:

* Tutti i valori per l'altezza e larghezza su contenuto AVC devono essere un multiplo di 4.
* In servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Ciò è diverso dal set di impostazioni con le nostre API v2, che usato kilobit al secondo come unità. Ad esempio, se la velocità in bit nella versione 2 è stato specificato come 128 (kilobit/sec), in v3 valore potrebbe essere impostato su 128000 (bit/sec).

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). <br/>Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definire un set di impostazioni personalizzato

L'esempio seguente definisce il corpo della richiesta di un nuovo file di trasformazione. Si definisce un set di output che si vuole essere generato quando viene utilizzata questa trasformazione. 

In questo esempio, è innanzitutto necessario aggiungere un livello AacAudio per la codifica audio e due livelli di H264Video per la codifica video. Nei livelli video, abbiamo assegnare etichette in modo che possono essere utilizzati nei nomi dei file di output. Successivamente, si desidera l'output includono anche le anteprime. Nell'esempio seguente viene specificato di immagini in formato PNG, generato al 50% della risoluzione del video di input e tre i timestamp - {25%, 50%, 75} della lunghezza del video di input. Infine, specifichiamo il formato per i file di output: uno per video e audio e l'altro per le anteprime. Poiché sono presenti più H264Layers, è necessario usare le macro che generano nomi univoci per ogni livello. È possibile usare una `{Label}` o `{Bitrate}` (macro), nell'esempio viene illustrato il primo.

Si intende salvare questa trasformazione in un file. In questo esempio, abbiamo denominare il file `customPreset.json`. 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
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

```

## <a name="create-a-new-transform"></a>Creare una nuova trasformazione  

In questo esempio, si crea una **trasformare** basato su set di impostazioni personalizzato definito in precedenza. Quando si crea una trasformazione, controllare innanzitutto se ne esiste già. Se non esiste, la trasformazione di riutilizzarlo. Quanto segue `show` comando restituisce le `customTransformName` trasformare se esistente:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Il comando seguente crea la trasformazione di base di set di impostazioni personalizzato (indicato in precedenza). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Per servizi multimediali applicare la trasformazione al specificato video o audio, è necessario inviare un processo in tale trasformazione. Per un esempio completo che illustra come inviare un processo in una trasformazione, vedere [Guida introduttiva: Stream file video - CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Vedere anche

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
