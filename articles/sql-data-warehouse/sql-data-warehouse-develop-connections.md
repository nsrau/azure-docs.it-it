<properties
   pageTitle="Connettersi a SQL Data Warehouse |Microsoft Azure"
   description="Suggerimenti per la connessione a SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Connettersi a SQL Data Warehouse 
Per connettersi a SQL Data Warehouse sarà necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione si potrà notare che alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.

In questo articolo vengono illustrati i seguenti aspetti della connessione a SQL Data Warehouse:

- Autenticazione
- Impostazioni di connessione
- Sessioni e identificatori di richiesta


## Autenticazione
Per connettersi a SQL Data Warehouse è necessario fornire le informazioni seguenti:

- Nome del server completo 
- Specificare l'autenticazione di SQL
- Nome utente 
- Password
- Database predefinito (facoltativo)

È importante notare che gli utenti devono essere autenticati utilizzando l'autenticazione di SQL. Attualmente l'autenticazione attendibile non è supportata.

Per impostazione predefinita la connessione si connetterà al database master e non al database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

1. Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT o nella stringa di connessione dell’applicazione. Ad esempio, includendo il parametro InitialCatalog per una connessione ODBC.
2. Selezionare innanzitutto il database utente prima di creare una sessione in SSDT.

> [AZURE.NOTE]Per informazioni sulla connessione a SQL Data Warehouse con SSDT, consultare nuovamente l’articolo dell’introduzione [Connettersi ed eseguire query][].

Anche in questo caso è importante notare che l'istruzione Transact-SQL **USE <your DB>** non è supportata per la modifica del database per una connessione

## Protocolli di connessione dell’applicazione
È possibile connettersi a SQL Data Warehouse utilizzando i protocolli seguenti:

- ADO.NET
- ODBC
- PHP
- JDBC

### Stringa di connessione ADO.NET di esempio

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Stringa di connessione ODBC di esempio

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Stringa di connessione PHP di esempio

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Stringa di connessione JDBC di esempio

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

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

Tuttavia, poiché SQL Data Warehouse è un sistema MPP distribuito, gli identificatori di sessione e di richiesta vengono esposti in modo leggermente diverso rispetto a SQL Server.

Le sessioni e le richieste sono rappresentate logicamente dai rispettivi identificatori.
	
| Identificatore | Valore di esempio |
| :--------- | :------------ |
| ID sessione | SID123456 |
| ID richiesta | QID123456 |

Si noti che l'ID sessione è preceduto dal prefisso SID (abbreviazione di ID sessione) e le richieste sono precedute dal prefisso QID (abbreviazione di ID query).

Queste informazioni saranno necessarie per identificare la query quando si monitorano le prestazioni di query. È possibile monitorare le prestazioni di query utilizzando il [portale di Azure] e le viste a gestione dinamica.

Per identificare in quale sessione attualmente si utilizza la funzione seguente:

```
SELECT SESSION_ID()
;
```

Per visualizzare tutte le query in esecuzione o eseguite di recente sul data warehouse è possibile utilizzare una query analoga a quella riportata di seguito:

```
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
```

Tenere presente che questa query è stata incapsulata in una vista in modo da poterla incorporare nella soluzione. Tuttavia, per visualizzare i risultati, è necessario creare la vista ed eseguirla.

## Passaggi successivi
Una volta connessi, è possibile iniziare a progettare le tabelle. Per ulteriori dettagli, consultare l’articolo relativo alla [progettazione della tabella].

<!--Image references-->

<!--Azure.com references-->
[Connettersi ed eseguire query]: sql-data-warehouse-get-started-connect-query.md
[progettazione della tabella]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=Oct15_HO1-->