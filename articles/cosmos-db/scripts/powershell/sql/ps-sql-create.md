---
title: Script di PowerShell per creare un database e un contenitore per l'API SQL di Azure Cosmos DB
description: Script di Azure PowerShell - Creare un database e un contenitore per l'API SQL di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 434cc6df326a0028f327c6d9e2eda0d19989ca01
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653172"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Creare un database e un contenitore per Azure Cosmos DB - API SQL

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

Questo script crea un account Cosmos per l'API SQL (Core) in due aree con coerenza a livello di sessione, un database e un contenitore con una chiave di partizione, criteri di indicizzazione personalizzati, criteri di chiave univoca, durata TTL, unità elaborate dedicate e criteri di risoluzione dei conflitti basati sulla priorità dell'ultima scrittura con un percorso di risoluzione dei conflitti personalizzato che verrà usato quando `multipleWriteLocations=true`.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un nuovo account Cosmos DB. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Crea un nuovo database SQL Cosmos DB o ne aggiorna uno esistente. |
| [New-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Crea un nuovo oggetto PSSqlUniqueKey SQL di Cosmos DB usato come parametro per New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Crea un nuovo oggetto PSSqlUniqueKeyPolicy SQL di Cosmos DB usato come parametro per Set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Crea un nuovo oggetto PSCompositePath SQL di Cosmos DB usato come parametro per New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Crea un nuovo oggetto di tipo PSIndexes usato come parametro per Set-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Crea un nuovo oggetto di tipo PSIncludedPath usato come parametro per New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Crea un nuovo oggetto di tipo PSSqlIndexingPolicy usato come parametro per Set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Crea un nuovo oggetto di tipo PSSqlConflictResolutionPolicy usato come parametro per Set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Crea un nuovo contenitore SQL Cosmos DB o ne aggiorna uno esistente. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Cosmos DB sono disponibili in [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Script di PowerShell per Azure Cosmos DB).
