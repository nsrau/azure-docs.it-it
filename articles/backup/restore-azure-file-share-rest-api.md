---
title: Ripristinare le condivisioni file di Azure con l'API REST
description: Informazioni su come usare l'API REST per ripristinare condivisioni file di Azure o file specifici da un punto di ripristino creato da backup di Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252506"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Ripristinare le condivisioni file di Azure usando l'API REST

Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) usando l'API REST.

Alla fine di questo articolo si apprenderà come eseguire le operazioni seguenti usando l'API REST:

* Visualizzare i punti di ripristino per una condivisione file di Azure di cui è stato eseguito il backup.
* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.

## <a name="prerequisites"></a>Prerequisiti

Si presuppone che sia già presente una condivisione file di cui si vuole eseguire il ripristino. In caso contrario, selezionare [backup condivisione file di Azure usando l'API REST](backup-azure-file-share-rest-api.md) per informazioni su come crearne uno.

Per questo articolo, verranno usate le risorse seguenti:

* **Recoveryservicesvault fino**: *azurefilesvault*
* **Gruppo di risorse**: *risorsa*
* **Account di archiviazione**: *afsaccount*
* **Condivisione file**: *risorsa*

## <a name="fetch-containername-and-protecteditemname"></a>Fetch ContainerName e ProtectedItemName

Per la maggior parte delle chiamate API di ripristino correlate, è necessario passare i valori per i parametri URI {ContainerName} e {protectedItemName}. Usare l'attributo ID nel corpo della risposta dell'operazione [Get backupprotectableitems](https://docs.microsoft.com/rest/api/backup/protecteditems/get) per recuperare i valori per questi parametri. In questo esempio, l'ID della condivisione file che si vuole proteggere è:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Quindi, i valori vengono convertiti come segue:

* {ContainerName}- *StorageContainer; storage; risorsa; afsaccount*
* {protectedItemName}- *azurefileshare; risorsa*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Recuperare i punti di ripristino per la condivisione file di backup di Azure

Per ripristinare eventuali file o condivisioni file di cui è stato eseguito il backup, selezionare prima di tutto un punto di ripristino per eseguire l'operazione di ripristino. I punti di ripristino disponibili di un elemento di cui è stato eseguito il backup possono essere elencati usando la chiamata all'API REST per l'elenco dei punti di [ripristino](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) . Si tratta di un'operazione GET con tutti i valori pertinenti.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Impostare i valori URI nel modo seguente:

* {fabricname}: *Azure*
* {VAULTNAME}: *azurefilesvault*
* {ContainerName}: *StorageContainer; storage; risorsa; afsaccount*
* {protectedItemName}: *azurefileshare; risorsa*
* {ResourceGroupName}: *risorsa*

All'URI GET sono associati tutti i parametri obbligatori. Non è necessario un corpo aggiuntivo della richiesta.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>Risposta di esempio

Una volta inviato l'URI GET, viene restituita una risposta 200:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Il punto di ripristino viene identificato con il campo {Name} nella risposta precedente.

## <a name="full-share-recovery-using-rest-api"></a>Ripristino della condivisione completa con l'API REST

Usare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.
L'attivazione del ripristino è una richiesta POST ed è possibile eseguire questa operazione usando l'API REST per il [ripristino del trigger](https://docs.microsoft.com/rest/api/backup/restores/trigger) .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

I valori {ContainerName} e {protectedItemName} sono impostati [qui](#fetch-containername-and-protecteditemname) e recoveryPointID è il campo {Name} del punto di ripristino menzionato in precedenza.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Creare il corpo della richiesta

Per attivare un ripristino per una condivisione file di Azure, di seguito sono riportati i componenti del corpo della richiesta:

Nome |  Type   |   Descrizione
--- | ---- | ----
Proprietà | AzureFileShareRestoreRequest | Proprietà di RestoreRequestResource

Per l'elenco completo delle definizioni del corpo della richiesta e altri dettagli, fare riferimento al [documento sull'API REST di ripristino del trigger](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Il ripristino viene eseguito nel percorso originale

#### <a name="request-body-example"></a>Esempio di corpo della richiesta

Il corpo della richiesta seguente definisce le proprietà necessarie per attivare un ripristino della condivisione file di Azure:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Ripristinare in un percorso alternativo

Specificare i seguenti parametri per il ripristino del percorso alternativo:

* **targetResourceId**: account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **nome**: la condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **targetFolderPath**: cartella nella condivisione file in cui vengono ripristinati i dati.

#### <a name="request-body-example"></a>Esempio di corpo della richiesta

Il corpo della richiesta seguente ripristina la condivisione file *risorsa* nell'account di archiviazione *afsaccount* nella condivisione file *azurefiles1* nell'account di archiviazione *afaccount1* .

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Risposta

L'attivazione di un'operazione di ripristino è un' [operazione asincrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Questa operazione crea un'altra operazione che deve essere rilevata separatamente.
Restituisce due risposte: 202 (accettato) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

#### <a name="response-example"></a>Esempio di risposta

Una volta inviato l'URI *post* per l'attivazione di un ripristino, la risposta iniziale è 202 (accettata) con un'intestazione Location o Azure-Async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Tenere quindi traccia dell'operazione risultante usando l'intestazione Location o l'intestazione Azure-AsyncOperation con un comando GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Al termine dell'operazione, viene restituita la risposta 200 (OK) con l'ID del processo di ripristino risultante nel corpo della risposta.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Per il ripristino del percorso alternativo, il corpo della risposta sarà simile al seguente:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Poiché il processo di backup è un'operazione con esecuzione prolungata, ne deve essere tenuta traccia come illustrato nel [documento per monitorare i processi usando l'API REST](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Ripristino a livello di elemento tramite l'API REST

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

I valori {ContainerName} e {protectedItemName} sono impostati [qui](#fetch-containername-and-protecteditemname) e recoveryPointID è il campo {Name} del punto di ripristino menzionato in precedenza.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Creare il corpo della richiesta

Per attivare un ripristino per una condivisione file di Azure, di seguito sono riportati i componenti del corpo della richiesta:

Nome |  Type   |   Descrizione
--- | ---- | ----
Proprietà | AzureFileShareRestoreRequest | Proprietà di RestoreRequestResource

Per l'elenco completo delle definizioni del corpo della richiesta e altri dettagli, fare riferimento al [documento sull'API REST di ripristino del trigger](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Il ripristino viene eseguito nel percorso originale

Il corpo della richiesta seguente consente di ripristinare il file *Restoretest. txt* nella condivisione file *risorsa* nell'account di archiviazione *afsaccount* .

Crea corpo della richiesta

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>Ripristinare in un percorso alternativo

Il corpo della richiesta seguente consente di ripristinare il file *Restoretest. txt* nella condivisione file *risorsa* nell'account di archiviazione *afsaccount* nella cartella *RestoreData* della condivisione file *azurefiles1* nell'account di archiviazione *afaccount1* .

Creare il corpo della richiesta

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

La risposta deve essere gestita in modo analogo a quanto descritto in precedenza per i [ripristini di condivisione completi](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [gestire il backup delle condivisioni file di Azure con l'API REST](manage-azure-file-share-rest-api.md).
