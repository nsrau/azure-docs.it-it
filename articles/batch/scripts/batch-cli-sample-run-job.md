---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire un processo con Batch | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire un processo con Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Eseguire processi in Azure Batch con l'interfaccia della riga di comando di Azure

Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività. Infine viene illustrato come eseguire query sul servizio Batch in modo efficiente per informazioni sulle attività del processo.

## <a name="prerequisites"></a>Prerequisiti

- Installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se questa operazione non è stata ancora eseguita.
- Creare un account Batch, se non è già disponibile. Vedere [Creare un account Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) per uno script di esempio che crea un account.
- Se questa operazione non è stata già eseguita, configurare un'applicazione in modo che venga eseguita da un'attività di avvio. Vedere [Aggiunta di applicazioni ad Azure Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) per uno script di esempio che crea un'applicazione e carica un pacchetto dell'applicazione in Azure.
- Configurare un pool in cui verrà eseguito il processo. Vedere [Gestione dei pool di Azure Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) per uno script di esempio che crea un pool con una configurazione del servizio cloud o della macchina virtuale.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>Pulire un processo

Dopo aver eseguito lo script di esempio precedente, eseguire il comando seguente per rimuovere il processo e tutte le relative attività. Si noti che il pool dovrà essere eliminato separatamente. Vedere [Gestione dei pool di Azure Batch con l'interfaccia della riga di comando di Azure](./batch-cli-sample-manage-pool.md) per altre informazioni sulla creazione e l'eliminazione di pool.

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un processo Batch e le relative attività. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Eseguire l'autenticazione con un account Batch.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Crea un processo Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Aggiorna le proprietà di un processo Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Recupera i dettagli di un processo Batch specificato.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Aggiunge un'attività al processo Batch specificato.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Recupera i dettagli di un'attività dal processo Batch specificato.  |
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | Elenca le attività associate al processo specificato.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).
