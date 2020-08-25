---
title: ripristinare le macchine virtuali di Azure con l'API REST
description: Questo articolo illustra come gestire le operazioni di ripristino del backup delle macchine virtuali di Azure usando l'API REST.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 03815cd9956e323583d6d66097e0eeaa3d492f44
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826362"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Ripristinare le macchine virtuali di Azure con l'API REST

Una volta completato il backup di una macchina virtuale di Azure con backup di Azure, è possibile ripristinare intere macchine virtuali o dischi o file di Azure dalla stessa copia di backup. Questo articolo descrive come ripristinare una macchina virtuale di Azure oppure dischi tramite l'API REST.

Per qualsiasi operazione di ripristino, è necessario identificare innanzitutto il punto di ripristino pertinente.

## <a name="select-recovery-point"></a>Selezione di un punto di ripristino

I punti di ripristino disponibili di un elemento di backup possono essere elencati usando l'[API REST per elencare i punti di ripristino](/rest/api/backup/recoverypoints/list). Si tratta di una semplice operazione *GET* con tutti i valori pertinenti.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` e `{protectedItemName}` sono come creati [qui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` è "Azure".

All'URI *GET* sono associati tutti i parametri obbligatori. Non è necessario un corpo della richiesta aggiuntivo.

### <a name="responses"></a>Risposte

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|200 - OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Risposta di esempio

Dopo che l'URI *GET* è stato inviato, viene restituita una risposta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Il punto di ripristino viene identificato con il campo `{name}` nella risposta precedente.

## <a name="restore-operations"></a>Operazioni di ripristino

Dopo aver selezionato il [punto di ripristino pertinente](#select-recovery-point), procedere con l'attivazione dell'operazione di ripristino.

***Tutte le operazioni di ripristino sull'elemento di backup vengono eseguite con la stessa API *post* . Solo il corpo della richiesta cambia con gli scenari di ripristino.***

> [!IMPORTANT]
> Di [seguito](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)sono indicati tutti i dettagli sulle varie opzioni di ripristino e le relative dipendenze. Prima di procedere con l'attivazione di queste operazioni, vedere.

L'attivazione delle operazioni di ripristino è una richiesta *post* . Per altre informazioni sull'API, vedere l' [API REST "trigger Restore"](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` e `{protectedItemName}` sono come creati [qui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` è "Azure" e `{recoveryPointId}` è il campo `{name}` del punto di ripristino indicato [in precedenza](#example-response).

Una volta ottenuto il punto di ripristino, è necessario creare il corpo della richiesta per lo scenario di ripristino pertinente. Le sezioni seguenti descrivono il corpo della richiesta per ogni scenario.

- [Ripristinare i dischi](#restore-disks)
- [Sostituisci dischi](#replace-disks-in-a-backed-up-virtual-machine)
- [Ripristina come nuova macchina virtuale](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Ripristina risposta

L'attivazione di qualsiasi operazione di ripristino è un' [operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

L'operazione restituisce due risposte: 202 (Accettata) quando viene creata un'altra operazione e 200 (OK) quando tale operazione viene completata.

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|202 - Accettato     |         |     Accettato    |

#### <a name="example-responses"></a>Risposte di esempio

Dopo aver inviato l'URI *POST* per l'attivazione dei dischi di ripristino, la risposta iniziale è 202 (Accettata) con intestazione location o Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Tenere quindi traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Al termine dell'operazione, viene restituita la risposta 200 (OK) con l'ID del processo di ripristino risultante nel corpo della risposta.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Poiché il processo di ripristino è un'operazione a esecuzione prolungata, deve essere rilevata come illustrato nel [documento monitorare i processi tramite l'API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Ripristinare i dischi

Se è necessario personalizzare la creazione di una macchina virtuale dai dati di backup, è possibile ripristinare solo i dischi in un account di archiviazione scelto e creare una VM da tali dischi in base ai requisiti. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali di servizi di ripristino e non deve essere con ridondanza della zona. I dischi, così come la configurazione della macchina virtuale di cui è stato eseguito il backup ("vmconfig.json"), verranno archiviati nell'account di archiviazione specificato. Come spiegato in [precedenza](#restore-operations), il corpo della richiesta pertinente per i dischi di ripristino è riportato di seguito.

#### <a name="create-request-body"></a>Creare il corpo della richiesta

Di seguito vengono indicati i componenti del corpo della richiesta necessari per attivare il ripristino di un disco da un backup di macchine virtuali di Azure.

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento sull'API REST per attivare il ripristino](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Richiesta di esempio

Il corpo della richiesta seguente definisce le proprietà necessarie per attivare il ripristino di un disco.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

Quando si tiene traccia della risposta come illustrato in [precedenza](#responses)e il processo a esecuzione prolungata è completo, i dischi e la configurazione della macchina virtuale di cui è stato eseguito il backup ("VMConfig.json") saranno presenti nell'account di archiviazione specificato.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Sostituire i dischi in una macchina virtuale di cui è stato eseguito il backup

Mentre i dischi di ripristino creano dischi dal punto di ripristino, Replace disks sostituisce i dischi correnti della VM di cui è stato eseguito il backup con i dischi dal punto di ripristino. Come spiegato in [precedenza](#restore-operations), il corpo della richiesta pertinente per la sostituzione dei dischi è riportato di seguito.

#### <a name="create-request-body"></a>Creare il corpo della richiesta

Per attivare una sostituzione del disco da un backup di VM di Azure, di seguito sono riportati i componenti del corpo della richiesta.

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Per l'elenco completo di definizioni del corpo della richiesta e altri dettagli, vedere il [documento sull'API REST per attivare il ripristino](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Richiesta di esempio

Il corpo della richiesta seguente definisce le proprietà necessarie per attivare il ripristino di un disco.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Eseguire il ripristino come un'altra macchina virtuale

Come spiegato in [precedenza](#restore-operations), il corpo della richiesta seguente definisce le proprietà necessarie per attivare un ripristino della macchina virtuale.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

La risposta deve essere gestita nello stesso modo [descritto in precedenza per il ripristino dei dischi](#responses).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API REST di Backup di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
