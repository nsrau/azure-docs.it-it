---
title: Evento di errore di pianificazione dell'attività Azure Batch
description: Riferimento per l'evento di errore di pianificazione dell'attività batch. Questo evento viene generato quando un'attività non è stata pianificata e verrà ritentata in un secondo momento.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852139"
---
# <a name="task-schedule-fail-event"></a>Evento di errore pianificazione attività

 Questo evento viene generato quando non è possibile pianificare un'attività e verrà eseguito un nuovo tentativo in un secondo momento. Si tratta di un errore temporaneo durante la pianificazione delle attività a causa di una limitazione delle risorse, ad esempio slot insufficienti nei nodi per eseguire un'attività con `requiredSlots` specificato.

 Nell'esempio seguente viene illustrato il corpo di un evento di pianificazione dell'attività non riuscita.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`jobId`|string|ID del processo contenente l'attività.|
|`id`|string|ID dell'attività.|
|`taskType`|string|Tipo dell'attività. Il valore può essere "JobManager" per indicare che si tratta di un'attività del gestore di processi oppure 'User' per indicare che non si tratta di un'attività del gestore di processi. Questo evento non viene generato per le attività di preparazione del processo, le attività di rilascio del processo o le attività di avvio.|
|`systemTaskVersion`|Int32|Contatore dei tentativi interni di esecuzione di un'attività. Il servizio Batch può ritentare internamente l'esecuzione di un'attività in funzione di problemi transitori. Questi problemi possono includere errori interni di pianificazione o tentativi di ripristino a seguito di nodi di calcolo in uno stato non valido.|
|`requiredSlots`|Int32|Slot necessari per eseguire l'attività.|
|[`nodeInfo`](#nodeInfo)|Tipo complesso|Contiene informazioni sul nodo di calcolo in cui è stata eseguita l'attività.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complesso|Specifica che l'attività è un'attività con istanze multiple che richiede più nodi di calcolo.  Per informazioni dettagliate, vedere [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task).|
|[`constraints`](#constraints)|Tipo complesso|Vincoli di esecuzione che si applicano a questa attività.|
|[`schedulingError`](#schedulingError)|Tipo complesso|Contiene informazioni sull'errore di pianificazione dell'attività.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`poolId`|string|ID del pool in cui viene eseguita l'attività.|
|`nodeId`|string|ID del nodo in cui viene eseguita l'attività.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Numero di nodi di calcolo richiesti dall'attività.|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Numero massimo di tentativi consentiti per l'attività. Il servizio Batch ripete un'attività se il relativo codice di uscita è diverso da zero.<br /><br /> Si noti che questo valore controlla specificamente il numero di tentativi. Il servizio Batch eseguirà l'attività una volta e quindi ripeterà l'esecuzione fino al limite di tentativi specificato. Ad esempio, se il numero massimo di tentativi è 3, il servizio Batch eseguirà l'attività 4 volte, ovvero una iniziale e 3 ulteriori tentativi.<br /><br /> Se il numero massimo di tentativi è 0, il servizio Batch non eseguirà ulteriori tentativi.<br /><br /> Se il numero massimo di tentativi è -1, il servizio Batch continuerà a eseguire tentativi senza limiti.<br /><br /> Il valore predefinito è 0, ovvero nessun tentativo.|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`category`|string|Categoria dell'errore.|
|`code`|string|Identificatore dell'errore di pianificazione dell'attività. I codici sono invariabili e sono progettati per essere usati a livello di codice.|
|`message`|string|Messaggio che descrive l'errore di pianificazione dell'attività, che deve essere adatto per la visualizzazione in un'interfaccia utente.|
