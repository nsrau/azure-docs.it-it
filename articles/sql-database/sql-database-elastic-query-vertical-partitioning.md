<properties
    pageTitle="Query su database elastico per le query tra database (partizionamento verticale) | Microsoft Azure"
    description="Informazioni su come configurare le query tra database su partizioni verticali."    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="torsteng;sidneyh" />

# Query su database elastico per le query tra database (partizionamento verticale)

Questo documento illustra come configurare le query elastiche per scenari di query tra database (partizionamento verticale) e come eseguire le query. Per una definizione dello scenario di partizionamento verticale, vedere [Panoramica delle query su database elastico del database SQL di Azure (anteprima)](sql-database-elastic-query-overview.md).

## Creazione di oggetti di database

Per gli scenari di partizionamento verticale, la query elastica estende il DDL di T-SQL corrente in modo da fare riferimento alle tabelle archiviate sui database remoti. Questa sezione fornisce una panoramica delle istruzioni DDL per configurare la query elastica per l'accesso trasparente alle tabelle remote. Queste istruzioni DDL consentono di creare la rappresentazione dei metadati delle tabelle remote nel database locale.

**NOTA**: a differenza del partizionamento orizzontale, queste istruzioni DDL non dipendono dalla definizione di un livello dati con una mappa partizioni tramite la libreria client del database elastico.

La definizione degli oggetti database per le query su database elastico si basa sulle istruzioni T-SQL seguenti, che vengono illustrate con maggiore dettaglio per lo scenario di partizionamento verticale seguente:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Chiave master con ambito database e credenziali 

Una credenziale rappresenta l'ID utente e la password che verranno usati dalla query elastica per la connessione ai database remoti nel database SQL di Azure. Per creare la chiave master e le credenziali necessarie, usare la sintassi seguente:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]
    
Per eliminare la credenziale:
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
Assicurarsi che *< username>* non includa alcun suffisso *"@servername"*.

### 1\.2 Origini dati esterne

Per fornire le informazioni sui database remoti alla query elastica, è possibile definire le origini dati esterne. La sintassi per la creazione e l'eliminazione di origini dati esterne è definita come segue:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

Si noti il parametro TYPE che definisce questa origine dati come RDBMS.

Per eliminare un'origine dati esterna, è possibile utilizzare l'istruzione seguente:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### Autorizzazioni per CREATE/DROP EXTERNAL DATA SOURCE 

L'utente deve disporre dell'autorizzazione ALTER ANY origine dei dati esterni. Questa autorizzazione è inclusa nell'autorizzazione ALTER DATABASE.

**Esempio**

Nell'esempio seguente viene illustrato l'utilizzo dell'istruzione CREATE per origini dati esterne.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
Per recuperare l'elenco di origini dati esterne correnti dalla vista del catalogo seguente:

    select * from sys.external_data_sources; 

### 1\.3 Tabelle esterne 

La query elastica estende la sintassi esistente della tabella esterna per definire le tabelle esterne che usano origini dati esterne di tipo RDBMS. Una definizione di tabella esterna per il partizionamento verticale comprende gli aspetti seguenti:

* **Schema**: il DDL della tabella esterna definisce uno schema che può essere usato dalle query. Lo schema fornito nella definizione della tabella esterna deve corrispondere allo schema delle tabelle nel database remoto in cui sono archiviati i dati effettivi. 

* **Riferimento al database remoto**: il DDL della tabella esterna fa riferimento a un'origine dati esterna. L'origine dati esterna specifica il nome del server logico e il nome del database remoto in cui sono archiviati i dati effettivi della tabella.

Se si usa un'origine dati esterna, come illustrato nella sezione precedente, la sintassi per la creazione di tabelle esterne è la seguente:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

La clausola DATA\_SOURCE definisce l'origine dati esterna, ovvero il database remoto nel caso del partizionamento verticale, usata per la tabella esterna.

Le clausole SCHEMA\_NAME e OBJECT\_NAME consentono di mappare la definizione della tabella esterna a una tabella in uno schema diverso sul database remoto o a una tabella con un nome diverso, rispettivamente. Ciò risulta utile se si vuole definire una tabella esterna per in una vista del catalogo o una DMV nel database remoto o in qualsiasi altra situazione in cui il nome della tabella remota sia già usato in locale.

L'istruzione DDL seguente elimina una definizione di tabella esterna esistente da un catalogo locale. Non ha alcun impatto sul database remoto.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Autorizzazioni per CREATE/DROP EXTERNAL TABLE**: le autorizzazioni di tipo ALTER ANY EXTERNAL DATA SOURCE sono necessarie per il DDL di tabelle esterne, che è richiesto anche per fare riferimento all'origine dati sottostante.

**Considerazioni sulla sicurezza:** gli utenti con accesso alla tabella esterna ottengono automaticamente l'accesso alle tabelle remote sottostanti con le credenziali specificate nella definizione dell'origine dati esterna. È necessario gestire con attenzione l'accesso alla tabella esterna, in modo da evitare l'elevazione indesiderata dei privilegi tramite le credenziali dell'origine dati esterna. È possibile usare le normali autorizzazioni SQL per CONCEDERE o REVOCARE l'accesso a una tabella esterna, come se fosse una tabella normale.


 **Esempio**: l'esempio seguente illustra come creare una tabella esterna.

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

Nell'esempio seguente viene illustrato come recuperare l'elenco di tabelle esterni dal database corrente:

	select * from sys.external_tables; 

## Query

### 2\.1 Query T-SQL a massima fedeltà 

Dopo aver definito l'origine dati esterna e le tabelle esterne, è ora possibile usare la sintassi T-SQL completa sulle tabelle esterne.

**Esempio per il partizionamento verticale**: la query seguente esegue un join a tre vie tra due tabelle locali per ordini e righe di ordine e la tabella remota per i clienti. Ecco un esempio di caso di utilizzo dei dati di riferimento per la query elastica:

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## Connettività per gli strumenti

È possibile usare le normali stringhe di connessione di SQL Server per connettere gli strumenti di Business Intelligence e di integrazione dei dati ai database nel server del database SQL per cui sono abilitate le query elastiche e sono state definite tabelle esterne. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Fare quindi riferimento al database elastico sottoposto a query e alle relative tabelle esterne come se fosse un qualsiasi altro database di SQL Server a cui ci si può connettere con lo strumento.

## Procedure consigliate 
 
* Assicurarsi che il database di endpoint della query elastica abbia l'accesso al database remoto mediante l'abilitazione dell'accesso per i servizi di Azure nella rispettiva configurazione del firewall del database SQL. Assicurarsi anche che le credenziali fornite nella definizione dell'origine dati esterna possano accedere correttamente al database remoto e abbiano le autorizzazioni necessarie per accedere alla tabella remota.  

* La query elastica funziona in modo ottimale per le query in cui la maggior parte dei calcoli può essere eseguita sui database remoti. In genere si ottengono le prestazioni migliori per le query tramite i predicati di filtro selettivo, che possono essere valutati sui database remoti, o mediante join che possono essere eseguiti completamente nel database remoto. Altri modelli di query potrebbero richiedere il caricamento di quantità elevate di dati dal database remoto e potrebbero offrire prestazioni ridotte.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Nov15_HO3-->