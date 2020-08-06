---
title: Script di PowerShell per aggiornare la velocità effettiva (UR/s) per le risorse dell'API Cassandra di Azure Cosmos DB
description: Informazioni su come usare uno script di PowerShell per aggiornare le unità elaborate per un keyspace o una tabella nell'API Cassandra di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: e3a2e1f277d0824089ef5288f8966f9cd4b21efc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505323"
---
# <a name="update-rus-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Aggiornare le unità richiesta al secondo per un keyspace o una tabella per Azure Cosmos DB - API Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBCassandraTableThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Ottiene il valore della velocità effettiva della tabella dell'API Cassandra. |
| [Update-AzCosmosDBCassandraTableThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Aggiorna il valore della velocità effettiva della tabella dell'API Cassandra. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Cosmos DB sono disponibili in [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Script di PowerShell per Azure Cosmos DB).