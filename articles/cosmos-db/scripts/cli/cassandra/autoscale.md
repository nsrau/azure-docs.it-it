---
title: Creare un keyspace e una tabella con scalabilità automatica Cassandra per Azure Cosmos DB
description: Creare un keyspace e una tabella con scalabilità automatica Cassandra per Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 7/29/2020
ms.openlocfilehash: 0f11d022567a5e221a306cd7c34f31cae0ef48aa
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433423"
---
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-with-autoscale-using-azure-cli"></a>Creare un account, un keyspace e una tabella con scalabilità automatica dell'API Cassandra di Azure Cosmos con l'interfaccia della riga di comando di Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.9.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/autoscale.sh "Create an Azure Cosmos DB Cassandra API account, keyspace, and table with autoscale.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Crea un keyspace Cassandra di Azure Cosmos. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Crea una tabella Cassandra di Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure Cosmos DB, vedere la relativa [documentazione](/cli/azure/cosmosdb).

Tutti gli esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nel [repository GitHub dell'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
