---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un cluster Batch per intelligenza artificiale con un file di configurazione | Microsoft Docs
description: Esempio di Script della riga di comando Azure - Creare un cluster Batch per intelligenza artificiale specificando le impostazioni di configurazione in un file JSON.
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
ms.date: 08/16/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 41a3a801214ff00c01397034e26fde6946ab97f0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407814"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>Esempio di interfaccia della riga di comando: Creare un cluster Batch per intelligenza artificiale usando un file di configurazione del cluster

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Questo script illustra come usare un file di configurazione JSON per specificare le impostazioni per un cluster Batch per intelligenza artificiale. Usare queste impostazioni invece dei corrispondenti parametri della riga di comando per `az batchai cluster create`. Un file di configurazione è utile quando è necessario montare più sistemi di file nei nodi del cluster o si intende usare una configurazione identica in più cluster.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando in locale, questa guida richiede l'esecuzione della versione 2.0.38 o successiva dell'interfaccia della riga di comando di Azure. Per determinare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire i comandi seguenti per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea un account di archiviazione. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Crea una condivisione file in un account di archiviazione. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Crea un'area di lavoro Batch per intelligenza artificiale. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Crea un cluster Batch per intelligenza artificiale. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Mostra informazioni su un cluster Batch per intelligenza artificiale. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
