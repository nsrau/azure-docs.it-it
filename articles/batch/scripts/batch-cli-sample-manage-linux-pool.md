---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Pool Linux in Batch
description: Questo script mostra alcuni dei comandi disponibili nell'interfaccia della riga di comando di Azure per creare e gestire un pool di nodi di calcolo Linux in Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b2e0fbf44be5718cf5577f6bc9aea436968e2fc3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073562"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Esempio di interfaccia della riga di comando: creare e gestire un pool Linux in Azure Batch

Questo script mostra alcuni dei comandi disponibili nell'interfaccia della riga di comando di Azure per creare e gestire un pool di nodi di calcolo Linux in Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

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
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Elenca gli SKU agente nodo disponibili e le informazioni sull'immagine.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Crea un pool di nodi di calcolo.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Ridimensiona il numero di macchine virtuali in esecuzione nel pool specificato.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Visualizza le proprietà di un pool.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Elenca tutti i nodi di calcolo nel pool specificato.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Riavvia il nodo di calcolo specificato.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Elimina i nodi elencati dal pool specificato.  |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
