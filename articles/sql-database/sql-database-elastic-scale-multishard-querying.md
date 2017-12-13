---
title: Eseguire query su database SQL di Azure partizionati | Documentazione Microsoft
description: Eseguire query tra partizioni utilizzando la libreria client dei database elastici.
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: torsteng
ms.openlocfilehash: 33128357bd5b2bd744c5c1c3032f658ebe865d49
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="multi-shard-querying"></a>Esecuzione di query su più partizioni
## <a name="overview"></a>Panoramica
Con gli [strumenti dei database elastici](sql-database-elastic-scale-introduction.md) è possibile creare soluzioni di database partizionati. La funzionalità di **query su più partizioni** viene usata per attività che richiedono l'esecuzione di una query su più partizioni, ad esempio la raccolta o la creazione di report sui dati, a differenza del [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md), che consente di eseguire tutto il lavoro su una singola partizione. 

1. Ottenere **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) or **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) con il metodo **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)) o **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)). Vedere **[Creazione di un oggetto ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** e **[Ottenere una mappa RangeShardMap o ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Creare un oggetto **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)).
3. Creare un oggetto **MultiShardStatement o MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Impostare la **proprietà CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) su un comando T-SQL.
5. Eseguire il comando chiamando il metodo **ExecuteQueryAsync or ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)).
6. Visualizzare i risultati usando la classe **MultiShardResultSet or MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)). 

## <a name="example"></a>Esempio
Il codice seguente illustra l'uso delle query su più partizioni mediante uno specifico oggetto **ShardMap** denominato *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

Un'importante differenza risiede nella costruzione delle connessioni a più partizioni. Mentre **SqlConnection** agisce su un singolo database, **MultiShardConnection** accetta come input una ***raccolta di partizioni***. Popolare la raccolta di partizioni da una mappa partizioni. La query viene quindi eseguita sulla raccolta di partizioni usando la semantica di **UNION ALL** per assemblare un unico risultato complessivo. Facoltativamente, è possibile aggiungere all'output il nome della partizione di origine della riga usando la proprietà **ExecutionOptions** nel comando. 

Notare la chiamata a **myShardMap.GetShards()**. Questo metodo recupera tutte le partizioni dalla mappa partizioni e fornisce un metodo semplice per eseguire una query su tutti i database rilevanti. La raccolta di partizioni per una query su più partizioni può essere ulteriormente perfezionata eseguendo una query LINQ sulla raccolta restituita dalla chiamata a **myShardMap.GetShards()**. In combinazione con i criteri sui risultati parziali, la funzionalità corrente di query su più partizioni è stata progettata per supportare correttamente da decine fino a centinaia di partizioni.

Un'attuale limitazione delle query su più partizioni è la mancanza di convalida delle partizioni e degli shardlet interrogati. Mentre il routing dipendente dai dati verifica che una determinata partizione faccia parte della mappa partizioni al momento dell'esecuzione delle query, le query su più partizioni non eseguono questo controllo. Questo può determinare l'esecuzione di query su più partizioni in database che sono stati rimossi dalla mappa partizioni.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Query su più partizioni e operazioni di suddivisione e unione
Le query su più partizioni non verificano se gli shardlet presenti nel database interrogato sono oggetto di operazioni di divisione/unione in corso. Vedere [Scaling using the Elastic Database split-merge tool](sql-database-elastic-scale-overview-split-and-merge.md) (Ridimensionamento con lo strumento di divisione e unione del database elastico). Questo può causare incoerenze se righe dello stesso shardlet compaiono per più database nella stessa query su più partizioni. Tenere presente queste limitazioni e completare le operazioni di suddivisione-unione in corso e le modifiche alla mappa partizioni prima dell'esecuzione di query su più partizioni.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


