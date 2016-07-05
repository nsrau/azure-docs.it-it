<properties
   pageTitle="Driver per SQL Data Warehouse | Microsoft Azure"
   description="Stringhe di connessione e driver per SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>


# Driver per Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-connect-overview.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Driver](sql-data-warehouse-connection-strings.md)


È possibile connettersi a SQL Data Warehouse tramite i protocolli di applicazione seguenti:

- ADO.NET
- ODBC
- PHP
- JDBC 

Di seguito sono riportati esempi di stringhe di connessione per ogni protocollo. È anche possibile usare il portale di Azure per impostare la stringa di connessione. È sufficiente passare al database nel portale di Azure. In *Informazioni di base*, fare clic su *Mostra stringhe di connessione del database*.

## Stringa di connessione ADO.NET di esempio

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## Stringa di connessione ODBC di esempio

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## Stringa di connessione PHP di esempio

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## Stringa di connessione JDBC di esempio

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Passaggi successivi

Per iniziare a eseguire query sul data warehouse con Visual Studio e altre applicazioni, vedere [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][].

<!--Image references-->

<!--Azure.com references-->


<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0622_2016-->