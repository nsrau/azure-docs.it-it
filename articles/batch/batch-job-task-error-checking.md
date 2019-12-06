---
title: Verificare la presenza di errori relativi a processi e attività-Azure Batch | Microsoft Docs
description: Errori per verificare e risolvere i problemi relativi a processi e attività
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 12/01/2019
ms.author: markscu
ms.openlocfilehash: c4e36d76bf85b9715a817dbeb7c690aa77f8d978
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852185"
---
# <a name="job-and-task-error-checking"></a>Controllo degli errori di attività e processi

Quando si aggiungono processi e attività, possono verificarsi diversi errori. Il rilevamento degli errori per queste operazioni è semplice perché tutti gli errori vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.  Tuttavia, esistono errori che possono verificarsi in un secondo momento durante la pianificazione e l'esecuzione dei processi e delle attività.

Questo articolo descrive gli errori che possono verificarsi dopo l'invio dei processi e delle attività. Vengono elencati e illustrati gli errori che devono essere controllati e gestiti.

## <a name="jobs"></a>Processi

Un processo è un raggruppamento di una o più attività, ovvero le attività che specificano effettivamente le righe di comando da eseguire.

Quando si aggiunge un processo, è possibile specificare i parametri seguenti che possono influenzare il modo in cui il processo può avere esito negativo:

- [Vincoli di processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Facoltativamente, è possibile specificare la proprietà `maxWallClockTime` per impostare la quantità massima di tempo in cui un processo può essere attivo o in esecuzione. Se superata, il processo verrà terminato con la proprietà `terminateReason` impostata in [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) per il processo.
- [Attività di preparazione del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se specificato, viene eseguita un'attività di preparazione del processo la prima volta che un'attività viene eseguita per un processo in un nodo. L'attività di preparazione del processo può non riuscire, il che comporta l'esecuzione dell'attività e il processo non viene completato.
- [Attività di rilascio del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - È possibile specificare un'attività di rilascio del processo solo se è configurata un'attività di preparazione del processo. Quando un processo viene terminato, l'attività di rilascio del processo viene eseguita su ogni nodo del pool in cui è stata eseguita un'attività di preparazione del processo. Un'attività di rilascio del processo può avere esito negativo, ma il processo verrà ancora spostato in uno stato `completed`.

### <a name="job-properties"></a>Proprietà dei processi

Verificare la presenza di errori nelle proprietà del processo seguenti:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - La proprietà `terminateReason` può includere valori per indicare che è stato superato il `maxWallClockTime`, specificato nei vincoli di processo, e pertanto il processo è stato terminato. È inoltre possibile impostare per indicare che un'attività non è riuscita se la proprietà processo `onTaskFailure` è stata impostata in modo appropriato.
  - Se si è verificato un errore di pianificazione, viene impostata la proprietà [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) .
 
### <a name="job-preparation-tasks"></a>Attività di preparazione del processo

Se un'attività di preparazione del processo viene specificata per un processo, un'istanza di tale attività verrà eseguita la prima volta che un'attività per il processo viene eseguita in un nodo. L'attività di preparazione del processo configurata nel processo può essere considerata come un modello di attività, in cui vengono eseguite più istanze di attività di preparazione del processo, fino al numero di nodi in un pool.

È necessario controllare le istanze dell'attività di preparazione del processo per determinare se sono presenti errori:
- Quando viene eseguita un'attività di preparazione del processo, l'attività che ha attivato l'attività di preparazione del processo passerà a uno [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) di `preparing`; Se l'attività di preparazione del processo ha esito negativo, l'attività di trigger verrà ripristinata allo stato `active` e non verrà eseguita.  
- Tutte le istanze dell'attività di preparazione del processo che sono state eseguite possono essere ottenute dal processo usando l'API di [stato dell'attività di preparazione e rilascio dell'elenco](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Come per qualsiasi attività, sono disponibili [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali `failureInfo`, `exitCode`e `result`.
- Se le attività di preparazione del processo hanno esito negativo, le attività del processo di attivazione non verranno eseguite, il processo non verrà completato e rimarrà bloccato. È possibile che il pool non venga utilizzato se non sono presenti altri processi con attività pianificabili.

### <a name="job-release-tasks"></a>Attività di rilascio del processo

Se un'attività di rilascio del processo viene specificata per un processo, quando un processo viene terminato, un'istanza dell'attività di rilascio del processo viene eseguita in ogni nodo del pool in cui è stata eseguita un'attività di preparazione del processo.  È necessario controllare le istanze delle attività di rilascio del processo per determinare se sono presenti errori:
- Tutte le istanze dell'attività di rilascio del processo da eseguire possono essere ottenute dal processo usando lo [stato dell'attività di preparazione e rilascio dell'elenco](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)di API. Come per qualsiasi attività, sono disponibili [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali `failureInfo`, `exitCode`e `result`.
- Se una o più attività di rilascio del processo hanno esito negativo, il processo verrà ancora terminato e passerà a uno stato `completed`.

## <a name="tasks"></a>Attività

Le attività del processo possono avere esito negativo per diversi motivi:

- La riga di comando dell'attività ha esito negativo e restituisce un codice di uscita diverso da zero.
- Sono `resourceFiles` specificati per un'attività, ma si è verificato un errore che significava che uno o più file non sono stati scaricati.
- Sono `outputFiles` specificati per un'attività, ma si è verificato un errore che significava che uno o più file non sono stati caricati.
- È stato superato il tempo trascorso per l'attività, specificato dalla proprietà `maxWallClockTime` nei [vincoli](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)dell'attività.

In tutti i casi, è necessario verificare la presenza di errori e informazioni sugli errori per le proprietà seguenti:
- La proprietà Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) contiene più proprietà che forniscono informazioni su un errore. [result](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se l'attività ha avuto esito negativo per qualsiasi motivo, con `exitCode` e `failureInfo` forniscono ulteriori informazioni sull'errore.
- L'attività verrà sempre spostata allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)`completed`, indipendentemente dal fatto che abbia avuto esito positivo o negativo.

È necessario considerare l'effetto degli errori delle attività nel processo e le dipendenze delle attività.  È possibile specificare la proprietà [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) per un'attività per configurare un'azione per le dipendenze e per il processo.
- Per le dipendenze, [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controlla se le attività dipendenti dall'attività non riuscita sono bloccate o vengono eseguite.
- Per il processo, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controlla se l'attività non riuscita conduce il processo disabilitato, terminato o lasciato invariato.

## <a name="next-steps"></a>Passaggi successivi

Verificare che l'applicazione implementi il controllo completo degli errori. può essere fondamentale rilevare e diagnosticare tempestivamente i problemi.
