---
title: File e directory in Azure Batch
description: Informazioni su file e directory e su come vengono usati in un flusso di lavoro Azure Batch dal punto di vista dello sviluppo.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: e7babb7e2cfdbbe78f61be766c549c1e80cacf98
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790868"
---
# <a name="files-and-directories-in-azure-batch"></a>File e directory in Azure Batch

In Azure Batch, ogni attività ha una directory di lavoro in cui crea zero o più file e directory. Questa directory di lavoro può essere usata per archiviare il programma eseguito dall'attività, i dati elaborati e l'output dell'elaborazione eseguita dall'attività. Tutti i file e le directory di un'attività sono proprietà dell'utente dell'attività.

Il servizio Batch espone una parte del file system in un nodo come *directory radice*. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_ROOT_DIR`. Per altre informazioni sull'uso delle variabili di ambiente, vedere [Impostazioni di ambiente per le attività](jobs-and-tasks.md#environment-settings-for-tasks).

La directory radice contiene la struttura di directory seguente:

![Struttura di directory dei nodi di calcolo] [media\files-and-directories\node-folder-structure.png]

- **applications**: Contiene informazioni sui dettagli dei pacchetti di applicazioni installati nel nodo di calcolo. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_APP_PACKAGE`.

- **fsmounts**: La directory contiene i file system montati in un nodo di calcolo. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_MOUNTS_DIR`. Per ulteriori informazioni, vedere [Montare un file system virtuale in un pool Batch](virtual-file-mount.md).

- **shared**: questa directory fornisce l'accesso in lettura/scrittura a *tutte* le attività eseguite in un nodo. Le attività eseguite nel nodo possono creare, leggere, aggiornare ed eliminare file in questa directory. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_SHARED_DIR`.

- **startup**: questa directory viene usata da un'attività di avvio come directory di lavoro. Tutti i file scaricati nel nodo dall'attività di avvio vengono archiviati qui. L'attività di avvio può creare, leggere, aggiornare ed eliminare file in tale directory. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_STARTUP_DIR`.

- **volatile**: Questa directory è per scopi interni. Non vi è alcuna garanzia che i file presenti in questa directory o che la directory stessa saranno presenti in futuro.

- **workitems**: Questa directory contiene le directory per i processi e le relative attività nel nodo di calcolo.

    Nella directory **workitems** viene creata una directory **Attività** per ogni attività in esecuzione sul nodo. È possibile accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_TASK_DIR`.
    
    All'interno di ogni directory di **Attività**, il servizio Batch crea una directory di lavoro (`wd`), il cui percorso univoco viene specificato dalla variabile di ambiente `AZ_BATCH_TASK_WORKING_DIR`. Questa directory fornisce l'accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare file in tale directory. Questa directory viene mantenuta in base al vincolo *RetentionTime* specificato per l'attività.

    I file `stdout.txt` e `stderr.txt` vengono scritti nella cartella dell'**Attività** durante l'esecuzione di quest'ultima.

> [!IMPORTANT]
> Quando un nodo viene rimosso dal pool, vengono rimossi tutti i file archiviati nel nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [rilevamento e gestione degli errori](error-handling.md) in Azure Batch.