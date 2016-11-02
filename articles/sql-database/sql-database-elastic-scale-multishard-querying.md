<properties 
	pageTitle="L'esecuzione di query su più partizioni | Microsoft Azure" 
	description="Eseguire query tra partizioni utilizzando la libreria client dei database elastici." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="torsteng"/>

# Esecuzione di query su più partizioni

## Panoramica

Con gli [strumenti del Database elastico](sql-database-elastic-scale-introduction.md), è possibile creare soluzioni di database partizionato. La funzionalità di **query su più partizioni** viene usata per attività che richiedono l'esecuzione di una query su più partizioni, ad esempio la raccolta o la creazione di report sui dati (Diversamente dal [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md), che consente di eseguire tutto il lavoro in una singola partizione).

## Panoramica

1. Ottenere un [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) o [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) con il metodo [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) o [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx). Vedere [**Creazione di un oggetto ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [**Ottenere una mappa RangeShardMap o ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Creare un oggetto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**.
2. Creare un **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Impostare la **[proprietà CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** su un comando T-SQL.
3. Eseguire il comando chiamando il **[metodo ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Vedere i risultati usando la **[classe MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## Esempio

Il codice seguente illustra l'uso delle query su più partizioni mediante uno specifico oggetto **ShardMap** denominato *myShardMap*.

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
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

 
Un'importante differenza risiede nella costruzione delle connessioni a più partizioni. Mentre **SqlConnection** agisce su un singolo database, **MultiShardConnection** accetta come input una ***raccolta di partizioni***. Popolare la raccolta di partizioni da una mappa partizioni. La query viene quindi eseguita sulla raccolta di partizioni usando la semantica di **UNION ALL** per assemblare un unico risultato complessivo. Facoltativamente, è possibile aggiungere all'output il nome della partizione di origine della riga usando la proprietà **ExecutionOptions** nel comando.

Notare la chiamata a **myShardMap.GetShards()**. Questo metodo recupera tutte le partizioni dalla mappa partizioni e fornisce un metodo semplice per eseguire una query su tutti i database rilevanti. La raccolta di partizioni per una query su più partizioni può essere ulteriormente perfezionata eseguendo una query LINQ sulla raccolta restituita dalla chiamata a **myShardMap.GetShards()**. In combinazione con i criteri sui risultati parziali, la funzionalità corrente di query su più partizioni è stata progettata per supportare correttamente da decine fino a centinaia di partizioni.

Un'attuale limitazione delle query su più partizioni è la mancanza di convalida delle partizioni e degli shardlet interrogati. Mentre il routing dipendente dai dati verifica che una determinata partizione faccia parte della mappa partizioni al momento dell'esecuzione delle query, le query su più partizioni non eseguono questo controllo. Questo può determinare l'esecuzione di query su più partizioni in database che sono stati rimossi dalla mappa partizioni.

## Query su più partizioni e operazioni di suddivisione e unione

Le query su più partizioni non verificano se gli shardlet presenti nel database interrogato sono oggetto di operazioni di divisione/unione in corso. (Vedere [Scalabilità tramite lo strumento di suddivisione-unione del database elastico](sql-database-elastic-scale-overview-split-and-merge.md).) Questo può causare incoerenze se righe dello stesso shardlet compaiono per più database nella stessa query su più partizioni. Tenere presente queste limitazioni e completare le operazioni di suddivisione-unione in corso e le modifiche alla mappa partizioni prima dell'esecuzione di query su più partizioni.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## Vedere anche
Classi e metodi **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**.


Gestire le partizioni con la [libreria client dei database elastici](sql-database-elastic-database-client-library.md). Comprende un nuovo spazio dei nomi denominato [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx), che consente di eseguire query su più partizioni usando una singola query con un unico risultato. Fornisce l'astrazione delle query su una raccolta di partizioni, nonché criteri di esecuzione alternativi, in particolare risultati parziali, per la gestione degli errori quando si eseguono query su molte partizioni.

 

<!---HONumber=AcomDC_0420_2016-->