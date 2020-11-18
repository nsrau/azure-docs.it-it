---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire un processo Batch
description: Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b67925f48a9d2dbe0b4559d46d783b500e7a0773
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100916"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Esempio di interfaccia della riga di comando: eseguire un processo e attività con Azure Batch

Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crea l'account Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Crea un pool di nodi di calcolo.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Crea un processo Batch.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Aggiunge un'attività al processo Batch specificato.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Aggiorna le proprietà di un processo Batch.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Recupera i dettagli di un processo Batch specificato.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Recupera i dettagli di un'attività dal processo Batch specificato.  |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
