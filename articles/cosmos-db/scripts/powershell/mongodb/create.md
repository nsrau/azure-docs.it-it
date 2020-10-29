---
title: Script di PowerShell per creare un database e una raccolta per l'API MongoDB di Azure Cosmos DB
description: Script di Azure PowerShell - Creare un database e una raccolta per l'API MongoDB di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 6945bf100ed33846de720addd0d97dec9593e40c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489201"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>Creare un database e una raccolta per Azure Cosmos DB - API MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-create.ps1 "Create a database and collection for MongoDB API")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un account Cosmos DB. |
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Crea un database dell'API MongoDB. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Crea un indice dell'API MongoDB. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Crea una raccolta dell'API MongoDB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).