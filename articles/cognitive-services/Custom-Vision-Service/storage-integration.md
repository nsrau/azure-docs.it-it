---
title: Integrare archiviazione di Azure per le notifiche e il backup dei modelli
titleSuffix: Azure Cognitive Services
description: Informazioni su come integrare archiviazione di Azure per ricevere notifiche push quando si esegue il training o l'esportazione di modelli Visione personalizzata. È anche possibile salvare un backup dei modelli esportati.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f4d9cc4c02ab062c73e9dbd977d9ea9e6ccdb60d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532790"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrare archiviazione di Azure per le notifiche e il backup

È possibile integrare il progetto di Visione personalizzata con una coda di archiviazione BLOB di Azure per ottenere notifiche push di attività di training/esportazione di progetto e copie di backup di modelli pubblicati. Questa funzionalità è utile per evitare di eseguire continuamente il polling del servizio per ottenere risultati durante l'esecuzione di operazioni lunghe. Al contrario, è possibile integrare le notifiche della coda di archiviazione nel flusso di lavoro.

Questa guida illustra come usare queste API REST con cURL. Per eseguire le richieste, è anche possibile usare un servizio di richiesta HTTP, ad esempio il poster.

> [!NOTE]
> Le notifiche push dipendono dal parametro facoltativo _notificationQueueUri_ nell'API **CreateProject** e i backup del modello richiedono che venga usato anche il parametro facoltativo _exportModelContainerUri_ . Questa guida utilizzerà entrambi per il set completo di funzionalità.

## <a name="prerequisites"></a>Prerequisiti

- Una risorsa Visione personalizzata in Azure. Se non si dispone di uno, passare alla portale di Azure e [creare una nuova risorsa visione personalizzata](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Questa funzionalità non supporta attualmente la risorsa servizio cognitivo (tutte in una chiave).
- Un account di archiviazione di Azure con un contenitore BLOB. Se è necessario assistenza per questo passaggio, seguire [gli esercizi 1 del Lab di archiviazione di Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) .

## <a name="set-up-azure-storage-integration"></a>Configurare l'integrazione di archiviazione di Azure

Passare alla risorsa di training Visione personalizzata nel portale di Azure, selezionare la pagina **identità** e abilitare identità gestita assegnata dal sistema.

Passare quindi alla risorsa di archiviazione nel portale di Azure. Passare alla pagina **controllo di accesso (IAM)** e aggiungere un'assegnazione di ruolo per ogni funzionalità di integrazione:
* Selezionare la risorsa di training Visione personalizzata e assegnare il ruolo di **collaboratore dati BLOB di archiviazione** se si prevede di usare la funzionalità di backup del modello. 
* Selezionare quindi la risorsa di training Visione personalizzata e assegnare il **collaboratore ai dati della coda di archiviazione** se si prevede di usare la funzionalità coda di notifica.

> [!div class="mx-imgBorder"]
> ![Pagina Aggiungi assegnazione ruolo dell'account di archiviazione](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Ottenere gli URL di integrazione

Si otterranno quindi gli URL che consentono alla risorsa Visione personalizzata di accedere a questi endpoint.

Per l'URL di integrazione della coda di notifica, passare alla pagina **Code** dell'account di archiviazione, aggiungere una nuova coda e salvarne l'URL in un percorso temporaneo.

> [!div class="mx-imgBorder"]
> ![Pagina della coda di archiviazione di Azure](./media/storage-integration/queue-url.png) 

Per l'URL di integrazione del backup del modello, passare alla pagina **contenitori** dell'account di archiviazione e creare un nuovo contenitore. Quindi selezionarlo e passare alla pagina delle **Proprietà** . Copiare l'URL in un percorso temporaneo.
 
> [!div class="mx-imgBorder"]
> ![Pagina delle proprietà del contenitore di archiviazione di Azure](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrare Visione personalizzata progetto

Ora che si dispone degli URL di integrazione, è possibile creare un nuovo progetto di Visione personalizzata che integra le funzionalità di archiviazione di Azure. È anche possibile aggiornare un progetto esistente per aggiungere le funzionalità.

### <a name="create-new-project"></a>Creare un nuovo progetto

Quando si chiama l'API [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) , aggiungere i parametri facoltativi _exportModelContainerUri_ e _notificationQueueUri_. Assegnare i valori URL ottenuti nella sezione precedente. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Se si riceve una `200/OK` risposta, significa che gli URL sono stati configurati correttamente. Verranno visualizzati anche i valori URL nella risposta JSON:

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
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Aggiorna progetto esistente

Per aggiornare un progetto esistente con l'integrazione delle funzionalità di archiviazione di Azure, chiamare l'API [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) usando l'ID del progetto che si vuole aggiornare. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Impostare il corpo della richiesta ( `body` ) sul formato JSON seguente, inserendo i valori appropriati per _ExportModelContainerUri_ e _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Se si riceve una `200/OK` risposta, significa che gli URL sono stati configurati correttamente.

## <a name="verify-the-connection"></a>Verificare la connessione 

La chiamata API nella sezione precedente dovrebbe avere già attivato nuove informazioni nell'account di archiviazione di Azure. 

Nel contenitore designato dovrebbe essere presente un BLOB di test all'interno di una cartella **CustomVision-TestPermission** . Questo BLOB esisterà solo temporaneamente.

Nella coda di notifiche verrà visualizzata una notifica di prova nel formato seguente:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Ricevi notifiche degli eventi

Quando si è pronti, chiamare l'API [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) nel progetto per eseguire una normale operazione di training.

Nella coda di notifica dell'archiviazione si riceverà una notifica al termine del training:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

Il `"trainingStatus"` campo può essere `"TrainingCompleted"` o `"TrainingFailed"` . Il `"iterationId"` campo è l'ID del modello sottoposto a training.

## <a name="get-model-export-backups"></a>Ottenere i backup di esportazione del modello

Quando si è pronti, chiamare l'API [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) per esportare un modello sottoposto a training in una piattaforma specifica.

Nel contenitore di archiviazione designato verrà visualizzata una copia di backup del modello esportato. Il nome del BLOB avrà il formato seguente:

```
{projectId} - {iterationId}.{platformType}
```

Al termine dell'esportazione, si riceverà anche una notifica nella coda. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

Il `"exportStatus"` campo può essere `"ExportCompleted"` o `"ExportFailed"` . Il `"modelUri"` campo conterrà l'URL del modello di backup archiviato nel contenitore, presupponendo che l'utente abbia integrato le notifiche della coda all'inizio. Se non è stato fatto, il `"modelUri"` campo visualizzerà l'URL di firma di accesso condiviso per il BLOB del modello di visione personalizzata.

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come copiare e spostare un progetto tra Visione personalizzata risorse. Esplorare quindi la documentazione di riferimento per le API per scoprire le altre operazioni che è possibile eseguire con Visione personalizzata.
* [Documentazione di riferimento delle API REST](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
