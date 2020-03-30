---
title: Verificare la presenza di errori relativi a processi e attività - Azure Batch Documenti Microsoft
description: Errori da controllare e risolvere i problemi relativi a processi e attività
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087003"
---
# <a name="job-and-task-error-checking"></a>Controllo degli errori di lavoro e attività

Esistono vari errori che possono verificarsi quando si aggiungono processi e attività. Il rilevamento degli errori per queste operazioni è semplice perché eventuali errori vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.  Tuttavia, si verificano errori che possono verificarsi in un secondo momento quando i processi e le attività vengono pianificati ed eseguiti.

In questo articolo vengono illustrati gli errori che possono verificarsi dopo l'invio di processi e attività. Elenca e spiega gli errori che devono essere controllati e gestiti.

## <a name="jobs"></a>Processi

Un processo è un raggruppamento di una o più attività, ovvero le attività che specificano effettivamente le righe di comando da eseguire.

Quando si aggiunge un processo, è possibile specificare i seguenti parametri che possono influenzare il modo in cui il processo può avere esito negativo:

- [Vincoli di processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - La `maxWallClockTime` proprietà può essere specificata facoltativamente per impostare la quantità massima di tempo per cui un processo può essere attivo o in esecuzione. Se viene superato, il processo `terminateReason` verrà terminato con la proprietà impostata in [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) per il processo.
- [Attività di preparazione del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se specificato, un'attività di preparazione del processo viene eseguita la prima volta che un'attività viene eseguita per un processo in un nodo. L'attività di preparazione del processo può non riuscire, il che porterà all'attività non eseguita e al completamento del processo.
- [Attività di rilascio processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Un'attività di rilascio del processo può essere specificata solo se è configurata un'attività di preparazione del processo. Quando un processo viene terminato, l'attività di rilascio del processo viene eseguita in ognuno dei nodi del pool in cui è stata eseguita un'attività di preparazione del processo. Un'attività di rilascio del processo può `completed` non riuscire, ma il processo verrà comunque spostato in uno stato.

### <a name="job-properties"></a>Proprietà dei processi

Per verificare la ricerca di errori, è necessario verificare la ricerca di errori nelle seguenti proprietà del processo:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - La `terminateReason` proprietà può avere valori `maxWallClockTime`per indicare che l'oggetto , specificato nei vincoli del processo, è stato superato e pertanto il processo è stato terminato. Può anche essere impostata per indicare `onTaskFailure` che un'attività non è riuscita se la proprietà del processo è stata impostata in modo appropriato.
  - La proprietà [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) viene impostata se si è verificato un errore di pianificazione.
 
### <a name="job-preparation-tasks"></a>Attività di preparazione dei processi

Se per un processo viene specificata un'attività di preparazione del processo, verrà eseguita un'istanza di tale attività la prima volta che un'attività per il processo viene eseguita su un nodo. L'attività di preparazione del processo configurata nel processo può essere considerata come un modello di attività, con più istanze di attività di preparazione del processo in esecuzione, fino al numero di nodi in un pool.

Le istanze delle attività di preparazione del processo devono essere controllate per determinare se si sono verificati errori:The job preparation task instances should be checked for determine if there were errors:
- Quando viene eseguita un'attività di preparazione del processo, l'attività `preparing`che ha attivato l'attività di preparazione del processo passerà allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) ; Se l'attività di preparazione del processo ha `active` esito negativo, l'attività di attivazione tornerà allo stato e non verrà eseguita.  
- Tutte le istanze dell'attività di preparazione del processo che sono state eseguite possono essere ottenute dal processo utilizzando l'API [Preparazione elenco e Stato attività di rilascio.](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) Come per qualsiasi attività, sono disponibili informazioni `failureInfo` `exitCode` [sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali , , e `result`.
- Se le attività di preparazione del processo hanno esito negativo, le attività del processo di attivazione non verranno eseguite, il processo non verrà completato e verrà bloccato. Il pool potrebbe non essere utilizzato se non sono presenti altri processi con attività che possono essere pianificate.

### <a name="job-release-tasks"></a>Attività di rilascio dei processi

Se per un processo viene specificata un'attività di rilascio del processo, quando un processo viene terminato viene eseguita un'istanza dell'attività di rilascio del processo in ognuno dei nodi del pool in cui è stata eseguita un'attività di preparazione del processo.  Le istanze delle attività di rilascio del processo devono essere controllate per determinare se si sono verificati errori:The job release task instances should be checked to determine if there were errors:
- Tutte le istanze dell'attività di rilascio del processo in esecuzione possono essere ottenute dal processo utilizzando la preparazione dell'elenco API [e lo stato dell'attività](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)di rilascio . Come per qualsiasi attività, sono disponibili informazioni `failureInfo` `exitCode` [sull'esecuzione](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) con proprietà quali , , e `result`.
- Se una o più attività di rilascio del processo hanno `completed` esito negativo, il processo verrà comunque terminato e verrà spostato in uno stato.

## <a name="tasks"></a>Attività

Le attività processo possono avere esito negativo per diversi motivi:Job tasks can fail for multiple reasons:

- La riga di comando dell'attività ha esito negativo, tornando con un codice di uscita diverso da zero.
- Sono `resourceFiles` stati specificati per un'attività, ma si è verificato un errore che indicava che uno o più file non erano stati scaricati.
- Sono `outputFiles` stati specificati per un'attività, ma si è verificato un errore che indicava che uno o più file non venivano caricati.
- È stato superato il `maxWallClockTime` tempo trascorso per l'attività, specificato dalla proprietà nei [vincoli](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)dell'attività.

In tutti i casi è necessario verificare la ricerca di errori nelle seguenti proprietà e informazioni sugli errori:
- La proprietà [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) delle attività contiene più proprietà che forniscono informazioni su un errore. [risultato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se l'attività non `exitCode` `failureInfo` è riuscita per qualsiasi motivo, con e fornendo ulteriori informazioni sull'errore.
- L'attività passerà `completed` sempre allo [stato](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), indipendentemente dal fatto che abbia avuto esito positivo o negativo.

È necessario considerare l'impatto degli errori delle attività sul processo e delle eventuali dipendenze delle attività.  La proprietà [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) può essere specificata per un'attività per configurare un'azione per le dipendenze e per il processo.
- Per le dipendenze, [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controlla se le attività dipendenti dall'attività non riuscita sono bloccate o vengono eseguite.
- Per il processo, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controlla se l'attività non riuscita comporta la disabilitazione, la terminazione o la modifica del processo.

### <a name="task-command-line-failures"></a>Errori della riga di comando dell'attivitàTask command line failures

Quando viene eseguita la riga di `stderr.txt` `stdout.txt`comando dell'attività, l'output viene scritto in e . Inoltre, l'applicazione può scrivere in file di log specifici dell'applicazione.

Se il nodo del pool in cui è stata eseguita un'attività esiste ancora, è possibile ottenere e visualizzare i file di registro. Ad esempio, il portale di Azure elenca e può visualizzare i file di log per un'attività o un nodo del pool. Più API consentono inoltre di elencare e ottenere file di attività, ad esempio [Ottieni da attività](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

A causa dei pool e dei nodi del pool spesso temporanei, con i nodi aggiunti ed eliminati continuamente, è consigliabile rendere persistenti i file di log. I file di output delle attività sono un modo pratico per salvare i file di log in Archiviazione di [Azure.Task output files](https://docs.microsoft.com/azure/batch/batch-task-output-files) are a convenient way to save log files to Azure Storage.

### <a name="output-file-failures"></a>Errori del file di output
Per ogni caricamento di file, il servizio Batch scrive due file di log nel nodo di calcolo, `fileuploadout.txt` e `fileuploaderr.txt`. È possibile esaminare questi file di log per ottenere ulteriori informazioni su un errore specifico. Nei casi in cui il caricamento del file non è mai stato tentato, ad esempio perché non è stato possibile eseguire l'attività stessa, questi file di registro non esisteranno.  

## <a name="next-steps"></a>Passaggi successivi

Verificare che l'applicazione implementi il controllo completo degli errori; può essere fondamentale per rilevare e diagnosticare tempestivamente i problemi.
