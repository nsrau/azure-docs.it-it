---
title: Variabili di ambiente per i nodi di calcolo di Azure Batch | Microsoft Docs
description: Riferimento variabile di ambiente del nodo di calcolo per le analisi di Azure Batch.
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 9a31288f4cf67e551db016aa3b56058f607ef13a
ms.lasthandoff: 04/22/2017

---

# <a name="azure-batch-compute-node-environment-variables"></a>Variabili di ambiente per i nodi di calcolo di Azure Batch
Il [servizio Azure Batch](https://azure.microsoft.com/services/batch/) imposta le variabili di ambiente seguenti sui nodi di calcolo. È possibile fare riferimento a queste variabili di ambiente nelle righe di comando delle attività e nei programmi e negli script eseguiti dalle righe di comando.

Per altre informazioni sull'uso delle variabili di ambiente con Batch, vedere [Impostazioni di ambiente per le attività](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilità delle variabili di ambiente

Queste variabili di ambiente sono visibili solo nel contesto dell'**utente dell'attività**, ovvero l'account utente nel nodo in cui viene eseguita un'attività. *Non* verranno visualizzate se ci si [connette in modalità remota](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a un nodo di calcolo tramite Remote Desktop Protocol (RDP) o Secure Shell (SSH) e si elencano le variabili di ambiente. L'account utente usato per la connessione remota non corrisponde infatti all'account usato dall'attività.

## <a name="command-line-expansion-of-environment-variables"></a>Espansione delle variabili di ambiente della riga di comando

Le righe di comando eseguite dalle attività sui nodi di calcolo non vengono eseguite in una shell. Pertanto non possono usufruire in modo nativo dei vantaggi delle funzionalità della shell, ad esempio l'espansione delle variabili di ambiente, inclusa `PATH`. Per sfruttare i vantaggi di queste funzionalità, è necessario **richiamare la shell** nella riga di comando. Ad esempio, avviando `cmd.exe` sui nodi di calcolo Windows o `/bin/sh` sui nodi Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variabili di ambiente

| Nome variabile         | Descrizione                                                              | Disponibilità | Esempio |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| `AZ_BATCH_ACCOUNT_NAME`           | Nome dell'account Batch a cui appartiene l'attività. | Tutte le attività. | `mybatchaccount` |
| `AZ_BATCH_CERTIFICATES_DIR`       | Una directory all'interno della [directory di lavoro dell'attività] [ files_dirs] in cui sono archiviati i certificati i nodi di calcolo Linux. Si noti che questa variabile di ambiente non è applicabile a Windows i nodi di calcolo. | Tutte le attività. | `/mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs` |
| `AZ_BATCH_JOB_ID`                 | ID del processo a cui appartiene l'attività. | Tutte le attività tranne l'attività di avvio. | `batchjob001` |
| `AZ_BATCH_JOB_PREP_DIR`           | Percorso completo della [directory delle attività][files_dirs] di preparazione del processo sul nodo. | Tutte le attività ad eccezione dell'attività di preparazione del processo e dell'attività di avvio. Questo campo è disponibile solo se il processo è configurato con un'attività di preparazione del processo. | `C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation` |
| `AZ_BATCH_JOB_PREP_WORKING_DIR`   | Percorso completo della [directory di lavoro delle attività][files_dirs] di preparazione del processo sul nodo. | Tutte le attività ad eccezione dell'attività di preparazione del processo e dell'attività di avvio. Questo campo è disponibile solo se il processo è configurato con un'attività di preparazione del processo. | `C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd` |
| `AZ_BATCH_NODE_ID`                | ID del nodo a cui è assegnata l'attività. | Tutte le attività. | `tvm-1219235766_3-20160919t172711z` |
| `AZ_BATCH_NODE_ROOT_DIR`          | Percorso completo della radice di tutte le [directory di Batch][files_dirs] nel nodo. | Tutte le attività. | `C:\user\tasks` |
| `AZ_BATCH_NODE_SHARED_DIR`        | Percorso completo della [directory condivisa][files_dirs] nel nodo. Tutte le attività eseguite su un nodo dispongono di accesso in lettura/scrittura per questa directory. Le attività eseguite su altri nodi non dispongono dell'accesso remoto per questa directory (non è una directory di rete "condivisa"). | Tutte le attività. | `C:\user\tasks\shared` |
| `AZ_BATCH_NODE_STARTUP_DIR`       | Percorso completo della [directory dell'attività di avvio][files_dirs] nel nodo. | Tutte le attività. | `C:\user\tasks\startup` |
| `AZ_BATCH_POOL_ID`                | ID del pool in cui è in esecuzione l'attività. | Tutte le attività. | `batchpool001` |
| `AZ_BATCH_TASK_DIR`               | Percorso completo della [directory delle attività][files_dirs] nel nodo. Questa directory contiene `stdout.txt` e `stderr.txt` per l'attività e `AZ_BATCH_TASK_WORKING_DIR`. | Tutte le attività. | `C:\user\tasks\workitems\batchjob001\job-1\task001` |
| `AZ_BATCH_TASK_ID`                | ID dell'attività corrente. | Tutte le attività tranne l'attività di avvio. | `task001` |
| `AZ_BATCH_TASK_WORKING_DIR`       | Percorso completo della [directory di lavoro delle attività][files_dirs] nel nodo. L'attività attualmente in esecuzione ha accesso in lettura/scrittura per questa directory. | Tutte le attività. | `C:\user\tasks\workitems\batchjob001\job-1\task001\wd` |
| `CCP_NODES`                       | L'elenco dei nodi e il numero di core per nodo allocati per un'[attività multi-istanza][multi_instance]. I nodi e i core sono elencati nel formato`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, dove il numero di nodi è seguito da uno o più indirizzi IP di nodo e dal numero di core per ognuno. |  Principale multi-istanza e sottoattività. |`2 10.0.0.4 1 10.0.0.5 1` |
| `AZ_BATCH_NODE_LIST`              | L'elenco dei nodi che vengono allocati a un'[attività multi-istanza][multi_instance] nel formato `nodeIP;nodeIP`. | Principale multi-istanza e sottoattività. | `10.0.0.4;10.0.0.5` |
| `AZ_BATCH_HOST_LIST`              | L'elenco dei nodi che vengono allocati a un'[attività multi-istanza][multi_instance] nel formato `nodeIP,nodeIP`. | Principale multi-istanza e sottoattività. | `10.0.0.4,10.0.0.5` |
| `AZ_BATCH_MASTER_NODE`            | L'indirizzo IP e la porta del nodo di calcolo in cui viene eseguita l'attività principale di un'[attività multi-istanza][multi_instance]. | Principale multi-istanza e sottoattività. | `10.0.0.4:6000`|
| `AZ_BATCH_TASK_SHARED_DIR` | Un percorso di directory identico per l'attività principale e tutte le sottoattività di un'[attività multi-istanza][multi_instance]. Il percorso esiste in ogni nodo in cui viene eseguita l'attività multi-istanza ed è accessibile in lettura/scrittura per i comandi di attività in esecuzione su tale nodo (sia il [comando di coordinamento][coord_cmd] che [il comando di applicazione][app_cmd]). Le sottoattività o un'attività principale eseguite su altri nodi non dispongono dell'accesso remoto per questa directory (non è una directory di rete "condivisa"). | Principale multi-istanza e sottoattività. | `C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask` |
| `AZ_BATCH_IS_CURRENT_NODE_MASTER` | Specifica se il nodo corrente è il nodo principale per un'[attività multi-istanza][multi_instance]. I valori possibili sono `true` e `false`.| Principale multi-istanza e sottoattività. | `true` |


[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
