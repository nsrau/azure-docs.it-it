---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Eseguire un processo con Batch | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Eseguire un processo con Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Eseguire processi in Azure Batch con l'interfaccia della riga di comando di Azure

Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività.
Per eseguire questo script è necessario che sia già stato configurato un account Batch e sia stato configurato sia un pool sia un'applicazione. Per altre informazioni, vedere gli [script di esempio](../batch-cli-samples.md) per ognuno di questi argomenti.

Se necessario, installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per accedere ad Azure.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Eseguire il processo")]

## <a name="clean-up-job"></a>Pulire un processo

Dopo aver eseguito lo script di esempio precedente, eseguire il comando seguente per rimuovere il processo e tutte le relative attività. Si noti che il pool dovrà essere eliminato separatamente; vedere l'[esercitazione sulla gestione dei pool](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un processo Batch e le relative attività. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Eseguire l'autenticazione con un account Batch.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Crea un processo Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Aggiorna le proprietà di un processo Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | Recupera i dettagli di un processo Batch specificato.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | Aggiunge un'attività al processo Batch specificato.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | Recupera i dettagli di un'attività dal processo Batch specificato.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).

