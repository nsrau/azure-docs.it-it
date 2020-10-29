---
title: Copiare e spostare Visione personalizzata progetti
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare le API ExportProject e ImportProject per copiare e spostare i progetti Visione personalizzata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: c742240cc82035b2a9af16348eaa9f2a40a32fa3
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900330"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Copiare e spostare i progetti di Visione personalizzata

Dopo aver creato e sottoposto a Training un progetto di Visione personalizzata, è consigliabile copiare il progetto in un'altra risorsa. Ad esempio, potrebbe essere necessario spostare un progetto da un ambiente di sviluppo in un ambiente di produzione oppure eseguire il backup di un progetto in un account in un'area di Azure diversa per aumentare la sicurezza dei dati.

Le API **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** e **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** abilitano questo scenario consentendo di copiare i progetti da un account visione personalizzata ad altri. Questa guida illustra come usare queste API REST con cURL. Per eseguire le richieste, è anche possibile usare un servizio di richiesta HTTP, ad esempio il poster.

## <a name="business-scenarios"></a>Scenari aziendali

Se l'app o l'azienda dipende dall'uso di un progetto di Visione personalizzata, è consigliabile copiare il modello in un altro account Visione personalizzata in un'altra area. Se si verifica un'interruzione a livello di area, è possibile accedere al progetto nell'area in cui è stato copiato.

##  <a name="prerequisites"></a>Prerequisiti

- Due risorse Visione personalizzata di Azure. Se non sono disponibili, passare alla portale di Azure e [creare una nuova risorsa di visione personalizzata](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Chiavi di training e URL dell'endpoint delle risorse del Visione personalizzata. È possibile trovare questi valori nella scheda **Panoramica** della risorsa nel portale di Azure.
- Progetto Visione personalizzata creato. Per istruzioni su come eseguire questa operazione, vedere [compilare un classificatore](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier) .

## <a name="process-overview"></a>Panoramica del processo

Il processo per la copia di un progetto è costituito dai passaggi seguenti:

1. In primo luogo, si ottiene l'ID del progetto nell'account di origine che si vuole copiare.
1. Quindi si chiama l'API **ExportProject** usando l'ID progetto e la chiave di training dell'account di origine. Si otterrà una stringa di token temporanea.
1. Quindi si chiama l'API **ImportProject** usando la stringa del token e la chiave di training dell'account di destinazione. Il progetto verrà quindi elencato sotto l'account di destinazione.

## <a name="get-the-project-id"></a>Ottenere l'ID progetto

Chiamare prima **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** per visualizzare un elenco dei progetti di visione personalizzata esistenti e dei relativi ID. Usare la chiave di training e l'endpoint dell'account di origine.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Si otterrà una `200\OK` risposta con un elenco di progetti e i relativi metadati nel corpo. Il `"id"` valore è la stringa da copiare per i passaggi successivi.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Esportare il progetto

Chiamare **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** usando l'ID del progetto e la chiave e l'endpoint di training del codice sorgente.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Si otterrà una `200/OK` risposta con i metadati sul progetto esportato e una stringa di riferimento `"token"` . Copiare il valore del token.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importare il progetto

Chiamare **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** usando la chiave di training e l'endpoint di destinazione, insieme al token di riferimento. È anche possibile assegnare un nome al progetto nel nuovo account.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Si otterrà una `200/OK` risposta con i metadati sul progetto appena importato.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come copiare e spostare un progetto tra Visione personalizzata risorse. Esplorare quindi la documentazione di riferimento per le API per scoprire le altre operazioni che è possibile eseguire con Visione personalizzata.
* [Documentazione di riferimento delle API REST](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
