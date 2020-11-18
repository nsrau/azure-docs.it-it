---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Pool Windows in Batch
description: Questo script mostra alcuni dei comandi disponibili nell'interfaccia della riga di comando di Azure per creare e gestire un pool di nodi di calcolo Windows in Azure Batch.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb18f9d8777c17d31a3ab246603df0d9fa162467
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100940"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Esempio di interfaccia della riga di comando: creare e gestire un pool Windows in Azure Batch

Questo script mostra alcuni dei comandi disponibili nell'interfaccia della riga di comando di Azure per creare e gestire un pool di nodi di calcolo Windows in Azure Batch. È possibile configurare un pool Windows in due modi: con una configurazione Servizi cloud o con una configurazione macchina virtuale. Questo esempio illustra come creare un pool Windows con la configurazione Servizi Cloud.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando. |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Crea un pool di nodi di calcolo.  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | Aggiorna le proprietà di un pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Abilita la scalabilità automatica in un pool e applica una formula.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Visualizza le proprietà di un pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Disabilita la scalabilità automatica in un pool. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
