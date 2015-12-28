<properties
   pageTitle="Risoluzione dei problemi | Microsoft Azure"
   description="Risoluzione dei problemi relativi a SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/11/2015"
   ms.author="twounder"/>

# Risoluzione dei problemi
Il seguente argomento fornisce un elenco di alcuni dei problemi più comuni che i clienti riscontrano con SQL Data Warehouse di Azure.

## Connettività
La connessione all’ SQL Data Warehouse di Azure può avere esito negativo per due motivi:

- Le regole del firewall non sono impostate
- Si utilizzano strumenti/protocolli non supportati

### Regole del firewall
I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti possano accedere ai database. I firewall sono sicuri per impostazione predefinita, pertanto che è necessario consentire l'accesso al proprio indirizzo IP prima di potersi connettere.

Per configurare il firewall per l'accesso, attenersi alla procedura riportata nella sezione [Configurare l’accesso al firewall del server per l'indirizzo IP del client](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) della pagina [Provisioning](sql-data-warehouse-get-started-provision.md).

### Si utilizzano strumenti/protocolli non supportati
SQL Data Warehouse supporta gli ambienti di sviluppo [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) e la connettività client [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx).

Vedere le pagine [Connetti](sql-data-warehouse-get-started-connect.md) per altre informazioni..

## Prestazioni di query
SQL Data Warehouse usa costrutti comuni di SQL Server per l'esecuzione di query tra cui le statistiche. Le [statistiche](sql-data-warehouse-develop-statistics.md) sono oggetti che includono informazioni sull'intervallo e sulla frequenza di valori in una colonna di database. Il motore di query usa queste statistiche per ottimizzare l'esecuzione e migliorare le prestazioni delle query. È possibile utilizzare la query seguente per determinare l'ultimo aggiornamento delle statistiche.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

Vedere la pagina [Statistiche](sql-data-warehouse-develop-statistics.md) per altre informazioni.

## Principali concetti relativi alle prestazioni

Per comprendere più facilmente alcuni ulteriori concetti importanti sulle prestazioni e la scalabilità, fare riferimento agli articoli seguenti:

- [Prestazioni e scalabilità][]
- [Modello di concorrenza][]
- [Progettazione di tabelle][]
- [Scegliere una chiave di distribuzione hash per la tabella][]
- [Statistiche per migliorare le prestazioni][]

## Passaggi successivi
Per alcune indicazioni sulla creazione della soluzione SQL Data Warehouse, vedere l'articolo di [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->

[Prestazioni e scalabilità]: sql-data-warehouse-performance-scale.md
[Modello di concorrenza]: sql-data-warehouse-develop-concurrency.md
[Progettazione di tabelle]: sql-data-warehouse-develop-table-design.md
[Scegliere una chiave di distribuzione hash per la tabella]: sql-data-warehouse-develop-hash-distribution-key
[Statistiche per migliorare le prestazioni]: sql-data-warehouse-develop-statistics.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_1217_2015-->