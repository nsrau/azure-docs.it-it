---
title: Script di PowerShell per creare un contenitore con indicizzazione disattivata in un account Azure Cosmos DB
description: Esempio di script di Azure PowerShell - Creare un contenitore con indicizzazione disattivata in un account Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 729f5071a4f19bd1ac3d6d4779ad6082e6441f12
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489082"
---
# <a name="create-a-container-with-indexing-turned-off-in-an-azure-cosmos-db-account-using-powershell"></a>Creare un contenitore con indicizzazione disattivata in un account Azure Cosmos DB usando PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container with indexing turned off in an Azure Cosmos DB account")]

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
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Crea un database SQL di Cosmos DB. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Crea un oggetto PSSqlIndexingPolicy usato come parametro per New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Ottiene un contenitore SQL di Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).