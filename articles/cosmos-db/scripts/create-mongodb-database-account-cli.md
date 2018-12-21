---
title: Creare un account API di Azure Cosmos DB MongoDB, un database e una raccolta con uno script dell'interfaccia della riga di comando di Azure
description: Creare un account API di Azure Cosmos DB MongoDB, un database e una raccolta con un esempio di script dell'interfaccia della riga di comando di Azure
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 7c7f3f6907f0c21e3c66a3d814dc355476bec553
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958350"
---
# <a name="azure-cosmos-db-create-an-mongodb-api-account-using-azure-cli"></a>Azure Cosmos DB: creare un account API MongoDB con l'interfaccia della riga di comando di Azure

Questo script dell'interfaccia della riga di comando di Azure di esempio permette di creare un account API di Azure Cosmos DB MongoDB, un database e una raccolta.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-mongodb-account/create-cosmosdb-mongodb-account.sh "Create an Azure Cosmos DB MongoDB API account, database, and collection.")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Crea un database di Azure Cosmos DB. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Crea una raccolta di Azure Cosmos DB per MongoDB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).
