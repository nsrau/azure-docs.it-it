---
title: Script di PowerShell per creare un database e una raccolta per l'API MongoDB di Azure Cosmos DB
description: Script di Azure PowerShell - Creare un database e una raccolta per l'API MongoDB di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: ee17656eb479a3eb635cef3e1f858e294e30273d
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280574"
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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un account Cosmos DB. |
| [New-AzCosmosDBMongoDBDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Crea un database dell'API MongoDB. |
| [New-AzCosmosDBMongoDBIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Crea un indice dell'API MongoDB. |
| [New-AzCosmosDBMongoDBCollection](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Crea una raccolta dell'API MongoDB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).
