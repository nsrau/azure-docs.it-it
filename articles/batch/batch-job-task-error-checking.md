---
title: Verificare la presenza di errori relativi a processi e attività
description: Errori per verificare e risolvere i problemi relativi a processi e attività
author: mscurrell
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 0c58bdf50f3e69b2b7d18f750f94fecdb512af85
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116027"
---
# <a name="job-and-task-error-checking"></a>Controllo degli errori di attività e processi

Quando si aggiungono processi e attività, possono verificarsi diversi errori. Il rilevamento degli errori per queste operazioni è semplice perché tutti gli errori vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.  Tuttavia, esistono errori che possono verificarsi in un secondo momento durante la pianificazione e l'esecuzione dei processi e delle attività.

Questo articolo descrive gli errori che possono verificarsi dopo l'invio dei processi e delle attività. Vengono elencati e illustrati gli errori che devono essere controllati e gestiti.

## <a name="jobs"></a>Processi

Un processo è un raggruppamento di una o più attività, ovvero le attività che specificano effettivamente le righe di comando da eseguire.

Quando si aggiunge un processo, è possibile specificare i parametri seguenti che possono influenzare il modo in cui il processo può avere esito negativo:

- [Vincoli di processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Facoltativamente, è possibile specificare la `maxWallClockTime` proprietà per impostare la quantità massima di tempo in cui un processo può essere attivo o in esecuzione. Se superata, il processo verrà terminato con la `terminateReason` proprietà impostata in [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) per il processo.
- [Attività di preparazione del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se specificato, viene eseguita un'attività di preparazione del processo la prima volta che un'attività viene eseguita per un processo in un nodo. L'attività di preparazione del processo può non riuscire, il che comporta l'esecuzione dell'attività e il processo non viene completato.
- [Attività di rilascio del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - È possibile specificare un'attività di rilascio del processo solo se è configurata un'attività di preparazione del processo. Quando un processo viene terminato, l'attività di rilascio del processo viene eseguita su ogni nodo del pool in cui è stata eseguita un'attività di preparazione del processo. Un'attività di rilascio del processo può avere esito negativo, ma il processo `completed` verrà ancora spostato in uno stato.

### <a name="job-properties"></a>Proprietà dei processi

Verificare la presenza di errori nelle proprietà del processo seguenti:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - La `terminateReason` proprietà può avere valori per indicare che è `maxWallClockTime`stato superato il, specificato nei vincoli di processo, e pertanto il processo è stato terminato. Può anche essere impostato per indicare che un'attività non è riuscita se `onTaskFailure` la proprietà job è stata impostata in modo appropriato.
  - Se si è verificato un errore di pianificazione, viene impostata la proprietà [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) .
 
### <a name="job-preparation-tasks"></a>Attività di preparazione del processo

Se un'attività di preparazione del processo viene specificata per un processo, un'istanza di tale attività verrà eseguita la prima volta che un'attività per il processo viene eseguita in un nodo. L'attività di preparazione del processo configurata nel processo può essere considerata come un modello di attività, in cui vengono eseguite più istanze di attività di preparazione del processo, fino al numero di nodi in un pool.

È necessario controllare le istanze dell'attività di preparazione del processo per determinare se sono presenti errori:
- Quando viene eseguita un'attività di preparazione del processo, l'attività che ha attivato l'attività di `preparing`preparazione del processo passerà allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) ; Se l'attività di preparazione del processo ha esito negativo, l'attività di trigger `active` verrà ripristinata allo stato e non verrà eseguita.  
- Tutte le istanze dell'attività di preparazione del processo che sono state eseguite possono essere ottenute dal processo usando l'API di [stato dell'attività di preparazione e rilascio dell'elenco](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Come per qualsiasi attività, sono disponibili [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali `failureInfo`, `exitCode`e. `result`
- Se le attività di preparazione del processo hanno esito negativo, le attività del processo di attivazione non verranno eseguite, il processo non verrà completato e rimarrà bloccato. È possibile che il pool non venga utilizzato se non sono presenti altri processi con attività pianificabili.

### <a name="job-release-tasks"></a>Attività di rilascio del processo

Se un'attività di rilascio del processo viene specificata per un processo, quando un processo viene terminato, un'istanza dell'attività di rilascio del processo viene eseguita in ogni nodo del pool in cui è stata eseguita un'attività di preparazione del processo.  È necessario controllare le istanze delle attività di rilascio del processo per determinare se sono presenti errori:
- Tutte le istanze dell'attività di rilascio del processo da eseguire possono essere ottenute dal processo usando lo [stato dell'attività di preparazione e rilascio dell'elenco](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)di API. Come per qualsiasi attività, sono disponibili [informazioni sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali `failureInfo`, `exitCode`e. `result`
- Se una o più attività di rilascio del processo hanno esito negativo, il processo verrà ancora terminato e `completed` passerà a uno stato.

## <a name="tasks"></a>Attività

Le attività del processo possono avere esito negativo per diversi motivi:

- La riga di comando dell'attività ha esito negativo e restituisce un codice di uscita diverso da zero.
- Sono `resourceFiles` stati specificati per un'attività, ma si è verificato un errore che significava che uno o più file non sono stati scaricati.
- Sono `outputFiles` stati specificati per un'attività, ma si è verificato un errore che significava che uno o più file non sono stati caricati.
- È stato superato il tempo trascorso per l'attività, specificato `maxWallClockTime` dalla proprietà nei [vincoli](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)dell'attività.

In tutti i casi, è necessario verificare la presenza di errori e informazioni sugli errori per le proprietà seguenti:
- La proprietà Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) contiene più proprietà che forniscono informazioni su un errore. [risultato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se l'attività ha avuto esito negativo per `exitCode` qualsiasi `failureInfo` motivo, con e fornendo ulteriori informazioni sull'errore.
- L'attività verrà sempre spostata `completed` allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), indipendentemente dal fatto che abbia avuto esito positivo o negativo.

È necessario considerare l'effetto degli errori delle attività nel processo e le dipendenze delle attività.  È possibile specificare la proprietà [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) per un'attività per configurare un'azione per le dipendenze e per il processo.
- Per le dipendenze, [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controlla se le attività dipendenti dall'attività non riuscita sono bloccate o vengono eseguite.
- Per il processo, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controlla se l'attività non riuscita conduce il processo disabilitato, terminato o lasciato invariato.

### <a name="task-command-line-failures"></a>Errori della riga di comando dell'attività

Quando si esegue la riga di comando dell'attività, l'output `stderr.txt` viene `stdout.txt`scritto in e. Inoltre, l'applicazione può scrivere nei file di log specifici dell'applicazione.

Se il nodo del pool in cui è ancora in esecuzione un'attività, è possibile ottenere e visualizzare i file di log. Ad esempio, il portale di Azure elenca e può visualizzare i file di log per un'attività o un nodo del pool. Più API consentono inoltre di elencare e ottenere i file delle attività, ad esempio [ottenere da un'attività](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Poiché i pool e i nodi del pool sono spesso temporanei, i nodi vengono continuamente aggiunti ed eliminati, è consigliabile che i file di log siano salvati in modo permanente. [I file di output delle attività](https://docs.microsoft.com/azure/batch/batch-task-output-files) rappresentano un modo pratico per salvare i file di log in archiviazione di Azure.

### <a name="output-file-failures"></a>Errori file di output
Per ogni caricamento di file, il servizio Batch scrive due file di log nel nodo di calcolo, `fileuploadout.txt` e `fileuploaderr.txt`. È possibile esaminare questi file di log per ottenere ulteriori informazioni su un errore specifico. Nei casi in cui non si è mai tentato di caricare il file, ad esempio perché non è stato possibile eseguire l'attività stessa, questi file di log non esistono.  

## <a name="next-steps"></a>Passaggi successivi

Verificare che l'applicazione implementi il controllo completo degli errori. può essere fondamentale rilevare e diagnosticare tempestivamente i problemi.
