---
title: Clip secondario di un video durante la codifica con REST di Servizi multimediali di AzureSubclip a video when encoding with Azure Media Services REST
description: Questo argomento descrive come ritagliare un video durante la codifica con Servizi multimediali di Azure usando RESTThis topic describes how to subclip a video when encoding with Azure Media Services using REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514324"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Clip secondario di un video durante la codifica con Servizi multimediali - REST

Potete tagliare o ritagliare un video durante la codifica utilizzando un [lavoro.](https://docs.microsoft.com/rest/api/media/jobs) Questa funzionalità funziona con qualsiasi [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) compilata utilizzando i predefiniti [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

L'esempio REST in questo argomento crea un processo che taglia un video durante l'invio di un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account di Servizi multimediali di Azure.](create-account-cli-how-to.md)
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).
    
    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Creare una trasformazione e un asset di output. È possibile vedere come creare una trasformazione e un asset di output nella [codifica di un file remoto in base all'URL e](stream-files-tutorial-with-rest.md) lo streaming del video - REST esercitazione.
- Esaminare l'argomento [Concetto di codifica.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Creare un processo di sottoclip

1. Nella raccolta Postman scaricata selezionare **Trasformazioni e processi** -> **Crea processo con Ritaglio secondario**.
    
    La richiesta PUT è simile alla seguente:The **PUT** request looks like this:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Aggiornare il valore della variabile di ambiente "transformName" con il nome della trasformazione. 
1. Selezionare la scheda **Corpo** e aggiornare "myOutputAsset" con il nome dell'asset di output.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Fare clic su **Invia**.

    Viene visualizzata la **risposta** con le informazioni sul processo creato e inviato e lo stato del processo. 

## <a name="next-steps"></a>Passaggi successivi

[Come codificare con una trasformazione personalizzataHow to encode with a custom transform](custom-preset-rest-howto.md) 
