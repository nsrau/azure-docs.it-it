<properties
   pageTitle="Connettersi ad Azure SQL Data Warehouse |Microsoft Azure"
   description="Panoramica sulla connessione ad Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Connettersi ad Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-connect-overview.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Driver](sql-data-warehouse-connection-strings.md)

Panoramica sulla connessione ad Azure SQL Data Warehouse.

## Trovare la stringa di connessione nel portale

SQL Data Warehouse è associato a un server di Azure SQL. Per connettersi è necessario il nome completo del server, ad esempio **nomeserver**.database.windows.net*.

Per trovare il nome completo del server, procedere come segue:

1. Accedere al [portale di Azure][].
2. Fare clic su **Database SQL** e sul database a cui connettersi. Questo esempio usa il database di esempio AdventureWorksDW.
3. Individuare il nome completo del server.

    ![Nome completo del server][1]

## Impostazioni di connessione
SQL Data Warehouse standardizza alcune impostazioni durante la connessione e la creazione di oggetti. Queste impostazioni non possono essere sottoposte a override.

| Impostazione del database | Valore |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ATTIVA |
| QUOTED\_IDENTIFIERS | ATTIVA |
| NO\_COUNT | DISATTIVA |
| DATEFORMAT | mdy |
| DATEFORMAT | 7 |
| Regole di confronto database | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sessioni e richieste
Dopo che è stata stabilita una connessione e una sessione è possibile scrivere e inviare query a SQL Data Warehouse.

Ogni query viene rappresentata da uno o più identificatori di richiesta. Tutte le query inviate su tale connessione fanno parte di una singola sessione e pertanto saranno rappresentate da un ID sessione.

Tuttavia, poiché SQL Data Warehouse è un sistema MPP (Massively Parallel Processing) distribuito, gli identificatori di sessione e di richiesta vengono presentati in modo leggermente diverso rispetto a SQL Server.

Le sessioni e le richieste sono rappresentate logicamente dai rispettivi identificatori.

| Identificatore | Valore di esempio |
| :--------- | :------------ |
| ID sessione | SID123456 |
| ID richiesta | QID123456 |

Si noti che l'ID sessione è preceduto dal prefisso SID (abbreviazione di ID sessione) e le richieste sono precedute dal prefisso QID (abbreviazione di ID query).

Queste informazioni saranno necessarie per identificare la query quando si monitorano le prestazioni di query. È possibile monitorare le prestazioni di query usando il [portale di Azure] e le viste a gestione dinamica.

Questa query identifica la sessione corrente.

```sql
SELECT SESSION_ID()
;
```

Per visualizzare tutte le query in esecuzione o eseguite di recente sul data warehouse è possibile usare l'esempio riportato di seguito. In questo modo viene creata e poi eseguita una vista.

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;

SELECT * FROM dbo.vSessionRequests;
```

## Passaggi successivi

Per iniziare a eseguire query sul data warehouse con Visual Studio e altre applicazioni, vedere [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][].


<!--Arcticles-->

[Eseguire query in Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[portale di Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->