---
title: Gestire il backup di condivisione file di Azure con l'API RestManage Azure File share backup with Rest API
description: Informazioni su come usare l'API REST per gestire e monitorare le condivisioni file di Azure di cui viene eseguito il backup da Backup di Azure.Learn how to use REST API to manage and monitor Azure file shares that are backed up by Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444732"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Gestire il backup di condivisione file di Azure con l'API RESTManage Azure File share backup with REST API

Questo articolo illustra come eseguire attività per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup da Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview)

## <a name="monitor-jobs"></a>Monitorare i processi

Il servizio Backup di Azure attiva i processi eseguiti in background. Sono inclusi scenari quali l'attivazione del backup, le operazioni di ripristino e la disabilitazione del backup. Questi processi possono essere registrati tramite i relativi ID.

### <a name="fetch-job-information-from-operations"></a>Recuperare le informazioni sui processi dalle operazioni

Un'operazione, ad esempio l'attivazione del backup, restituirà sempre un jobID nella risposta.

Ad esempio, la risposta finale di un'operazione [dell'API REST](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) di backup del trigger è la seguente:For example, the final response of a trigger backup REST API operation is as follows:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Il processo di backup della condivisione file di Azure è identificato dal campo **jobId** e può essere rilevato come indicato [qui](https://docs.microsoft.com/rest/api/backup/jobdetails/) usando una richiesta GET.

### <a name="tracking-the-job"></a>Tenere traccia del processo

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

Il "jobName" è il "jobId" menzionato in precedenza. La risposta è sempre "200 OK" con il campo **dello stato** che indica lo stato del processo. Una volta che è "Completed" o "CompletedWithWarnings", la sezione **extendedInfo** rivela ulteriori dettagli sul processo.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Risposta

Nome  | Type  |  Descrizione
--- | --- | ----
200 - OK |  JobResource  | OK

#### <a name="response-example"></a>Esempio di risposta

Una volta inviato l'URI *GET,* viene restituita una risposta 200.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Modifica dei criteri

Per modificare i criteri con cui è protetta la condivisione file, è possibile utilizzare lo stesso formato dell'abilitazione della protezione. È sufficiente fornire il nuovo ID criterio nel criterio di richiesta e inviare la richiesta.

Ad esempio: per modificare i criteri di protezione di *testshare* da *schedule1* a *schedule2,* specificare l'ID *schedule2* nel corpo della richiesta.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Arrestare la protezione dati, ma conservare i dati esistenti

È possibile rimuovere la protezione in una condivisione file protetta mantenendo i dati già sottoposti a backup. A tale scopo, rimuovere il criterio nel corpo della richiesta utilizzato per[abilitare](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) il backup e inviare la richiesta. Una volta rimossa l'associazione con i criteri, i backup non vengono più attivati e non vengono creati nuovi punti di ripristino.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Risposta di esempio

L'arresto della protezione per una condivisione file è un'operazione asincrona. L'operazione crea un'altra operazione di cui è necessario tenere traccia. Restituisce due risposte: 202 (Accettato) quando viene creata un'altra operazione e 200 al completamento dell'operazione.

Intestazione di risposta quando l'operazione viene accettata correttamente:Response header when operation is successfully accepted:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Tenere quindi traccia dell'operazione risultante usando l'intestazione location o l'intestazione Azure-AsyncOperation con un comando GET:Then track the resulting operation using the location header or Azure-AsyncOperation header with a GET command:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Corpo della risposta

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Arrestare la protezione ed eliminare i dati

Per rimuovere la protezione da una condivisione file protetta ed eliminare anche i dati di backup, eseguire un'operazione di eliminazione come descritto [qui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

I parametri "containerName" e "protectedItemName" sono impostati [qui](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname).

Nell'esempio seguente viene attivata un'operazione per arrestare la protezione per la condivisione file *testshare* protetta con *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Risposte

La protezione dell'eliminazione è un'operazione asincrona. L'operazione crea un'altra operazione che deve essere tenuta traccia separatamente.
Restituisce due risposte: 202 (Accettato) quando viene creata un'altra operazione e 204 (NoContent) al completamento dell'operazione.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [risolvere i problemi durante la configurazione del backup per le condivisioni file di Azure.](troubleshoot-azure-files.md)