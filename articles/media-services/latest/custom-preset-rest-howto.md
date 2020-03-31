---
title: Codificare la trasformazione personalizzata usando Media Services v3 REST - Azure Documenti Microsoft
description: Questo argomento illustra come usare Servizi multimediali di Azure v3 per codificare una trasformazione personalizzata usando REST.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761805"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Come codificare con una trasformazione personalizzata - RESTHow to encode with a custom transform - REST

Quando si esegue la codifica con Servizi multimediali di Azure, è possibile iniziare rapidamente con una delle impostazioni predefinite predefinite predefinite consigliate, in base alle procedure consigliate del settore, come illustrato nell'esercitazione [sui file](stream-files-tutorial-with-rest.md#create-a-transform) di streaming. Puoi anche creare un predefinito personalizzato per il tuo scenario specifico o i requisiti del dispositivo.

## <a name="considerations"></a>Considerazioni

Quando create predefiniti personalizzati, si applicano le seguenti considerazioni:

* Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di 4.
* In Servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo è diverso dai preset con le nostre API v2, che hanno usato kilobit al secondo come unità. Ad esempio, se la velocità in bit 2 è stata specificata come 128 (kilobit/secondo), in v3 verrà impostata su 128000 (bit/secondo).

## <a name="prerequisites"></a>Prerequisiti 

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). <br/>Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).<br/>Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definire un predefinito personalizzato

Nell'esempio seguente viene definito il corpo della richiesta di una nuova trasformazione. Definiamo un set di output che vogliamo generare quando viene usata questa trasformazione. 

In questo esempio, aggiungiamo innanzitutto un livello AacAudio per la codifica audio e due livelli H264Video per la codifica video. Nei livelli video, assegniamo le etichette in modo che possano essere utilizzate nei nomi dei file di output. Successivamente, vogliamo che l'output includa anche le miniature. Nell'esempio seguente specifichiamo le immagini in formato PNG, generate al 50% della risoluzione del video di input, e a tre timestamp : 25%, 50%, 75 della lunghezza del video di input. Infine, specifichiamo il formato per i file di output - uno per il video e l'audio e un altro per le miniature. Poiché abbiamo più H264Layers, dobbiamo usare macro che producono nomi univoci per ogni livello. Possiamo usare una `{Label}` `{Bitrate}` o macro, l'esempio mostra il primo.

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

## <a name="create-a-new-transform"></a>Creare una nuova trasformazione  

In questo esempio viene creata una **trasformazione** basata sul predefinito personalizzato definito in precedenza. Quando si crea una trasformazione, è necessario innanzitutto utilizzare [Get](https://docs.microsoft.com/rest/api/media/transforms/get) per verificare se ne esiste già una. Se la trasformazione esiste, riutilizzarla. 

Nella raccolta Postman scaricata selezionare **Trasformazioni e processi**->**Crea o Aggiorna trasformazione**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selezionare la scheda **Corpo** e sostituire il corpo con il codice json [definito in precedenza.](#define-a-custom-preset) Affinché Servizi multimediali applichi la trasformazione al video o all'audio specificato, devi inviare un lavoro in tale trasformazione.

Selezionare **Invia**. 

Affinché Servizi multimediali applichi la trasformazione al video o all'audio specificato, devi inviare un lavoro in tale trasformazione. Per un esempio completo che illustra come inviare un processo in una trasformazione, vedere [Esercitazione: Eseguire lo streaming di file video - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [altre operazioni RESTSee other REST operations](https://docs.microsoft.com/rest/api/media/)
