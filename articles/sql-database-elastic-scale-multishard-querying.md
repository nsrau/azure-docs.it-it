<properties title="Multi-Shard Querying" pageTitle="Esecuzione di query su più partizioni" description="Esecuzione di query in sottopartizioni usando API Elastic Scale." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, multi-shard, multishard, querying" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Esecuzione di query su più partizioni
La funzionalità di **query su più partizioni** viene usata per attività che richiedono l'esecuzione di query su più partizioni, ad esempio la raccolta o la creazione di report sui dati (a differenza del [routing dipendente dai dati],(./sql-database-elastic-scale-data-dependent-routing.md)che opera su un'unica partizione). 

La libreria client di Scalabilità elastica introduce un nuovo spazio dei nomi denominato **Microsoft.Azure.SqlDatabase.ElasticScale.Query**, che consente di eseguire query su più partizioni usando una singola query con un unico risultato. Fornisce l'astrazione delle query su una raccolta di partizioni, nonché criteri di esecuzione alternativi, in particolare risultati parziali, per la gestione degli errori quando si eseguono query su molte partizioni.  

Il punto di ingresso principale in una query su più partizioni è la classe **MultiShardConnection**. Come nel caso del routing dipendente dai dati, l'API segue la familiare esperienza delle classi e dei metodi **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)**. Con la libreria **SqlClient**, il primo passaggio consiste nel creare un oggetto **SqlConnection**, quindi creare un oggetto **SqlCommand** per la connessione e successivamente eseguire il comando mediante uno dei metodi **Execute**. Infine, **SqlDataReader** esegue l'iterazione nei set di risultati restituiti dall'esecuzione del comando. L'esperienza delle API di query su più partizioni prevede i passaggi seguenti: 

1. Creazione di una classe **MultiShardConnection**.
2. Creazione di una classe **MultiShardCommand** per una **MultiShardConnection**.
3. Esecuzione del comando.
4. Utilizzo dei risultati mediante la classe **MultiShardDataReader**. 

Un'importante differenza risiede nella costruzione delle connessioni a più partizioni. Mentre **SqlConnection** agisce su un singolo database, **MultiShardConnection** accetta come input una ***raccolta di partizioni***. È possibile popolare la raccolta di partizioni da una mappa partizioni. La query viene quindi eseguita sulla raccolta di partizioni usando la semantica di **UNION ALL** per assemblare un unico risultato complessivo. Facoltativamente, è possibile aggiungere all'output il nome della partizione di origine della riga usando la proprietà **ExecutionOptions** nel comando. Il codice seguente illustra l'uso delle query su più partizioni mediante uno specifico oggetto **ShardMap** denominato * myShardMap *. 

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
 

Notare la chiamata a **myShardMap.GetShards()**. Questo metodo recupera tutte le partizioni dalla mappa partizioni e fornisce un metodo semplice per eseguire una query su tutte le partizioni presenti nella mappa. La raccolta di partizioni per una query su più partizioni può essere ulteriormente perfezionata eseguendo una query LINQ sulla raccolta restituita dalla chiamata a **myShardMap.GetShards()**. In combinazione con i criteri sui risultati parziali, la funzionalità corrente di query su più partizioni è stata progettata per supportare correttamente da decine fino a centinaia di partizioni.
Un'attuale limitazione delle query su più partizioni è la mancanza di convalida delle partizioni e degli shardlet interrogati. Mentre il routing dipendente dai dati verifica che una determinata partizione faccia parte della mappa partizioni al momento dell'esecuzione delle query, le query su più partizioni non eseguono questo controllo. Questo può provocare l'esecuzione di query su partizioni che sono state rimosse dalla mappa partizioni.

###Query su più partizioni e operazioni di suddivisione e unione

Le query su più partizioni non verificano se gli shardlet presenti nella partizione interrogata sono oggetto di operazioni di divisione/unione in corso. Questo può causare incoerenze se righe dello stesso shardlet compaiono per più partizioni nella stessa query su più partizioni. Tenere presente queste limitazioni e completare le operazioni di suddivisione/unione in corso e le modifiche alla mappa partizioni prima dell'esecuzione di query su più partizioni.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
