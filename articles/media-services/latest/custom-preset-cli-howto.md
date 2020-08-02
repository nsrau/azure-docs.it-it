---
title: Codificare la trasformazione personalizzata usando l'interfaccia della riga di comando di Azure per servizi multimediali Microsoft Docs
description: Questo argomento illustra come usare servizi multimediali di Azure V3 per codificare una trasformazione personalizzata usando l'interfaccia della riga di comando di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: devx-track-azurecli
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 4ea328cc21d849367d36342ed1b0953a7eb26c77
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503889"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Come codificare con una trasformazione personalizzata-interfaccia della riga di comando di Azure

Quando si esegue la codifica con servizi multimediali di Azure, è possibile iniziare rapidamente con uno dei set di impostazioni predefiniti consigliati, in base alle procedure consigliate del settore, come illustrato nella Guida introduttiva ai [file di streaming](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) . È anche possibile creare un set di impostazioni personalizzato per individuare i requisiti specifici dello scenario o del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando si creano set di impostazioni personalizzati, si applicano le considerazioni seguenti:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In servizi multimediali di Azure V3 tutti i bitrate di codifica sono in bit al secondo. Questa impostazione è diversa da quella delle API v2, che hanno usato kilobit al secondo come unità. Se, ad esempio, la velocità in bit in V2 è stata specificata come 128 (kilobit al secondo), in V3 verrebbe impostata su 128000 (bit al secondo).

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definire un set di impostazioni personalizzato

Nell'esempio seguente viene definito il corpo della richiesta di una nuova trasformazione. Si definisce un set di output che si desidera generare quando si utilizza questa trasformazione.

In questo esempio viene innanzitutto aggiunto un livello l'elemento aacaudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video si assegnano le etichette in modo da poterle usare nei nomi dei file di output. Successivamente, si vuole che l'output includa anche anteprime. Nell'esempio seguente vengono specificate le immagini in formato PNG, generate al 50% della risoluzione del video di input e tre timestamp: {25%, 50%, 75} della lunghezza del video di input. Infine, viene specificato il formato per i file di output, uno per video e audio e un altro per le anteprime. Poiché sono presenti più H264Layers, è necessario usare macro che producono nomi univoci per livello. È possibile usare una `{Label}` macro o `{Bitrate}` , l'esempio mostra la prima.

Questa trasformazione verrà salvata in un file. In questo esempio il file è denominato `customPreset.json` .

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

## <a name="create-a-new-transform"></a>Crea una nuova trasformazione  

In questo esempio viene creata una **trasformazione** basata sul set di impostazioni personalizzato definito in precedenza. Quando si crea una trasformazione, è necessario prima verificare se ne esiste già una. Se la trasformazione esiste, riutilizzarla. Il `show` comando seguente restituisce la `customTransformName` trasformazione se esistente:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Il comando dell'interfaccia della riga di comando di Azure seguente crea la trasformazione in base al set di impostazioni personalizzato (definito in precedenza).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Per fare in modo che servizi multimediali applichi la trasformazione al video o all'audio specificato, è necessario inviare un processo nella trasformazione. Per un esempio completo in cui viene illustrato come inviare un processo in una trasformazione, vedere [Guida introduttiva: eseguire lo streaming di file video-interfaccia](stream-files-cli-quickstart.md)della riga di comando di Azure.

## <a name="see-also"></a>Vedere anche

[Interfaccia della riga di comando di Azure](/cli/azure/ams)
