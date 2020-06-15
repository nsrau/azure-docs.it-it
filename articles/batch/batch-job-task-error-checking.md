---
title: Verificare la presenza di errori relativi a processi e attività
description: Errori da verificare per risolvere i problemi relativi a processi e attività
author: mscurrell
ms.topic: how-to
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 368b803ae3e62c0e27da9e52d9df9842037757ff
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726622"
---
# <a name="job-and-task-error-checking"></a>Verifica della presenza di errori relativi a processi e attività

Quando si aggiungono processi e attività, possono verificarsi diversi errori. Il rilevamento degli errori che si verificano durante queste operazioni è semplice perché qualsiasi errore viene restituito immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.  Tuttavia, alcuni errori potrebbero verificarsi in un secondo momento durante la pianificazione e l'esecuzione dei processi e delle attività.

Questo articolo descrive gli errori che possono verificarsi dopo l'avvio dei processi e delle attività. Vengono elencati e illustrati gli errori che è necessario verificare e gestire.

## <a name="jobs"></a>Processi

Un processo consiste in un raggruppamento di una o più attività, ovvero delle attività che specificano effettivamente le righe di comando da eseguire.

Nell'aggiunta di un processo è possibile specificare i parametri seguenti, che potrebbero determinare un esito negativo:

- [Vincoli di processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Facoltativamente, è possibile specificare la proprietà `maxWallClockTime` per impostare il tempo massimo per cui un processo può essere attivo o in esecuzione. Se il valore definito viene superato, il processo viene terminato e la proprietà `terminateReason` viene impostata su [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob).
- [Attività di preparazione del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se viene specificata un'attività di preparazione, questa viene eseguita al primo avvio di un'attività per un processo in un nodo. Se l'attività di preparazione del processo ha esito negativo, l'attività a cui fa riferimento non viene eseguita e il processo non viene completato.
- [Attività di rilascio del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - È possibile specificare un'attività di rilascio solo se è configurata un'attività di preparazione del processo. Quando un processo viene terminato, l'attività di rilascio del processo viene eseguita per ogni nodo del pool in cui è stata eseguita un'attività di preparazione del processo. Un'attività di rilascio del processo può avere esito negativo, ma il processo viene comunque impostato sullo stato `completed`.

### <a name="job-properties"></a>Proprietà dei processi

Verificare la presenza di errori nelle proprietà di processo seguenti:

- [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation):
  - La proprietà `terminateReason` può includere valori che indicano se è stato superato il valore `maxWallClockTime` specificato nei vincoli di processo e se il processo è stato terminato. Se la proprietà `onTaskFailure` del processo è stata impostata in modo appropriato, è anche possibile impostare questa proprietà per indicare che un'attività ha avuto esito negativo.
  - La proprietà [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) viene impostata in caso di errore di pianificazione.
 
### <a name="job-preparation-tasks"></a>Attività di preparazione del processo

Se per un processo viene specificata un'attività di preparazione, la prima volta che in un nodo viene avviata un'attività del processo viene eseguita anche un'istanza dell'attività di preparazione. L'attività di preparazione configurata per il processo può essere considerata come un modello di attività in cui vengono eseguite più istanze di attività di preparazione, fino al numero di nodi di un pool.

Verificare le istanze dell'attività di preparazione del processo per determinare se sono presenti errori:
- Quando viene eseguita un'attività di preparazione del processo, l'attività che ne ha determinato l'attivazione passa allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) di `preparing`. Se l'attività di preparazione ha esito negativo, l'attività trigger viene ripristinata allo stato `active` e non viene eseguita.  
- Tutte le istanze dell'attività di preparazione del processo eseguite possono essere ottenute dal processo tramite l'API dello [stato di attività di preparazione e rilascio dei processi](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Come per qualsiasi attività, le [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) sono disponibili con proprietà come `failureInfo`, `exitCode` e `result`.
- Se le attività di preparazione del processo hanno esito negativo, le attività del processo di trigger non vengono eseguite, il processo non viene completato e rimane bloccato. Se non sono presenti altri processi con attività pianificabili, è possibile che il pool non venga usato.

### <a name="job-release-tasks"></a>Attività di rilascio del processo

Se per un processo viene specificata un'attività di rilascio, quando un processo viene terminato, viene eseguita un'istanza dell'attività di rilascio in ogni nodo del pool in cui è stata eseguita un'attività di preparazione.  Verificare le istanze dell'attività di rilascio del processo per determinare se sono presenti errori:
- Tutte le istanze dell'attività di rilascio del processo da eseguire possono essere ottenute dal processo tramite l'API dello [stato di attività di preparazione e rilascio dei processi](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Come per qualsiasi attività, le [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) sono disponibili con proprietà come `failureInfo`, `exitCode` e `result`.
- Se una o più attività di rilascio del processo hanno esito negativo, il processo viene comunque terminato e passa allo stato `completed`.

## <a name="tasks"></a>Attività

Le attività del processo possono avere esito negativo per diversi motivi:

- La riga di comando dell'attività ha esito negativo e restituisce un codice di uscita diverso da zero.
- Sono stati specificati `resourceFiles` per un'attività, ma si è verificato un errore che indica che uno o più file non sono stati scaricati.
- Sono stati specificati `outputFiles` per un'attività, ma si è verificato un errore che indica che uno o più file non sono stati caricati.
- È stato superato il limite di tempo trascorso per l'attività specificato dalla proprietà `maxWallClockTime` nei [vincoli](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints) dell'attività.

In tutti i casi, è necessario verificare la presenza di errori e informazioni sugli errori per le proprietà seguenti:
- La proprietà [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) delle attività contiene più proprietà che forniscono informazioni su un errore. [result](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se l'attività ha avuto esito negativo per qualsiasi motivo, `exitCode` e `failureInfo` forniscono ulteriori informazioni sull'errore.
- L'attività passa sempre allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) `completed`, indipendentemente dal fatto che abbia avuto esito positivo o negativo.

È necessario valutare l'effetto degli errori delle attività sul processo e sulle dipendenze delle attività.  Per configurare un'azione per le dipendenze e per il processo, è possibile specificare la proprietà [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) per un'attività.
- Per le dipendenze, [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controlla se le attività dipendenti dall'attività non riuscita vengono bloccate o eseguite.
- Per il processo, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controlla se il processo viene disabilitato, terminato o lasciato invariato in seguito all'attività con esito negativo.

### <a name="task-command-line-failures"></a>Errori della riga di comando dell'attività

Quando la riga di comando dell'attività viene eseguita, l'output viene scritto in `stderr.txt` e `stdout.txt`. Inoltre, l'applicazione potrebbe registrare l'output anche nei file di log specifici dell'applicazione.

Se il nodo del pool in cui è stata eseguita l'attività esiste ancora, è possibile recuperare e visualizzare i file di log. Ad esempio, nel portale di Azure è presente un elenco dei file di log che è possibile visualizzare per un'attività o un nodo del pool. Sono disponibili inoltre diverse API che consentono di visualizzare un elenco dei file delle attività e di recuperare tali file, ad esempio l'API [Get From Task](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

I pool e i nodi dei pool sono spesso temporanei poiché i nodi vengono continuamente aggiunti o eliminati, pertanto, è consigliabile salvare i file di log in modo permanente. I [file di output delle attività](https://docs.microsoft.com/azure/batch/batch-task-output-files) offrono un modo pratico per salvare i file di log nell'Archiviazione di Azure.

### <a name="output-file-failures"></a>Errori del file di output
Per ogni caricamento di file, il servizio Batch scrive due file di log nel nodo di calcolo, `fileuploadout.txt` e `fileuploaderr.txt`. È possibile esaminare questi file di log per ottenere ulteriori informazioni su un errore specifico. Se il caricamento del file non è mai stato effettuato, ad esempio perché non è stato possibile eseguire l'attività, i file di log non sono presenti.  

## <a name="next-steps"></a>Passaggi successivi

Assicurarsi che l'applicazione esegua il controllo completo degli errori, si tratta di un aspetto fondamentale per rilevare e diagnosticare tempestivamente i problemi.
