---
title: Creare una tabella dell'API Tabella per Azure Cosmos DB
description: Creare una tabella dell'API Tabella per Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 92b7e78629fd3cf55425588db54b808746adfd61
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431487"
---
# <a name="create-an-azure-cosmos-table-api-account-and-table-using-azure-cli"></a>Creare un account e una tabella dell'API Tabella di Azure Cosmos tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.9.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/create.sh "Create an Azure Cosmos DB Table API account and table.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crea un account Azure Cosmos DB. |
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Crea una tabella dell'API Tabella di Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure Cosmos DB, vedere la relativa [documentazione](/cli/azure/cosmosdb).

Tutti gli esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nel [repository GitHub dell'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
