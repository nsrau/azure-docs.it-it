<properties
   pageTitle="Convertire database esistenti per l'uso di strumenti dei database elastici"
   description="Convertire database partizionati per l'uso di strumenti dei database elastici mediante la creazione di un gestore mappe partizioni"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/01/2016"
   ms.author="SilviaDoomra"/>

# Convertire database esistenti per l'uso di strumenti dei database elastici

Se si ha una soluzione esistente scalabile orizzontalmente e partizionata, è possibile usare gli strumenti dei database elastici, ad esempio la [libreria client di database elastici](sql-database-elastic-database-client-library.md) e lo [strumento di suddivisione-unione](sql-database-elastic-scale-overview-split-and-merge.md), mediante le tecniche descritte in questo articolo.

Queste tecniche possono essere implementate tramite la [libreria client .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) o gli script PowerShell disponibili nella pagina relativa agli [script degli strumenti dei database elastici - database SQL di Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Negli esempi in questo articolo vengono usati gli script PowerShell.

Si noti che è necessario creare i database prima di eseguire i cmdlet Add-Shard e New-ShardMapManager. I cmdlet non creano i database.

i quattro passaggi della creazione di una distribuzione cloud.

1. Preparare il database di gestione delle mappe partizioni.
2. Creare la mappa partizioni.
3. Preparare le singole partizioni.  
2. Aggiungere i mapping alla mappa partizioni.

Per altre informazioni sull'oggetto ShardMapManager, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md). Per altre informazioni sugli strumenti dei database elastici, vedere la [Panoramica sulle funzionalità di database elastico](sql-database-elastic-scale-introduction.md).

## Preparazione del database di gestione delle mappe partizioni
Come gestore mappe partizioni è possibile usare un database nuovo o uno esistente.

## Passaggio 1: Creare un gestore mappe partizioni
Si noti che un database che agisce da gestore mappe partizioni non deve essere lo stesso della partizione.

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### Per recuperare il gestore mappe partizioni

Dopo la creazione, è possibile recuperare il gestore mappe partizioni con questo cmdlet. Questo passaggio è necessario ogni volta che si deve usare l'oggetto ShardMapManager.

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## Passaggio 2: Creare una mappa partizioni

È possibile scegliere di creare uno dei modelli seguenti:

1. Singolo tenant per database 
2. Più tenant per database (due tipi):
	3. Mapping di tipo intervallo
	4. Mapping di tipo elenco
 

Se si usa un modello di database single-tenant, usare il mapping di tipo elenco. Il modello single-tenant assegna un database per tenant. Si tratta di un modello efficace per gli sviluppatori SaaS in quanto semplifica la gestione.

![Mapping di tipo elenco][1]

Al contrario, il modello di database multi-tenant assegna diversi tenant a un database singolo ed è possibile distribuire gruppi di tenant in più database. Si tratta di un modello valido quando si prevede una quantità limitata di dati per ogni tenant. In questo modello viene assegnato un intervallo di tenant a un database usando il *mapping di tipo intervallo*.
 

![Mapping di tipo intervallo][2]

È anche possibile implementare un modello di database multi-tenant usando il mapping di tipo elenco per assegnare più tenant a un database singolo. Ad esempio, DB1 viene usato per archiviare le informazioni sugli ID tenant 1 e 5 e DB2 archivia i dati per i tenant 7 e 10.

![Tenant multipli su database singolo][3]


## Passaggio 2, opzione 1: Creare una mappa partizioni per un mapping di tipo elenco
Creare una mappa partizioni usando l'oggetto ShardMapManager.

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## Passaggio 2, opzione 2: Creare una mappa partizioni per un mapping di tipo intervallo

Si noti che per usare questo modello di mapping, i valori dell'ID tenant devono essere a intervalli continui ed è accettabile avere gap negli intervalli semplicemente ignorando l'intervallo durante la creazione dei database.

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## Passaggio 2, opzione 3: mapping di tipo elenco su un database singolo
Per l'impostazione di questo modello è necessario anche creare una mappa di tipo elenco, come illustrato nel passaggio 2, opzione 1.

## Passaggio 3: preparare le singole partizioni

Aggiungere ciascuna partizione (database) al gestore mappe partizioni. Ciò consente di preparare i singoli database per l'archiviazione delle informazioni di mapping. Eseguire questo metodo su ogni partizione.
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## Passaggio 4: aggiungere i mapping

L'aggiunta di mapping dipende dal tipo di mappa partizioni creato. Se è stata creata una mappa di tipo elenco, aggiungere mapping di tipo elenco. Se è stata creata una mappa di tipo intervallo, aggiungere mapping di tipo intervallo.

### Passaggio 4, opzione 1: eseguire il mapping dei dati per il mapping di tipo elenco

Eseguire il mapping dei dati aggiungendo un mapping di tipo elenco per ogni tenant.

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### Passaggio 4, opzione 2: eseguire il mapping dei dati per il mapping di tipo intervallo

Aggiungere il mapping di tipo intervallo per tutte le associazioni intervallo ID tenant – database:

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### Passaggio 4, opzione 3: eseguire il mapping dei dati per più tenant su un database singolo

Per ogni tenant eseguire Add-ListMapping (opzione 1, come riportato sopra).


## Verifica dei mapping

È possibile eseguire query per ottenere informazioni sulle partizioni esistenti e sui mapping a esse associati usando i comandi seguenti:

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## Riepilogo

Dopo aver completato l'installazione, è possibile iniziare a usare la libreria client di database elastici. È anche possibile usare il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e le [query su più partizioni](sql-database-elastic-scale-multishard-querying.md).

## Passaggi successivi


Ottenere gli script PowerShell dalla pagina relativa agli [script degli strumenti dei database elastici - database SQL di Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Gli strumenti sono disponibili anche all'indirizzo di GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Usare lo strumento di suddivisione-unione per spostare dati in o da un modello multi-tenant a un modello single-tenant. Vedere la pagina relativa allo [strumento di suddivisione-unione](sql-database-elastic-scale-get-started.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0406_2016-->