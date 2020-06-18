---
title: Rilevamento e gestione degli errori in Azure Batch
description: Informazioni sulla gestione degli errori nei flussi di lavoro del servizio Batch dal punto di vista dello sviluppo.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 07b9d43ea9bdf21fe3188c4481e6dd0c86374607
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790828"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Rilevamento e gestione degli errori in Azure Batch

A volte, potrebbe essere necessario gestire sia gli errori delle attività che quelli delle applicazioni nella soluzione Batch. Questo articolo illustra i tipi di errori e come risolverli.

## <a name="error-codes"></a>Codici di errore

I tipi di errori generici includono:

- Errori di rete per le richieste che non hanno mai raggiunto Batch o quando la risposta di Batch non ha raggiunto il client nel tempo.
- Errori interni del server (risposta HTTP del codice di stato 5xx standard).
- Errori correlati alla limitazione delle richieste, ad esempio le risposte HTTP del codice di stato 429 o 503 con l'intestazione retry-after.
- Errori 4xx come AlreadyExists e InvalidOperation. Ciò significa che la risorsa non è nello stato corretto per la transizione di stato.

Per informazioni dettagliate sui codici di errore specifici, inclusi i codici di errore per l'API REST, il servizio Batch e l'attività/pianificazione del processo, vedere [Codici di stato e di errore di Batch](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Errori delle applicazioni

Durante l'esecuzione un'applicazione può generare un output di diagnostica che può essere usato per la risoluzione dei problemi. Come descritto in [File e directory](files-and-directories.md), il servizio Batch scrive l'output standard e l'output degli errori standard nei file `stdout.txt` e `stderr.txt` nella directory dell'attività nel nodo di calcolo.

Per scaricare questi file, è possibile usare il portale di Azure oppure uno degli SDK di Batch. È ad esempio possibile recuperare questi e altri file per la risoluzione dei problemi con [ComputeNode.GetNodeFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) e [CloudTask.GetNodeFile nella libreria Batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask).

## <a name="task-errors"></a>Errori attività

Gli errori delle attività rientrano in diverse categorie.

### <a name="pre-processing-errors"></a>Errori di pre-elaborazione

Se un'attività non può essere avviata, viene impostato un errore di pre-elaborazione per l'attività stessa.  

Gli errori di pre-elaborazione possono essere dovuti a file di risorse dell'attività spostati, un account di archiviazione non più disponibile o un altro problema che ha impedito la copia corretta dei file nel nodo.

### <a name="file-upload-errors"></a>In caso di errori di caricamento file

Se il caricamento dei file specificati per un'attività non riesce per qualsiasi motivo, per l'attività viene impostato un errore di caricamento file.

Gli errori di caricamento dei file possono verificarsi se la firma di accesso condiviso specificata per l'accesso ad Archiviazione di Azure non è valida oppure non concede le autorizzazioni di scrittura, se l'account di archiviazione non è più disponibile oppure se si è verificato un altro problema che ha impedito la copia corretta dei file dal nodo.

### <a name="application-errors"></a>Errori delle applicazioni

Anche il processo specificato dalla riga di comando dell'attività può non riuscire. Il processo viene considerato non riuscito quando il processo eseguito dall'attività restituisce un codice di uscita diverso da zero. Vedere *Codici di uscita delle attività* nella prossima sezione.

Per gli errori delle applicazioni è possibile configurare il servizio Batch in modo che riprovi automaticamente a eseguire l'attività per un numero di volte specificato.

### <a name="constraint-errors"></a>Errori di vincolo

È possibile impostare un vincolo che specifichi la durata massima di esecuzione per un processo o un'attività, *maxWallClockTime*. Questa impostazione può essere utile per terminare le attività bloccate.

Quando viene superata la quantità massima di tempo impostata, l'attività viene contrassegnata come *completata*, ma il codice di uscita viene impostato su `0xC000013A` e il campo *schedulingError* viene contrassegnato come `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Codici di uscita delle attività

Come indicato in precedenza, un'attività viene contrassegnata come non riuscita dal servizio Batch se il processo eseguito dall'attività restituisce un codice di uscita non zero. Quando un'attività esegue un processo, il servizio Batch popola la proprietà del codice di uscita dell'attività con il codice restituito del processo.

È importante notare che il codice di uscita dell'attività non è determinato dal servizio Batch. Il codice di uscita di un'attività è determinato dal processo stesso o dal sistema operativo in cui il processo è stato eseguito.

## <a name="task-failures-or-interruptions"></a>Errori o interruzioni delle attività

In alcuni casi, le attività non riescono o vengono interrotte. È possibile che si verifichi un errore dell'applicazione dell'attività stessa, che il nodo in cui è in esecuzione l'attività venga riavviato o che il nodo venga rimosso dal pool durante un'operazione di ridimensionamento (se nei criteri di deallocazione del pool è impostata la rimozione immediata dei nodi senza attendere il completamento delle attività). In ogni caso, Batch può riaccodare automaticamente l'attività per eseguirla in un altro nodo.

È anche possibile che un problema intermittente provochi il blocco di un'attività o ne renda troppo lunga l'esecuzione. È possibile impostare l'intervallo di esecuzione massimo per un'attività. Se si supera l'intervallo di esecuzione massimo, il servizio Batch consente di interrompere l'applicazione dell'attività.

## <a name="connect-to-compute-nodes"></a>Connessione ai nodi di calcolo

È possibile eseguire altre operazioni di debug e di risoluzione dei problemi accedendo a un nodo di calcolo in remoto. È possibile usare il portale di Azure per scaricare un file Remote Desktop Protocol (RDP) per i nodi Windows e ottenere informazioni sulla connessione SSH (Secure Shell) per i nodi Linux. È anche possibile eseguire questa operazione usando le API Batch, ad esempio con [Batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) o [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Per connettersi a un nodo tramite RDP o SSH, è necessario creare prima di tutto un utente nel nodo. A questo scopo è possibile usare il Portale di Azure, [aggiungere un account utente a un nodo](https://docs.microsoft.com/rest/api/batchservice/computenode/adduser) con l'API Batch REST, chiamare il metodo [ComputeNode.CreateComputeNodeUser](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) in Batch .NET o chiamare il metodo [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) nel modulo Batch Python.

Se è necessario limitare o disabilitare l'accesso RDP o SSH ai nodi di calcolo, vedere [Configurare o disabilitare l'accesso remoto ai nodi di calcolo in un pool di Azure Batch](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Risolvere i problemi relativi ai nodi

Nei casi in cui alcune attività non riescono, il servizio o l'applicazione client Batch può esaminare i metadati delle attività non riuscite per identificare un nodo non correttamente funzionante. A ogni nodo di un pool viene assegnato un ID univoco e il nodo in cui viene eseguita un'attività viene incluso nei metadati dell'attività. Dopo avere identificato un "nodo problematico", è possibile intervenire in diversi modi:

- **Riavviare un nodo** ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/reboot) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    Il riavvio del nodo a volte consente di eliminare problemi latenti, ad esempio processi bloccati o arrestati in modo anomalo. Se il pool usa un'attività di avvio o il processo usa un'attività di preparazione del processo, vengono eseguite quando il nodo viene riavviato.
- **Ricreare l'immagine di un nodo** ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Il sistema operativo viene reinstallato nel nodo. Come durante il riavvio di un nodo, le attività di avvio e le attività di preparazione del processo vengono rieseguite dopo che è stata ricreata l'immagine del nodo.
- **Rimuovere i nodi di calcolo da un pool** ([REST](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations))

    A volte è necessario rimuovere completamente il nodo dal pool.
- **Disabilitare la pianificazione delle attività in un nodo** ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/disablescheduling) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    In questo modo il nodo in realtà passa offline e non è possibile assegnargli altre attività, ma può rimanere in esecuzione e nel pool. È quindi possibile eseguire altre indagini sulla causa degli errori senza perdere i dati delle attività non riuscite e senza che il nodo generi altri errori delle attività. È ad esempio possibile disabilitare la pianificazione delle attività nel nodo, quindi accedere in remoto per esaminare i log eventi del nodo o eseguire altre operazioni di risoluzione dei problemi. Al termine dell'indagine, è possibile riportare il nodo online abilitando la pianificazione delle attività ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/enablescheduling) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)) o eseguire una delle altre azioni illustrate in precedenza.

> [!IMPORTANT]
> Con le azioni descritte in precedenza, è possibile specificare il modo in cui vengono gestite le attività in esecuzione nel nodo quando si esegue l'azione. Quando ad esempio si disabilita la pianificazione delle attività in un nodo con la libreria client Batch .NET, è possibile specificare un valore enum [DisableComputeNodeSchedulingOption](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) per specificare se eseguire l'operazione **Termina** per terminare le attività in esecuzione o **Reinserisci nella coda** per la pianificazione in altri nodi oppure consentire il completamento delle attività in esecuzione prima di eseguire l'azione (**TaskCompletion**).

## <a name="retry-after-errors"></a>Riprovare dopo gli errori

Le API di Batch invieranno una notifica in caso di errore. Tutte possono essere ripetute e tutte includono un gestore tentativi globale a tale scopo. È preferibile usare questo meccanismo incorporato.

In seguito a un errore, prima di riprovare è necessario attendere alcuni secondi tra i tentativi. Se si riprova troppo spesso o troppo rapidamente, il gestore tentativi limiterà le richieste.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [verificare la presenza di errori in pool e nodi](batch-pool-node-error-checking.md).
- Informazioni su come [verificare la presenza di errori relativi a processi e attività](batch-job-task-error-checking.md).
- Esaminare l'elenco dei [codici di stato e di errore di Batch](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
