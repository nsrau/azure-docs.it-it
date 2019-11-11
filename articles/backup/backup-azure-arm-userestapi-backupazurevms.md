---
title: "Backup di Azure: eseguire il backup di macchine virtuali di Azure con l'API REST"
description: Questo articolo illustra come configurare, avviare e gestire le operazioni di backup del backup delle macchine virtuali di Azure usando l'API REST.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: API REST, backup di macchine virtuali di Azure, ripristino di macchine virtuali di Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: dacurwin
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 39ab0caa31f79aca41f2c40a3220412e0683ee22
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903156"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Eseguire il backup di una macchina virtuale di Azure con Backup di Azure tramite l'API REST

Questo articolo descrive come gestire i backup per una macchina virtuale di Azure con Backup di Azure tramite l'API REST. Configurare la protezione per la prima volta per una macchina virtuale di Azure precedentemente non protetta, attivare un backup su richiesta per una macchina virtuale di Azure protetta e modificare le proprietà di backup di una macchina virtuale di cui è stato eseguito il backup tramite l'API REST, come illustrato qui.

Per creare nuovi insiemi di credenziali e nuovi criteri, vedere le esercitazioni sull'API REST per [creare insiemi di credenziali](backup-azure-arm-userestapi-createorupdatevault.md) e per [creare criteri](backup-azure-arm-userestapi-createorupdatepolicy.md).

Scenario: si vuole proteggere una macchina virtuale "testVM" in un gruppo di risorse "testRG" in un insieme di credenziali di Servizi di ripristino "testVault", presente nel gruppo di risorse "testVaultRG", con i criteri predefiniti (denominati "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configurare il backup di una macchina virtuale di Azure non protetta con l'API REST

### <a name="discover-unprotected-azure-vms"></a>Individuare le macchine virtuali di Azure non protette

In primo luogo l'insieme di credenziali deve essere in grado di identificare la macchina virtuale di Azure. Questa azione viene attivata tramite l'[operazione di aggiornamento](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh), ovvero un'operazione *POST*  asincrona che garantisce che l'insieme di credenziali ottenga l'elenco più recente di tutte le macchine virtuali non protette della sottoscrizione corrente e che le memorizzi nella cache. Dopo che la macchina virtuale è stata memorizzata nella cache, la funzionalità Servizi di ripristino sarà in grado di accedere alla macchina virtuale e di proteggerla.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Per l'URI di POST sono presenti i parametri `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}`. Il valore di `{fabricName}` è "Azure". Come indicato nell'esempio, il valore di `{vaultName}` è "testVault" e il valore di `{vaultresourceGroupName}` è "testVaultRG". Tutti i parametri obbligatori vengono specificati nell'URI e di conseguenza il corpo di una richiesta separata non è necessario.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Risposte

L'operazione di aggiornamento è un'[operazione asincrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (Accettato) quando viene creata un'altra operazione e quindi 200 (OK) quando tale operazione viene completata.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|204 No Content (Nessun contenuto)     |         |  OK senza alcun contenuto restituito      |
|202 - Accettato     |         |     Accepted    |

##### <a name="example-responses"></a>Risposte di esempio

Dopo che la richiesta *POST* è stata inviata, viene restituita una risposta 202 di accettazione.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Tenere traccia dell'operazione risultante tramite l'intestazione "Location" con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Quando vengono individuate tutte le macchine virtuali di Azure, il comando GET restituisce una risposta 204 che non prevede la restituzione di alcun contenuto. L'insieme di credenziali è ora in grado di individuare tutte le macchine virtuali presenti nella sottoscrizione.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Selezione della macchina virtuale di Azure pertinente

 È possibile verificare che la memorizzazione nella cache venga eseguita [elencando tutti gli elementi da proteggere](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) nella sottoscrizione e individuare la macchina virtuale desiderata nella risposta. [La risposta di questa operazione](#example-responses-1) fornisce anche informazioni sul modo in cui i servizi di ripristino identificano una macchina virtuale.  Dopo aver acquisito familiarità con il modello, è possibile ignorare questo passaggio e procedere direttamente con l'[abilitazione della protezione](#enabling-protection-for-the-azure-vm).

Questa è un'operazione *GET*.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

All'URI *GET* sono associati tutti i parametri obbligatori. Non è necessario alcun corpo della richiesta aggiuntivo.

#### <a name="responses-1"></a>Risposte

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|200 - OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-1"></a>Risposte di esempio

Dopo che la richiesta *GET* è stata inviata, viene restituita una risposta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Il numero di valori in una risposta *GET* è limitato a 200 per pagina. Usare il campo "nextLink" per ottenere l'URL per il set successivo di risposte.

La risposta contiene l'elenco di tutte le macchine virtuali di Azure non protette e ogni elemento `{value}` contiene tutte le informazioni necessarie dal servizio di ripristino di Azure per configurare il backup. Per configurare il backup, si notino i campi `{name}` e `{virtualMachineId}` nella sezione `{properties}`. Creare due variabili a partire dai valori di questi campi come indicato di seguito.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` viene usato in seguito nel [corpo della richiesta](#example-request-body)

Nell'esempio in valori precedenti vengono convertiti in:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Abilitazione della protezione per la macchina virtuale di Azure

Dopo che la macchina virtuale di Azure è stata memorizzata nella cache e identificata, selezionare i criteri per la protezione. Per altre informazioni sui criteri esistenti, vedere l'[elenco di API dei criteri](https://docs.microsoft.com/rest/api/backup/backuppolicies/list) e quindi selezionare i [criteri rilevanti](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/get) facendo riferimento al nome dei criteri stessi. Per creare i criteri, vedere l'[esercitazione sulla creazione di criteri](backup-azure-arm-userestapi-createorupdatepolicy.md). Nell'esempio seguente è selezionato "DefaultPolicy".

L'abilitazione della protezione è un'operazione *PUT* asincrona che crea un elemento protetto.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati in precedenza. Il valore di `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Creare il corpo della richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per creare un elemento protetto.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Proprietà delle risorse ProtectedItem         |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento per creare l'API REST dell'elemento protetto](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo della richiesta seguente definisce le proprietà necessarie per creare un elemento protetto.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` è l'elemento `{virtualMachineId}` citato in precedenza per dalla [risposta per l'elenco di elementi che è possibile proteggere](#example-responses-1).

#### <a name="responses"></a>Risposte

La creazione di un elemento protetto è un'[operazione asincrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (Accettato) quando viene creata un'altra operazione e quindi 200 (OK) quando tale operazione viene completata.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|200 - OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 - Accettato     |         |     Accepted    |

##### <a name="example-responses"></a>Risposte di esempio

Dopo aver inviato la richiesta *PUT* per la creazione o l'aggiornamento dell'elemento protetto, la risposta iniziale è 202 (accettazione) con intestazione location o Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Quindi tenere traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Dopo che è stata completata, l'operazione restituisce 200 (OK) con il contenuto dell'elemento protetto nel corpo della risposta.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

In questo modo si conferma che la protezione viene abilitata per la macchina virtuale e che il primo backup viene attivato in base alla pianificazione dei criteri.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Attivare un backup su richiesta per una macchina virtuale di Azure protetta

Dopo che una macchina virtuale di Azure viene configurata per il backup, i backup vengono eseguiti in base alla pianificazione dei criteri. È possibile attendere il primo backup pianificato o attivare un backup su richiesta in qualsiasi momento. La conservazione dei backup su richiesta è separata da quella dei criteri di backup e può essere specificata per una determinata data e ora. Se non specificato, si presuppone che la durata della conservazione sia di 30 giorni a partire dal giorno del trigger di backup su richiesta.

L'attivazione di un backup su richiesta è un'operazione *POST*.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati [in precedenza](#responses-1). Il valore di `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2019-05-13
```

### <a name="create-the-request-body"></a>Creare il corpo della richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per attivare un backup su richiesta.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Proprietà BackupRequestResource         |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento sull'API REST per attivare il backup di elementi protetti](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo della richiesta seguente definisce le proprietà necessarie per attivare un backup per un elemento protetto. Se non specificato, si presuppone che la durata della conservazione sia di 30 giorni a partire dal giorno del trigger del processo di backup.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Risposte

L'attivazione di un backup su richiesta è un'[operazione asincrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (Accettato) quando viene creata un'altra operazione e quindi 200 (OK) quando tale operazione viene completata.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|202 - Accettato     |         |     Accepted    |

#### <a name="example-responses-3"></a>Risposte di esempio

Dopo aver inviato la richiesta *POST* per un backup su richiesta, la risposta iniziale è 202 (accettazione) con un'intestazione location o Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Quindi tenere traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Al termine dell'operazione, viene restituita la risposta 200 (OK) con l'ID del processo di backup risultante nel corpo della risposta.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Poiché il processo di backup è un'operazione con esecuzione prolungata, ne deve essere tenuta traccia come illustrato nel [documento per monitorare i processi usando l'API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modificare la configurazione di backup per una macchina virtuale di Azure protetta

### <a name="changing-the-policy-of-protection"></a>Modifica dei criteri di protezione

Per modificare i criteri con cui la macchina virtuale è protetta, è possibile usare lo stesso formato usato per [l'abilitazione della protezione](#enabling-protection-for-the-azure-vm). È sufficiente fornire il nuovo ID criteri nel [corpo della richiesta](#example-request-body) e inviare la richiesta. Ad esempio: per modificare i criteri di testVM da' DefaultPolicy ' a' ProdPolicy ', specificare l'ID ' ProdPolicy ' nel corpo della richiesta.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Il formato della risposta è analogo a quello indicato [per l'abilitazione della protezione](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Arrestare la protezione dati, ma conservare i dati esistenti

Per rimuovere la protezione in una macchina virtuale protetta conservando contemporaneamente i dati di cui è stato eseguito il backup, rimuovere i criteri dal corpo della richiesta e inviare la richiesta stessa. Dopo che l'associazione ai criteri è stata rimossa, i backup non vengono più attivati e non viene creato alcun nuovo punto di ripristino.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Il formato della risposta è analogo a quello indicato [per attivare un backup su richiesta](#example-responses-3). Il processo risultante deve essere rilevato come illustrato nel [documento per monitorare i processi usando l'API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Arrestare la protezione ed eliminare i dati

Per rimuovere la protezione in una macchina virtuale protetta ed eliminare anche i dati di backup, eseguire un'operazione di eliminazione come descritto in dettaglio [qui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

L'arresto della protezione e l'eliminazione dei dati sono operazioni *DELETE*.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati [in precedenza](#responses-1). `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

### <a name="responses-2"></a>Risposte

L'operazione *DELETE* applicata alla protezione è un'[operazione asincrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (accettazione) quando viene creata un'altra operazione e 204 (nessun contenuto restituito) quando tale operazione viene completata.

|Nome  |digitare  |DESCRIZIONE  |
|---------|---------|---------|
|204 NoContent (Nessun contenuto)     |         |  Nessun contenuto restituito       |
|202 - Accettato     |         |     Accepted    |

## <a name="next-steps"></a>Passaggi successivi

[Restore data from an Azure Virtual machine backup](backup-azure-arm-userestapi-restoreazurevms.md) (Ripristinare i dati da un backup di macchina virtuale di Azure).

Per altre informazioni sulle API REST di Backup di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
