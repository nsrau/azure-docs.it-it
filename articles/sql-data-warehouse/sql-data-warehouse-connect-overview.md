<properties
   pageTitle="Connettersi ad Azure SQL Data Warehouse |Microsoft Azure"
   description="Come trovare il nome del server e la stringa di connessione per Azure SQL Data Warehouse"
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
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>  

# Connettersi ad Azure SQL Data Warehouse

Questo articolo consente di connettersi a SQL Data Warehouse per la prima volta.

## Trovare il nome del server

Il primo passaggio per connettersi a SQL Data Warehouse è sapere come trovare il nome del server. Nell'esempio seguente, il nome del server è sample.database.windows.net. Per trovare il nome completo del server, procedere come segue:

1. Accedere al [portale di Azure][].
2. Fare clic su **Database SQL**.
3. Fare clic sul database a cui si vuole connettersi.
4. Individuare il nome completo del server.

    ![Nome completo del server][1]  

## Driver supportati e stringhe di connessione

Azure SQL Data Warehouse supporta [ADO.NET][], [ODBC][], [PHP][] e [JDBC][]. Fare clic su uno dei driver precedenti per trovare la versione più recente e la documentazione. Per generare automaticamente la stringa di connessione per il driver in uso dal portale di Azure, è possibile fare clic su **Mostra stringhe di connessione del database** nell'esempio precedente. Di seguito sono riportati alcuni esempi di come si presenta la stringa di connessione per ogni driver.

> [AZURE.NOTE] Per preservare la connessione in caso di brevi periodi di indisponibilità, può essere opportuno impostare il timeout di connessione su 300 secondi.

### Esempio di stringa di connessione ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Esempio di stringa di connessione ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Esempio di stringa di connessione PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Esempio di stringa di connessione JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Impostazioni di connessione

SQL Data Warehouse standardizza alcune impostazioni durante la connessione e la creazione di oggetti. Queste impostazioni, di cui non è possibile eseguire l'override, includono:

| Impostazione del database | Valore |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ATTIVA |
| [QUOTED\_IDENTIFIERS][] | ATTIVA |
| [DATEFORMAT][] | mdy |
| [DATEFORMAT][] | 7 |

## Passaggi successivi

Per connettersi ed eseguire query con Visual Studio, vedere [Eseguire query con Visual Studio][]. Per altre informazioni sulle opzioni di autenticazione, vedere [Autenticazione in Azure SQL Data Warehouse][].

<!--Articles-->  
[Eseguire query con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticazione in Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[portale di Azure]: https://portal.azure.com

<!--Image references-->  
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->