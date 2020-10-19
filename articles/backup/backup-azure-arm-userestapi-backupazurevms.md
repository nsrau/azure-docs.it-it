---
title: Eseguire il backup di macchine virtuali di Azure con l'API REST
description: Questo articolo illustra come configurare, avviare e gestire le operazioni di backup del backup delle macchine virtuali di Azure usando l'API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171462"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Eseguire il backup di una macchina virtuale di Azure con Backup di Azure tramite l'API REST

Questo articolo descrive come gestire i backup per una macchina virtuale di Azure con Backup di Azure tramite l'API REST. Configurare la protezione per la prima volta per una macchina virtuale di Azure precedentemente non protetta, attivare un backup su richiesta per una macchina virtuale di Azure protetta e modificare le proprietà di backup di una macchina virtuale di cui è stato eseguito il backup tramite l'API REST, come illustrato qui.

Per creare nuovi insiemi di credenziali e nuovi criteri, vedere le esercitazioni sull'API REST per [creare insiemi di credenziali](backup-azure-arm-userestapi-createorupdatevault.md) e per [creare criteri](backup-azure-arm-userestapi-createorupdatepolicy.md).

Scenario: si vuole proteggere una macchina virtuale "testVM" in un gruppo di risorse "testRG" in un insieme di credenziali di Servizi di ripristino "testVault", presente nel gruppo di risorse "testVaultRG", con i criteri predefiniti (denominati "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configurare il backup di una macchina virtuale di Azure non protetta con l'API REST

### <a name="discover-unprotected-azure-vms"></a>Individuare le macchine virtuali di Azure non protette

In primo luogo l'insieme di credenziali deve essere in grado di identificare la macchina virtuale di Azure. Questa azione viene attivata tramite l'[operazione di aggiornamento](/rest/api/backup/protectioncontainers/refresh), Si tratta di un'operazione *post*  asincrona che verifica che l'insieme di credenziali ottenga l'elenco più recente di tutte le macchine virtuali non protette nella sottoscrizione corrente e le memorizza nella cache. Dopo che la macchina virtuale è stata memorizzata nella cache, la funzionalità Servizi di ripristino sarà in grado di accedere alla macchina virtuale e di proteggerla.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Per l'URI di POST sono presenti i parametri `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}`. Il valore di `{fabricName}` è "Azure". In base all'esempio, `{vaultName}` è "testVault" e `{vaultresourceGroupName}` è "testVaultRG". Poiché tutti i parametri obbligatori sono specificati nell'URI, non è necessario un corpo della richiesta separato.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Risposte all'operazione di aggiornamento

L'operazione di aggiornamento è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (Accettata) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|204 No Content (Nessun contenuto)     |         |  OK senza alcun contenuto restituito      |
|202 - Accettato     |         |     Accettato    |

##### <a name="example-responses-to-refresh-operation"></a>Risposte di esempio all'operazione di aggiornamento

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Tenere traccia dell'operazione risultante tramite l'intestazione "Location" con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
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

 È possibile verificare che la memorizzazione nella cache venga eseguita [elencando tutti gli elementi da proteggere](/rest/api/backup/backupprotectableitems/list) nella sottoscrizione e individuare la macchina virtuale desiderata nella risposta. [La risposta di questa operazione](#example-responses-to-get-operation) fornisce anche informazioni sul modo in cui i servizi di ripristino identificano una macchina virtuale.  Dopo aver acquisito familiarità con il modello, è possibile ignorare questo passaggio e procedere direttamente con l'[abilitazione della protezione](#enabling-protection-for-the-azure-vm).

Questa è un'operazione *GET*.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

All'URI *GET* sono associati tutti i parametri obbligatori. Non è necessario alcun corpo della richiesta aggiuntivo.

#### <a name="responses-to-get-operation"></a>Risposte per l'operazione Get

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Risposte di esempio per l'operazione Get

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

Dopo che la macchina virtuale di Azure è stata memorizzata nella cache e identificata, selezionare i criteri per la protezione. Per altre informazioni sui criteri esistenti, vedere l'[elenco di API dei criteri](/rest/api/backup/backuppolicies/list) e quindi selezionare i [criteri rilevanti](/rest/api/backup/protectionpolicies/get) facendo riferimento al nome dei criteri stessi. Per creare i criteri, vedere l'[esercitazione sulla creazione di criteri](backup-azure-arm-userestapi-createorupdatepolicy.md). Nell'esempio seguente è selezionato "DefaultPolicy".

L'abilitazione della protezione è un'operazione *PUT* asincrona che crea un elemento protetto.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati in precedenza. Il valore di `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Creare il corpo della richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per creare un elemento protetto.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Proprietà delle risorse ProtectedItem         |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento per creare l'API REST dell'elemento protetto](/rest/api/backup/protecteditems/createorupdate#request-body).

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

`{sourceResourceId}` è l'elemento `{virtualMachineId}` citato in precedenza per dalla [risposta per l'elenco di elementi che è possibile proteggere](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Risposte per creare un'operazione di elemento protetto

La creazione di un elemento protetto è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (Accettata) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 - Accettato     |         |     Accettato    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Risposte di esempio per creare un'operazione di elemento protetto

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

Tenere quindi traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
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

In questo modo viene confermata l'abilitazione della protezione per la macchina virtuale e il primo backup viene attivato in base alla pianificazione dei criteri.

### <a name="excluding-disks-in-azure-vm-backup"></a>Esclusione di dischi nel backup di macchine virtuali di Azure

Backup di Azure consente anche di eseguire il backup in modo selettivo di un subset di dischi nella macchina virtuale di Azure. Altre informazioni sono disponibili [qui](selective-disk-backup-restore.md). Per eseguire il backup selettivo di pochi dischi durante l'abilitazione della protezione, il frammento di codice seguente deve essere il [corpo della richiesta durante l'abilitazione della protezione](#example-request-body).

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

Nel corpo della richiesta precedente, l'elenco dei dischi di cui eseguire il backup viene fornito nella sezione proprietà estese.

|Proprietà  |valore  |
|---------|---------|
|diskLunList     | L'elenco di LUN del disco è un elenco di *lun di dischi dati*. **Viene sempre eseguito il backup del disco del sistema operativo e non è necessario citarlo**.        |
|Di inclusione     | Deve essere **true** per i LUN da includere durante il backup. Se è **false**, i LUN sopra indicati verranno esclusi.         |

Quindi, se il requisito consiste nel eseguire il backup solo del disco del sistema operativo, è necessario escludere _tutti_ i dischi dati. Un modo più semplice è quello di indicare che non deve essere incluso alcun disco dati. Quindi, l'elenco di LUN del disco sarà vuoto e l'elenco di **inclusione** sarà **true**. Analogamente, è possibile considerare il modo più semplice per selezionare un subset: è sempre necessario escludere alcuni dischi oppure è sempre necessario includere alcuni dischi. Scegliere di conseguenza l'elenco di LUN e il valore della variabile booleana.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Attivare un backup su richiesta per una macchina virtuale di Azure protetta

Una volta configurata una macchina virtuale di Azure per il backup, i backup vengono eseguiti in base alla pianificazione dei criteri. È possibile attendere il primo backup pianificato o attivare un backup su richiesta in qualsiasi momento. La conservazione dei backup su richiesta è separata da quella dei criteri di backup e può essere specificata per una determinata data e ora. Se non specificato, si presuppone che la durata della conservazione sia di 30 giorni a partire dal giorno del trigger di backup su richiesta.

L'attivazione di un backup su richiesta è un'operazione *POST*.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati [in precedenza](#responses-to-get-operation). Il valore di `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Creare il corpo della richiesta per il backup su richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per attivare un backup su richiesta.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Proprietà BackupRequestResource         |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento sull'API REST per attivare il backup di elementi protetti](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Corpo della richiesta di esempio per il backup su richiesta

Il corpo della richiesta seguente definisce le proprietà necessarie per attivare un backup per un elemento protetto. Se la conservazione non è specificata, verrà mantenuta per 30 giorni dall'ora del trigger del processo di backup.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Risposte per il backup su richiesta

L'attivazione di un backup su richiesta è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (Accettata) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|202 - Accettato     |         |     Accettato    |

#### <a name="example-responses-for-on-demand-backup"></a>Risposte di esempio per il backup su richiesta

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
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Tenere quindi traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

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

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Il formato della risposta è analogo a quello indicato [per l'abilitazione della protezione](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Esclusione di dischi durante la protezione delle macchine virtuali di Azure

Se è già stato eseguito il backup della macchina virtuale di Azure, è possibile specificare l'elenco dei dischi di cui eseguire il backup o l'esclusione modificando il criterio di protezione. È sufficiente preparare la richiesta nello stesso formato in cui [escludere i dischi durante l'abilitazione della protezione](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> Il corpo della richiesta precedente è sempre la copia finale dei dischi dati da escludere o includere. Questa operazione non viene *aggiunta* alla configurazione precedente. Ad esempio, se si aggiorna la protezione per la prima volta come "Escludi disco dati 1" e si ripete con "Escludi disco dati 2", *solo i dati del disco 2 verranno esclusi* nei backup successivi e verrà incluso il disco dati 1. Si tratta sempre dell'elenco finale che verrà incluso/escluso nei backup successivi.

Per ottenere l'elenco corrente dei dischi esclusi o inclusi, ottenere le informazioni sull'elemento protetto come indicato [qui](/rest/api/backup/protecteditems/get). La risposta fornirà l'elenco dei LUN del disco dati e indicherà se sono inclusi o esclusi.

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

Il formato della risposta è analogo a quello indicato [per attivare un backup su richiesta](#example-responses-for-on-demand-backup). Il processo risultante deve essere rilevato come illustrato nel [documento per monitorare i processi usando l'API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Arrestare la protezione ed eliminare i dati

Per rimuovere la protezione in una macchina virtuale protetta ed eliminare anche i dati di backup, eseguire un'operazione di eliminazione come descritto in dettaglio [qui](/rest/api/backup/protecteditems/delete).

L'arresto della protezione e l'eliminazione dei dati sono operazioni *DELETE*.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Gli elementi `{containerName}` e `{protectedItemName}` sono come creati [in precedenza](#responses-to-get-operation). `{fabricName}` è "Azure". Per questo esempio, la situazione è quindi la seguente:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Risposte per la protezione dell'eliminazione

L'operazione *DELETE* applicata alla protezione è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (accettazione) quando viene creata un'altra operazione e 204 (nessun contenuto restituito) quando tale operazione viene completata.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|204 NoContent (Nessun contenuto)     |         |  Nessun contenuto restituito       |
|202 - Accettato     |         |     Accettato    |

> [!IMPORTANT]
> Per garantire la protezione da scenari di eliminazione accidentale, è [disponibile una funzionalità di eliminazione](use-restapi-update-vault-properties.md#soft-delete-state) temporanea per l'insieme di credenziali di servizi di ripristino. Se lo stato di eliminazione temporanea dell'insieme di credenziali è impostato su abilitato, l'operazione di eliminazione non eliminerà immediatamente i dati. Verranno conservati per 14 giorni e quindi eliminati definitivamente. Non viene addebitato alcun costo per l'archiviazione per questo periodo di 14 giorni. Per annullare l'operazione di eliminazione, fare riferimento alla [sezione Undo-Delete](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Annulla l'eliminazione

L'eliminazione accidentale è simile alla creazione dell'elemento di backup. Dopo aver annullato l'eliminazione, l'elemento viene mantenuto, ma non vengono attivati backup futuri.

L'eliminazione Annulla è un'operazione *put* che è molto simile alla [modifica dei criteri](#changing-the-policy-of-protection) e/o [all'abilitazione della protezione](#enabling-protection-for-the-azure-vm). È sufficiente fornire l'intenzione di annullare l'eliminazione con la variabile *isRehydrate*  nel [corpo della richiesta](#example-request-body) e inviare la richiesta. Ad esempio: per annullare l'eliminazione per testVM, è necessario usare il corpo della richiesta seguente.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Il formato della risposta è analogo a quello indicato [per attivare un backup su richiesta](#example-responses-for-on-demand-backup). Il processo risultante deve essere rilevato come illustrato nel [documento per monitorare i processi usando l'API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Passaggi successivi

[Restore data from an Azure Virtual machine backup](backup-azure-arm-userestapi-restoreazurevms.md) (Ripristinare i dati da un backup di macchina virtuale di Azure).

Per altre informazioni sulle API REST di Backup di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)