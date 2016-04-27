<properties
    pageTitle="Query su database elastico per il partizionamento orizzontale | Microsoft Azure"
    description="Informazioni su come configurare le query elastiche sui partizionamenti orizzontali."    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/28/2016"
    ms.author="torsteng;sidneyh" />

# Query su database elastico per il partizionamento orizzontale

Questo documento illustra come configurare le query su database elastico per scenari di partizionamento orizzontale e come eseguire le query. Per una definizione dello scenario di partizionamento orizzontale, vedere la [panoramica delle query su database elastico (anteprima)](sql-database-elastic-query-overview.md).

![Eseguire una query tra partizioni][1]

Questa funzionalità è inclusa nel [set di funzionalità del database elastico](sql-database-elastic-scale-introduction.md) del database SQL di Azure.
 
## Creazione di oggetti di database

La query su database elastico estende la sintassi T-SQL in modo da fare riferimento ai livelli dati che usano il partizionamento orizzontale per distribuire i dati in molti database. Questa sezione fornisce una panoramica delle istruzioni DDL associate alla query elastica su tabelle con partizionamento orizzontale. Queste istruzioni creano la rappresentazione dei metadati del livello dati con partizionamento orizzontale nel database elastico sottoposto a query. Un prerequisito per l'esecuzione di queste istruzioni è costituito dalla creazione di una mappa partizioni mediante la libreria client del database elastico. Per altre informazioni, vedere [Gestione delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md) oppure usare l'esempio disponibile nell'argomento [Iniziare a usare gli strumenti dei database elastici](sql-database-elastic-scale-get-started.md) per crearne una.

La definizione degli oggetti database per le query su database elastico si basa sulle istruzioni T-SQL seguenti, che vengono illustrate con maggiore dettaglio per lo scenario di partizionamento orizzontale seguente:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREARE/ELIMINARE ORIGINE DATI ESTERNA](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Chiave master con ambito database e credenziali 

Una credenziale rappresenta l'ID utente e la password che verranno usati dalla query elastica per la connessione ai database remoti nel database SQL di Azure. Per creare la chiave master e le credenziali necessarie, usare la sintassi seguente:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

Per eliminare invece le credenziali e la chiave:

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**Nota** Assicurarsi che *< username>* non includa alcun suffisso *"@servername"*.

### 1\.2 Origini dati esterne

Fornire le informazioni sulla mappa partizioni e sul livello dati definendo un'origine dati esterna. L'origine dati esterna fa riferimento alla mappa partizioni. Una query elastica usa quindi l'origine dati esterna e la mappa partizioni sottostante per enumerare i database che partecipano al livello dati. La sintassi per creare un'origine dati esterna è la seguente:

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
Per eliminare invece un'origine dati esterna:

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### Autorizzazioni per CREATE/DROP EXTERNAL DATA SOURCE 

L'utente deve disporre dell'autorizzazione ALTER ANY origine dei dati esterni. Questa autorizzazione è inclusa nell'autorizzazione ALTER DATABASE.

**Esempio**

Nell'esempio seguente viene illustrato l'utilizzo dell'istruzione CREATE per origini dati esterne.

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
È possibile recuperare l'elenco di origini dati esterne corrente della vista del catalogo seguenti:

	select * from sys.external_data_sources; 

Si noti che le stesse credenziali vengono usate per leggere la mappa partizioni e per accedere ai dati nelle partizioni durante l'elaborazione di una query elastica.

### 1\.3 Tabelle esterne 
 
La query elastica estende il DDL della tabella esterna in modo da fare riferimento a tabelle esterne con partizionamento orizzontale in diversi database. La definizione della tabella esterna include gli aspetti seguenti:

* **Schema**: il DDL della tabella esterna definisce uno schema che può essere usato dalle query. Lo schema fornito nella definizione della tabella esterna deve corrispondere allo schema delle tabelle nelle partizioni in cui sono archiviati i dati effettivi. 

* **Distribuzione dei dati**: il DDL della tabella esterna definisce la distribuzione dei dati usata per distribuire i dati nel livello dati. Si noti che il database SQL di Azure non convalida la distribuzione definita nella tabella esterna rispetto alla distribuzione effettiva nelle partizioni. Occorre assicurare che la distribuzione effettiva dei dati sulle partizioni corrisponda alla definizione della tabella esterna.

* **Riferimento al livello dati**: il DDL della tabella esterna fa riferimento a un'origine dati esterna. L'origine dati esterna specifica una mappa partizioni che fornisce alla tabella esterna le informazioni necessarie per individuare tutti i database nel livello dati.

Se si usa un'origine dati esterna, come illustrato nella sezione precedente, la sintassi per la creazione e l'eliminazione di tabelle esterne è la seguente:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

La clausola DATA\_SOURCE definisce l'origine dati esterna, ovvero una mappa partizioni nel caso del partizionamento orizzontale, usata per la tabella esterna.

Le clausole SCHEMA\_NAME e OBJECT\_NAME consentono di mappare la definizione della tabella esterna a una tabella in uno schema diverso sulla partizione o a una tabella con un nome diverso, rispettivamente. Se queste clausole vengono omesse, si presupporrà che lo schema dell'oggetto remoto sia "dbo" e che il relativo nome sia identico al nome della tabella esterna in fase di definizione.

Le clausole SCHEMA\_NAME e OBJECT\_NAME sono particolarmente utili se il nome della tabella remota è già usato nel database in cui si vuole creare la tabella esterna. Questo problema si verifica, ad esempio, quando si vuole definire una tabella esterna per ottenere una visualizzazione aggregata delle viste del catalogo o delle viste a gestione dinamica (DMV) nel livello dati con scalabilità orizzontale. Poiché le viste del catalogo e le DMV esistono già localmente, non sarà possibile usare i rispettivi nomi per la definizione della tabella esterna. Usare invece un nome diverso e usare il nome della vista del catalogo o della DMV nelle clausole SCHEMA\_NAME e/o OBJECT\_NAME. Vedere l'esempio seguente.

La clausola DISTRIBUTION specifica la distribuzione dei dati usata per questa tabella:

* SHARDED indica che i dati per questa tabella sono sottoposti a partizionamento orizzontale nei database nella mappa partizioni. La chiave di partizionamento per la distribuzione dei dati viene acquisita nel parametro <sharding_column_name>.  

* REPLICATED indica che copie identiche della tabella sono presenti in ogni database nella mappa partizioni. Il database SQL di Azure non gestisce le copie della tabella. Sarà quindi necessario assicurarsi che le repliche siano identiche in tutti i database.

* ROUND\_ROBIN indica che la tabella viene distribuita usando il partizionamento orizzontale. È stata tuttavia usata una distribuzione dipendente dall'applicazione.

Query Processor utilizza le informazioni fornite nella clausola DISTRIBUTION per generare i piani di query più efficienti.

Per eliminare le tabelle esterne, usare l'istruzione seguente:

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Autorizzazioni per CREATE/DROP EXTERNAL TABLE:** sono necessarie le autorizzazioni di tipo ALTER ANY EXTERNAL DATA SOURCE, richieste anche per fare riferimento all'origine dati sottostante.

**Considerazioni sulla sicurezza:** gli utenti con accesso alla tabella esterna ottengono automaticamente l'accesso alle tabelle remote sottostanti con le credenziali specificate nella definizione dell'origine dati esterna. È necessario gestire con attenzione l'accesso alla tabella esterna, in modo da evitare l'elevazione indesiderata dei privilegi tramite le credenziali dell'origine dati esterna. È possibile usare le normali autorizzazioni SQL per CONCEDERE o REVOCARE l'accesso a una tabella esterna, come se fosse una tabella normale.

**Esempio**: l'esempio seguente illustra come creare una tabella esterna.

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

Nell'esempio seguente viene illustrato come recuperare l'elenco di tabelle esterni dal database corrente:

	select * from sys.external_tables; 

## Query 

### 2\.1 Query T-SQL a massima fedeltà 

Dopo aver definito l'origine dati esterna e le tabelle esterne, è ora possibile usare la sintassi T-SQL completa sulle tabelle esterne.

**Esempio per il partizionamento orizzontale:** la query seguente esegue un join a tre vie tra magazzini, ordini e righe di ordine e vengono usate diverse funzioni di aggregazione e un filtro selettivo. Si presuppone che (1) sia usato il partizionamento orizzontale e che (2) i magazzini, gli ordini e le righe di ordine siano partizionati orizzontalmente in base alla colonna warehouse id e che la query elastica possa collocare i join sulle partizioni ed elaborare la parte dispendiosa della query in parallelo sulle partizioni.

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
### 2\.2 Stored procedure per l'esecuzione remota di T-SQL

La query elastica introduce anche una stored procedure che fornisce l'accesso diretto alle partizioni. La stored procedure è denominata sp\_execute\_remote e può essere usata per eseguire stored procedure remote o codice T-SQL su database remoti. È necessario specificare i seguenti parametri:
* Nome dell'origine dati (nvarchar): il nome dell'origine dati esterna di tipo RDBMS. 
* Query (nvarchar): la query T-SQL da eseguire in ogni partizione. 
* Dichiarazione del parametro (nvarchar) - Facoltativo: stringa con definizioni del tipo di dati per i parametri usati nel parametro della query, ad esempio sp\_executesql. 
* Elenco di valori dei parametri (facoltativo): elenco delimitato da virgole di valori dei parametri, ad esempio sp\_executesql.

La stored procedure sp\_execute\_remote usa l'origine dati esterna specificata nei parametri di chiamata per eseguire l'istruzione T-SQL inclusa nei database remoti. Usa le credenziali dell'origine dati esterna per connettersi al database di gestione shardmap e ai database remoti.

Esempio:

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 

## Connettività per gli strumenti  

Usare le normali stringhe di connessione di SQL Server per connettere l'applicazione, gli strumenti di Business Intelligence e di integrazione dei dati al database con le definizioni delle tabelle esterne. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Fare quindi riferimento al database elastico sottoposto a query in modo analogo a qualsiasi altro database di SQL Server connesso allo strumento e usare le tabelle esterne dallo strumento o dall'applicazione come se fossero tabelle locali.

## Procedure consigliate 

* Assicurarsi che il database di endpoint della query elastica abbia l'accesso al database di mappe partizioni e a tutte le partizioni attraverso i firewall del database SQL.  

* La query elastica non convalida o impone la distribuzione di dati definita dalla tabella esterna. Se la distribuzione di dati effettivo è diversa dalla distribuzione specificata nella definizione di tabella, le query possono restituire risultati imprevisti.

* La query elastica non esegue attualmente l'eliminazione di partizioni quando i predicati sulla chiave di partizionamento consentirebbero l'esclusione sicura di alcune partizioni dall'elaborazione.

* La query elastica funziona in modo ottimale per le query in cui la maggior parte dei calcoli può essere eseguita sulle partizioni. In genere si ottiene le migliori prestazioni di query con predicati del filtro selettivo che può essere valutata in join le partizioni tramite le chiavi di partizionamento che possono essere eseguite in modo su tutte le partizioni allineate alle partizioni. Altri modelli di query potrebbero richiedere il caricamento di quantità elevate di dati dalle partizioni al nodo head e potrebbero offrire prestazioni ridotte.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0413_2016-->