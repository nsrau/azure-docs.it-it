---
title: Un video di sottoclip durante la codifica con API REST di servizi multimediali di Azure
description: In questo argomento viene descritto come un video di sottoclip durante la codifica con servizi multimediali di Azure tramite REST
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
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305235"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Un video di sottoclip durante la codifica con servizi multimediali - REST

È possibile tagliare o un video di sottoclip durante la codifica tramite un [processo](https://docs.microsoft.com/rest/api/media/jobs). Questa funzionalità funziona con qualsiasi [trasformare](https://docs.microsoft.com/rest/api/media/transforms) che viene compilato usando il [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefiniti, o il [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) i set di impostazioni. 

Nell'esempio REST in questo argomento crea un processo che rimuove un video, come invia un processo di codifica. 

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo argomento, è necessario:

- [Creare un account servizi multimediali di Azure](create-account-cli-how-to.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).
    
    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottenere Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Creare una trasformazione e output asset. È possibile vedere come creare una trasformazione e output gli asset nel [codificare un file remoto basato su URL e streaming di video - REST](stream-files-tutorial-with-rest.md) esercitazione.
- Rivedere le [concetto di codifica](encoding-concept.md) argomento.

## <a name="create-a-subclipping-job"></a>Creare un processo di scomposizione

1. Nella raccolta Postman che è stato scaricato, selezionare **Trasforma e i processi** -> **Crea processo con Sub ritaglio**.
    
    Il **PUT** richiesta si presenta come segue:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Aggiornare il valore della variabile di ambiente "transformName" con il nome della trasformazione. 
1. Selezionare il **corpo** scheda e aggiornare "myOutputAsset" con l'output il nome dell'Asset.

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

    Viene visualizzato il **risposta** con le informazioni su un processo è stato creato e inviato e lo stato del processo. 

## <a name="next-steps"></a>Passaggi successivi

[Come codificare con una trasformazione personalizzata](custom-preset-rest-howto.md) 
