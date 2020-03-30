---
title: Codificare una trasformazione personalizzata usando l'interfaccia della riga di comando di Azure di Servizi multimediali v3. Documenti Microsoft
description: Questo argomento illustra come usare Servizi multimediali di Azure v3 per codificare una trasformazione personalizzata usando l'interfaccia della riga di comando di Azure.This topic shows how to use Azure Media Services v3 to encode a custom transform using Azure CLI.
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
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382954"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Come codificare con una trasformazione personalizzata - Interfaccia della riga di comando di AzureHow to encode with a custom transform - Azure CLI

Quando si esegue la codifica con Servizi multimediali di Azure, è possibile iniziare rapidamente con una delle procedure predefinite predefinite predefinite consigliate, in base alle procedure consigliate del settore, come illustrato nella guida introduttiva dei file di [streaming.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Puoi anche creare un predefinito personalizzato per il tuo scenario specifico o i requisiti del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando create predefiniti personalizzati, si applicano le seguenti considerazioni:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In Servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo è diverso dai preset con le nostre API v2, che hanno usato kilobit al secondo come unità. Ad esempio, se la velocità in bit 2 è stata specificata come 128 (kilobit/secondo), in v3 verrà impostata su 128000 (bit/secondo).

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definire un predefinito personalizzato

Nell'esempio seguente viene definito il corpo della richiesta di una nuova trasformazione. Definiamo un set di output che vogliamo generare quando viene usata questa trasformazione.

In questo esempio, aggiungiamo innanzitutto un livello AacAudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video, assegniamo le etichette in modo che possano essere utilizzate nei nomi dei file di output. Successivamente, vogliamo che l'output includa anche le miniature. Nell'esempio seguente specifichiamo le immagini in formato PNG, generate al 50% della risoluzione del video di input, e a tre timestamp : 25%, 50%, 75 della lunghezza del video di input. Infine, specifichiamo il formato per i file di output - uno per il video e l'audio e un altro per le miniature. Poiché abbiamo più H264Layers, dobbiamo usare macro che producono nomi univoci per ogni livello. Possiamo usare una `{Label}` `{Bitrate}` o macro, l'esempio mostra il primo.

Salveremo questa trasformazione in un file. In questo esempio, nominiamo il file `customPreset.json`.

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

In questo esempio viene creata una **trasformazione** basata sul predefinito personalizzato definito in precedenza. Quando si crea una trasformazione, è necessario innanzitutto verificare se ne esiste già una. Se la trasformazione esiste, riutilizzarla. Il `show` comando seguente `customTransformName` restituisce la trasformazione, se esistente:The following command returns the transform if it exists:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Il comando CLI di Azure seguente crea la trasformazione in base alla preimpostazione personalizzata (definita in precedenza).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Affinché Servizi multimediali applichi la trasformazione al video o all'audio specificato, devi inviare un lavoro in tale trasformazione. Per un esempio completo che illustra come inviare un processo in una trasformazione, vedere Guida introduttiva: File video di flusso - Interfaccia della riga di comando di Azure.For a complete example that shows how to submit a job under a transform, [see Quickstart: Stream video files - Azure CLI.](stream-files-cli-quickstart.md)

## <a name="see-also"></a>Vedere anche

[Interfaccia della riga di comando di AzureAzure](/cli/azure/ams)
