---
title: Codificare la trasformazione personalizzata usando servizi multimediali V3 REST-Azure | Microsoft Docs
description: Questo argomento illustra come usare servizi multimediali di Azure V3 per codificare una trasformazione personalizzata con REST.
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
ms.openlocfilehash: 4bd092dbee09a783dcbd6e36c82a70ff5decaf83
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053327"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Come codificare con una trasformazione personalizzata-REST

Quando si esegue la codifica con servizi multimediali di Azure, è possibile iniziare rapidamente con uno dei set di impostazioni predefiniti consigliati, in base alle procedure consigliate del settore, come illustrato nell'esercitazione relativa ai [file di streaming](stream-files-tutorial-with-rest.md#create-a-transform) . È anche possibile creare un set di impostazioni personalizzato per individuare i requisiti specifici dello scenario o del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando si creano set di impostazioni personalizzati, si applicano le considerazioni seguenti:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In servizi multimediali di Azure V3 tutti i bitrate di codifica sono in bit al secondo. Questa impostazione è diversa da quella delle API v2, che hanno usato kilobit al secondo come unità. Se, ad esempio, la velocità in bit in V2 è stata specificata come 128 (kilobit al secondo), in V3 verrebbe impostata su 128000 (bit al secondo).

## <a name="prerequisites"></a>Prerequisiti 

- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). <br/>Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).<br/>Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definire un set di impostazioni personalizzato

Nell'esempio seguente viene definito il corpo della richiesta di una nuova trasformazione. Si definisce un set di output che si desidera generare quando si utilizza questa trasformazione. 

In questo esempio viene innanzitutto aggiunto un livello l'elemento aacaudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video si assegnano le etichette in modo da poterle usare nei nomi dei file di output. Successivamente, si vuole che l'output includa anche anteprime. Nell'esempio seguente vengono specificate le immagini in formato PNG, generate al 50% della risoluzione del video di input e tre timestamp: {25%, 50%, 75} della lunghezza del video di input. Infine, viene specificato il formato per i file di output, uno per video e audio e un altro per le anteprime. Poiché sono presenti più H264Layers, è necessario usare macro che producono nomi univoci per livello. È possibile usare una `{Label}` macro o `{Bitrate}` , l'esempio mostra la prima.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Crea una nuova trasformazione  

In questo esempio viene creata una **trasformazione** basata sul set di impostazioni personalizzato definito in precedenza. Quando si crea una trasformazione, è necessario innanzitutto usare [Get](/rest/api/media/transforms/get) per verificare se ne esiste già una. Se la trasformazione esiste, riutilizzarla. 

Nella raccolta dei messaggi scaricati selezionare **trasformazioni e processi** -> **Crea o aggiorna trasformazione**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selezionare la scheda **Body (corpo** ) e sostituire il corpo con il codice JSON definito in [precedenza](#define-a-custom-preset). Per fare in modo che servizi multimediali applichi la trasformazione al video o all'audio specificato, è necessario inviare un processo nella trasformazione.

Selezionare **Send** (Invia). 

Per fare in modo che servizi multimediali applichi la trasformazione al video o all'audio specificato, è necessario inviare un processo nella trasformazione. Per un esempio completo in cui viene illustrato come inviare un processo in una trasformazione, vedere [esercitazione: eseguire il flusso di file video-Rest](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [altre operazioni REST](/rest/api/media/)
