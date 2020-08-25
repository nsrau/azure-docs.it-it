---
title: Eseguire il backup delle condivisioni file di Azure con l'API REST
description: Informazioni su come usare l'API REST per eseguire il backup di condivisioni file di Azure nell'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: b3d83104b699740c43b0c6506c00694c2b2ac063
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757133"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Eseguire il backup di una condivisione file di Azure con backup di Azure tramite l'API REST

Questo articolo descrive come eseguire il backup di una condivisione file di Azure usando backup di Azure tramite l'API REST.

Questo articolo presuppone che sia già stato creato un insieme di credenziali dei servizi di ripristino e un criterio per la configurazione del backup per la condivisione file. Se non lo si è fatto, fare riferimento alle esercitazioni [creare](./backup-azure-arm-userestapi-createorupdatevault.md) l'insieme di credenziali e [creare criteri](./backup-azure-arm-userestapi-createorupdatepolicy.md) per l'API REST per creare nuovi insiemi di credenziali e criteri.

Per questo articolo, verranno usate le risorse seguenti:

- **Recoveryservicesvault fino**: *azurefilesvault*

- **Criterio:** *schedule1*

- **Gruppo di risorse**: *risorsa*

- **Account di archiviazione**: *testvault2*

- **Condivisione file**: *TestShare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Configurare il backup per una condivisione file di Azure non protetta usando l'API REST

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Individuare gli account di archiviazione con condivisioni file di Azure non protette

L'insieme di credenziali deve individuare tutti gli account di archiviazione di Azure nella sottoscrizione con condivisioni file di cui è possibile eseguire il backup nell'insieme di credenziali di servizi di ripristino. Questa azione viene attivata tramite l'[operazione di aggiornamento](/rest/api/backup/protectioncontainers/refresh), Si tratta di un'operazione *post* asincrona che garantisce che l'insieme di credenziali ottenga l'elenco più recente di tutte le condivisioni file di Azure non protette nella sottoscrizione corrente è le memorizza nella cache. Una volta che la condivisione file è stata memorizzata nella cache, i servizi di ripristino possono accedere alla condivisione file e proteggerla.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

L'URI Post dispone `{subscriptionId}` di `{vaultName}` parametri,, `{vaultresourceGroupName}` e `{fabricName}` . In questo esempio, il valore per i diversi parametri sarà il seguente:

- `{fabricName}`*Azure*

- `{vaultName}`*azurefilesvault*

- `{vaultresourceGroupName}`*risorsa*

- $filter = backupManagementType EQ ' AzureStorage '

Poiché tutti i parametri obbligatori sono specificati nell'URI, non è necessario un corpo della richiesta separato.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Risposte all'operazione di aggiornamento

L'operazione di aggiornamento è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (accettato) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

##### <a name="example-responses-to-the-refresh-operation"></a>Risposte di esempio all'operazione di aggiornamento

Dopo che la richiesta *POST* è stata inviata, viene restituita una risposta 202 di accettazione.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Tenere traccia dell'operazione risultante tramite l'intestazione "Location" con un semplice comando *GET*.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Una volta individuati tutti gli account di archiviazione di Azure, il comando GET restituisce una risposta 200 (nessun contenuto). L'insieme di credenziali è ora in grado di individuare qualsiasi account di archiviazione con condivisioni file di cui è possibile eseguire il backup nella sottoscrizione.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Ottenere l'elenco di account di archiviazione con condivisioni file di cui è possibile eseguire il backup con l'insieme di credenziali di servizi di ripristino

Per confermare che la memorizzazione nella cache è stata eseguita, elencare tutti gli account di archiviazione nella sottoscrizione con le condivisioni file di cui è possibile eseguire il backup con l'insieme di credenziali di servizi di ripristino. Individuare quindi l'account di archiviazione desiderato nella risposta. Questa operazione viene eseguita tramite l'operazione [Get ProtectableContainers](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

All'URI *GET* sono associati tutti i parametri obbligatori. Non è necessario alcun corpo della richiesta aggiuntivo.

Esempio di corpo della risposta:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Poiché è possibile individuare l'account di archiviazione *testvault2* nel corpo della risposta con il nome descrittivo, l'operazione di aggiornamento eseguita in precedenza ha avuto esito positivo. L'insieme di credenziali di servizi di ripristino è ora in grado di individuare correttamente gli account di archiviazione con condivisioni di file non protette nella stessa sottoscrizione.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrare un account di archiviazione con l'insieme di credenziali di servizi

Questo passaggio è necessario solo se l'account di archiviazione non è stato registrato in precedenza con l'insieme di credenziali. È possibile registrare l'insieme di credenziali tramite l' [operazione ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Impostare le variabili per l'URI nel modo seguente:

- {resourceGroupName}- *risorsa*
- {fabricname}- *Azure*
- {VAULTNAME}- *azurefilesvault*
- {ContainerName}-questo è l'attributo Name nel corpo della risposta dell'operazione GET ProtectableContainers.
   In questo esempio è *StorageContainer; Archiviazione Risorsa; testvault2*

>[!NOTE]
> Prendere sempre l'attributo Name della risposta e compilare la richiesta. Non impostare come hardcoded o creare il formato del nome del contenitore. Se si crea o si imposta come hardcoded, la chiamata API avrà esito negativo se il formato del nome del contenitore cambia in futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Il corpo della richiesta di creazione è il seguente:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Per l'elenco completo delle definizioni del corpo della richiesta e altri dettagli, vedere [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Si tratta di un'operazione asincrona e restituisce due risposte: "202 accettate" quando l'operazione viene accettata e "200 OK" al termine dell'operazione.  Per tenere traccia dello stato dell'operazione, usare l'intestazione Location per ottenere lo stato più recente dell'operazione.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Esempio di corpo della risposta al termine dell'operazione:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

È possibile verificare se la registrazione è stata eseguita correttamente dal valore del parametro *RegistrationStatus* nel corpo della risposta. In questo caso, Mostra lo stato registrato per *testvault2*, in modo che l'operazione di registrazione abbia avuto esito positivo.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Chiedere a tutte le condivisioni file non protette in un account di archiviazione

È possibile richiedere informazioni sugli elementi da proteggere in un account di archiviazione usando l'operazione di [richiesta di contenitori di protezione](/rest/api/backup/protectioncontainers/inquire) . Si tratta di un'operazione asincrona e i risultati devono essere rilevati usando l'intestazione Location.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Impostare le variabili per l'URI precedente come indicato di seguito:

- {VAULTNAME}- *azurefilesvault*
- {fabricname}- *Azure*
- {ContainerName}: fare riferimento all'attributo Name nel corpo della risposta dell'operazione GET ProtectableContainers. In questo esempio è *StorageContainer; Archiviazione Risorsa; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Una volta che la richiesta ha avuto esito positivo, restituisce il codice di stato "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selezionare la condivisione file di cui si vuole eseguire il backup

È possibile elencare tutti gli elementi da proteggere nella sottoscrizione e individuare la condivisione file desiderata di cui eseguire il backup usando l'operazione [Get backupprotectableItems](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Costruire l'URI nel modo seguente:

- {VAULTNAME}- *azurefilesvault*
- {$filter}- *backupManagementType EQ ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Risposta di esempio:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

La risposta contiene l'elenco di tutte le condivisioni file non protette e contiene tutte le informazioni richieste dal servizio di ripristino di Azure per configurare il backup.

### <a name="enable-backup-for-the-file-share"></a>Abilita backup per la condivisione file

Quando la condivisione file pertinente viene "identificata" con il nome descrittivo, selezionare i criteri da proteggere. Per altre informazioni sui criteri esistenti nell'insieme di credenziali, vedere [elenco API dei criteri](/rest/api/backup/backuppolicies/list). e quindi selezionare i [criteri rilevanti](/rest/api/backup/protectionpolicies/get) facendo riferimento al nome dei criteri stessi. Per creare i criteri, vedere l'[esercitazione sulla creazione di criteri](./backup-azure-arm-userestapi-createorupdatepolicy.md).

L'abilitazione della protezione è un'operazione *put* asincrona che crea un "elemento protetto".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Impostare le variabili **containerName** e **protecteditemname** usando l'attributo ID nel corpo della risposta dell'operazione Get backupprotectableitems.

In questo esempio, l'ID della condivisione file che si vuole proteggere è:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {ContainerName}- *StorageContainer; storage; risorsa; testvault2*
- {protectedItemName}- *azurefileshare; TestShare*

In alternativa, è possibile fare riferimento all'attributo **Name** del contenitore di protezione e alle risposte degli elementi da proteggere.

>[!NOTE]
>Prendere sempre l'attributo Name della risposta e compilare la richiesta. Non impostare come hardcoded o creare il formato nome contenitore o nome elemento protetto. Se si crea o si imposta il codice come hardcoded, la chiamata API avrà esito negativo se il formato del nome del contenitore o il formato del nome dell'elemento protetto cambia in futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Creare un corpo della richiesta:

Il corpo della richiesta seguente definisce le proprietà necessarie per creare un elemento protetto.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**SourceResourceId** è **parentcontainerFabricID** in risposta a Get backupprotectableItems.

Risposta di esempio

La creazione di un elemento protetto è un'operazione asincrona che crea un'altra operazione che deve essere rilevata. Restituisce due risposte: 202 (accettato) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

Una volta inviata la richiesta *put* per la creazione o l'aggiornamento di un elemento protetto, la risposta iniziale è 202 (accettata) con un'intestazione Location.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Quindi tenere traccia dell'operazione risultante usando l'intestazione Location o Azure-AsyncOperation con un comando  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Dopo che è stata completata, l'operazione restituisce 200 (OK) con il contenuto dell'elemento protetto nel corpo della risposta.

Corpo della risposta di esempio:

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

Questo conferma che la protezione è abilitata per la condivisione file e il primo backup verrà attivato in base alla pianificazione dei criteri.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Attivare un backup su richiesta per la condivisione file

Una volta configurata una condivisione file di Azure per il backup, i backup vengono eseguiti in base alla pianificazione dei criteri. È possibile attendere il primo backup pianificato o attivare un backup su richiesta in qualsiasi momento.

L'attivazione di un backup su richiesta è un'operazione POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{ContainerName} e {protectedItemName} sono costruiti sopra durante l'abilitazione del backup. Per questo esempio, la situazione è quindi la seguente:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Creare il corpo della richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per attivare un backup su richiesta.

| Nome       | Tipo                       | Descrizione                       |
| ---------- | -------------------------- | --------------------------------- |
| Proprietà | AzurefilesharebackupReques | Proprietà di BackupRequestResource |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento sull'API REST per attivare il backup di elementi protetti](/rest/api/backup/backups/trigger#request-body).

Esempio di corpo della richiesta

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Risposte all'operazione di backup su richiesta

L'attivazione di un backup su richiesta è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (accettato) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Risposte di esempio all'operazione di backup su richiesta

Dopo aver inviato la richiesta *POST* per un backup su richiesta, la risposta iniziale è 202 (accettazione) con un'intestazione location o Azure-async.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Quindi tenere traccia dell'operazione risultante usando l'intestazione Location o Azure-AsyncOperation con un comando  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Al termine dell'operazione, viene restituita la risposta 200 (OK) con l'ID del processo di backup risultante nel corpo della risposta.

#### <a name="sample-response-body"></a>Esempio di corpo della risposta

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Poiché il processo di backup è un'operazione con esecuzione prolungata, ne deve essere tenuta traccia come illustrato nel [documento per monitorare i processi usando l'API REST](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [ripristinare le condivisioni file di Azure usando l'API REST](restore-azure-file-share-rest-api.md).
