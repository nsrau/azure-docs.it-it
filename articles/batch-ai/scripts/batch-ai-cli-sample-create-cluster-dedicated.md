---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un cluster Batch per intelligenza artificiale dedicato | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare e gestire un cluster Batch per intelligenza artificiale di nodi dedicati (macchine virtuali)
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 643d2a5a600d018912f09d01f2e018100767f147
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410126"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>Esempio di interfaccia della riga di comando: creare e gestire un cluster Batch per intelligenza artificiale di nodi dedicati

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Questo script mostra alcuni dei comandi disponibili nell'interfaccia della riga di comando di Azure per creare e gestire un cluster Batch per intelligenza artificiale costituito da nodi dedicati (macchine virtuali).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.38 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire i comandi seguenti per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Crea un'area di lavoro Batch per intelligenza artificiale. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Crea un cluster Batch per intelligenza artificiale. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Mostra informazioni su un cluster Batch per intelligenza artificiale. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Elenca i nodi in un cluster Batch per intelligenza artificiale. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Ridimensiona il cluster Batch per intelligenza artificiale.  |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
