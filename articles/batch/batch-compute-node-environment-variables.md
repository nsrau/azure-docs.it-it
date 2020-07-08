---
title: Variabili di ambiente del runtime attività
description: Linee guida alle variabili di ambiente del runtime attività e informazioni di riferimento per le analisi di Azure Batch.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 6b8ade312146802ede6e12181a082a8fcd3842fe
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960912"
---
# <a name="azure-batch-runtime-environment-variables"></a>Variabili di ambiente del runtime attività di Azure Batch

Il [servizio Azure Batch](https://azure.microsoft.com/services/batch/) imposta le variabili di ambiente seguenti sui nodi di calcolo. È possibile fare riferimento a queste variabili di ambiente nelle righe di comando delle attività e nei programmi e negli script eseguiti dalle righe di comando.

Per altre informazioni sull'uso delle variabili di ambiente con Batch, vedere [Impostazioni di ambiente per le attività](./jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilità delle variabili di ambiente

Queste variabili di ambiente sono visibili solo nel contesto dell'**utente dell'attività**, ovvero l'account utente nel nodo in cui viene eseguita un'attività. *Non* verranno visualizzate se ci si [connette in modalità remota](./error-handling.md#connect-to-compute-nodes) a un nodo di calcolo tramite Remote Desktop Protocol (RDP) o Secure Shell (SSH) e si elencano le variabili di ambiente. L'account utente usato per la connessione remota non corrisponde infatti all'account usato dall'attività.

Per ottenere il valore corrente di una variabile di ambiente, avviare `cmd.exe` in un nodo di calcolo di Windows o `/bin/sh` in un nodo Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Espansione delle variabili di ambiente della riga di comando

Le righe di comando eseguite dalle attività sui nodi di calcolo non vengono eseguite in una shell. Pertanto non possono usufruire in modo nativo dei vantaggi delle funzionalità della shell, ad esempio l'espansione delle variabili di ambiente, inclusa `PATH`. Per sfruttare i vantaggi di queste funzionalità, è necessario **richiamare la shell** nella riga di comando. Avviare ad esempio `cmd.exe` su nodi di calcolo Windows o `/bin/sh` su nodi Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variabili di ambiente

| Nome variabile                     | Descrizione                                                              | Disponibilità | Esempio |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Nome dell'account Batch a cui appartiene l'attività.                  | Tutte le attività.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | URL dell'account Batch. | Tutte le attività. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Prefisso di tutte le variabili di ambiente del pacchetto dell'app. Se, ad esempio, l'applicazione "FOO" versione "1" viene installata in un pool, la variabile di ambiente è AZ_BATCH_APP_PACKAGE_FOO_1 (in Linux) o AZ_BATCH_APP_PACKAGE_FOO#1 (in Windows). AZ_BATCH_APP_PACKAGE_FOO_1 punta al percorso in cui è stato scaricato il pacchetto (una cartella). Quando si usa la versione predefinita del pacchetto dell'app, usare la variabile di ambiente AZ_BATCH_APP_PACKAGE senza i numeri di versione. In Linux, se il nome del pacchetto dell'applicazione è "Agent-Linux-x64" e la versione è "1.1.46.0", il nome effettivo dell'ambiente è: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, con caratteri di sottolineatura e lettere minuscole. Per altri dettagli, vedere [qui](./batch-application-packages.md#execute-the-installed-applications). | Qualsiasi attività è cui è associato un pacchetto dell'app. Disponibile anche per tutte le attività se il nodo stesso dispone di pacchetti dell'applicazione. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) o AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Token di autenticazione che concede l'accesso a un set limitato di operazioni del servizio Batch. Questa variabile di ambiente è presente solo se la definizione [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) è impostata quando l'[attività viene aggiunta](/rest/api/batchservice/task/add#request-body). Il valore del token viene usato nelle API Batch come credenziale per creare un client Batch, ad esempio nell'[API .NET BatchClient.Open()](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Tutte le attività. | Token di accesso OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Directory all'interno della [directory di lavoro dell'attività][files_dirs] in cui sono archiviati i certificati per i nodi di calcolo Linux. Questa variabile di ambiente non è applicabile a nodi di calcolo Windows.                                                  | Tutte le attività.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Elenco dei nodi allocati a un'[attività a istanze multiple][multi_instance] nel formato `nodeIP,nodeIP`. | Principale multi-istanza e sottoattività. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Specifica se il nodo corrente è il nodo master per un'[attività a istanze multiple][multi_instance]. I valori possibili sono `true` e `false`.| Principale multi-istanza e sottoattività. | `true` |
| AZ_BATCH_JOB_ID                 | ID del processo a cui appartiene l'attività. | Tutte le attività tranne l'attività di avvio. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Percorso completo della [directory delle attività][files_dirs] di preparazione del processo nel nodo. | Tutte le attività ad eccezione dell'attività di preparazione del processo e dell'attività di avvio. Questo campo è disponibile solo se il processo è configurato con un'attività di preparazione del processo. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Percorso completo della [directory di lavoro delle attività][files_dirs] di preparazione del processo nel nodo. | Tutte le attività ad eccezione dell'attività di preparazione del processo e dell'attività di avvio. Questo campo è disponibile solo se il processo è configurato con un'attività di preparazione del processo. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Indirizzo IP e porta del nodo di calcolo in cui viene eseguita l'attività principale di un'[attività a istanze multiple][multi_instance]. Non usare la porta specificata qui per la comunicazione MPI o NCCL, perché è riservata per il servizio Azure Batch. Usare invece la variabile MASTER_PORT, impostandola con un valore passato tramite argomento della riga di comando (la porta 6105 è una scelta predefinita adeguata) o usando il valore impostato da AML, se AML esegue questa operazione. | Principale multi-istanza e sottoattività. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID del nodo a cui è assegnata l'attività. | Tutte le attività. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Se `true`, il nodo corrente è un nodo dedicato. Se `false`, è un [nodo con priorità bassa](batch-low-pri-vms.md). | Tutte le attività. | `true` |
| AZ_BATCH_NODE_LIST              | Elenco dei nodi allocati a un'[attività a istanze multiple][multi_instance] nel formato `nodeIP;nodeIP`. | Principale multi-istanza e sottoattività. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Percorso completo della posizione di [montaggio del file system](virtual-file-mount.md) a livello di nodo in cui risiedono tutte le directory di montaggio. Le condivisioni file di Windows usano una lettera di unità, quindi, per Windows, l'unità di montaggio costituisce una parte dei dispositivi e delle unità.  |  Tutte le attività, incluse le attività di avvio, hanno accesso all'utente, se l'utente è a conoscenza delle autorizzazioni di montaggio per la directory montata. | In Ubuntu, ad esempio, il percorso è: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Percorso completo della radice di tutte le [directory di Batch][files_dirs] nel nodo. | Tutte le attività. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Percorso completo della [directory condivisa][files_dirs] nel nodo. Tutte le attività eseguite su un nodo dispongono di accesso in lettura/scrittura per questa directory. Le attività eseguite su altri nodi non dispongono dell'accesso remoto per questa directory (non è una directory di rete "condivisa"). | Tutte le attività. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Percorso completo della [directory dell'attività di avvio][files_dirs] nel nodo. | Tutte le attività. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID del pool in cui è in esecuzione l'attività. | Tutte le attività. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Percorso completo della [directory delle attività][files_dirs] nel nodo. Questa directory contiene `stdout.txt` e `stderr.txt` per l'attività e AZ_BATCH_TASK_WORKING_DIR. | Tutte le attività. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID dell'attività corrente. | Tutte le attività tranne l'attività di avvio. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Percorso di directory identico per l'attività principale e tutte le sottoattività di un'[attività a istanze multiple][multi_instance]. Il percorso esiste in ogni nodo in cui viene eseguita l'attività a istanze multiple ed è accessibile in lettura e scrittura per i comandi dell'attività in esecuzione nel nodo (sia il [comando di coordinamento][coord_cmd] che il [comando applicazione][app_cmd]). Le sottoattività o l'attività principale eseguita in altri nodi non hanno accesso remoto a questa directory (non è una directory di rete "condivisa"). | Principale multi-istanza e sottoattività. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Percorso completo della [directory di lavoro delle attività][files_dirs] nel nodo. L'attività attualmente in esecuzione ha accesso in lettura/scrittura per questa directory. | Tutte le attività. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Elenco dei nodi e numero di core per nodo allocati a un'[attività a istanze multiple][multi_instance]. I nodi e i core sono elencati nel formato`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, dove il numero di nodi è seguito da uno o più indirizzi IP di nodo e dal numero di core per ognuno. |  Principale multi-istanza e sottoattività. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
