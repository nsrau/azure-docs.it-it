---
title: Sottoclip di un video durante la codifica con REST di servizi multimediali di Azure
description: Questo argomento descrive come eseguire il clip di un video quando si esegue la codifica con servizi multimediali di Azure con REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 2a78e8c8c204e5cedeb8bdd2a25cff40a3e5cc44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296550"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Sottoclip di un video durante la codifica con servizi multimediali-REST

È possibile tagliare o sottotagliare un video quando lo si codifica usando un [processo](/rest/api/media/jobs). Questa funzionalità può essere usata con qualsiasi [Trasformazione](/rest/api/media/transforms) compilata mediante i set di impostazioni [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o i set di impostazioni [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Nell'esempio REST in questo argomento viene creato un processo che consente di tagliare un video quando invia un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account di servizi multimediali di Azure](./create-account-howto.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).
    
    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Creare una trasformazione e un asset di output. È possibile vedere come creare una trasformazione e un asset di output nell'esercitazione [codificare un file remoto basato sull'URL e trasmettere il video-Rest](stream-files-tutorial-with-rest.md) .
- Esaminare l'argomento relativo al [concetto di codifica](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Creazione di un processo di ritaglio

1. Nella raccolta dei messaggi scaricati selezionare **trasformazioni e processi**  ->  **Crea processo con il ritaglio secondario**.
    
    La richiesta **put** ha un aspetto simile al seguente:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Aggiornare il valore della variabile di ambiente "transformname" con il nome della trasformazione. 
1. Selezionare la scheda **Body (corpo** ) e aggiornare "myOutputAsset" con il nome dell'asset di output.

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

    Viene visualizzata la **risposta** con le informazioni sul processo che è stato creato e inviato e lo stato del processo. 

## <a name="next-steps"></a>Passaggi successivi

[Come codificare con una trasformazione personalizzata](custom-preset-rest-howto.md) 
