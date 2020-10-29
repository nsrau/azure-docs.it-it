---
title: Script di PowerShell per elencare e ottenere le risorse dell'API Cassandra di Azure Cosmos DB
description: Script di Azure PowerShell - Operazioni list e get di Azure Cosmos DB per l'API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 8cd9abf02d66c6bed5720129d0b19c9a7545fac7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488317"
---
# <a name="list-and-get-keyspaces-and-tables-for-azure-cosmos-db---cassandra-api"></a>Elencare e ottenere spazi delle chiavi e tabelle per Azure Cosmos DB - API Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-list-get.ps1 "List or get keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Elenca gli account Cosmos DB o ottiene un account Cosmos DB specificato. |
| [Get-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspace) | Elenca i keyspace dell'API Cassandra di Cosmos DB in un account oppure ottiene un keyspace dell'API Cassandra di Cosmos DB specificato in un account. |
| [Get-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratable) | Elenca le tabelle dell'API Cassandra di Cosmos DB in un keyspace o ottiene una tabella dell'API Cassandra di Cosmos DB specificata in un keyspace. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).