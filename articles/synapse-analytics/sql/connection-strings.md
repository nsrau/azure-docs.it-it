---
title: Stringhe di connessione per Synapse SQL (anteprima)
description: Stringhe di connessione per Synapse SQL (anteprima)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: e34563a312c5a248e97c4aac72d06030a836727b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684839"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Stringhe di connessione per Synapse SQL (anteprima)

È possibile connettersi a Synapse SQL (anteprima) con diversi protocolli applicativi, ad esempio [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) e [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Di seguito sono riportati esempi di stringhe di connessione per ogni protocollo. 

Per impostare la stringa di connessione, è anche possibile usare il portale di Azure.  Per compilare la stringa di connessione tramite il portale di Azure, passare al pannello del database e in *Informazioni di base* selezionare *Mostra stringhe di connessione del database*.

## <a name="sample-adonet-connection-string"></a>Stringa di connessione ADO.NET di esempio

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Stringa di connessione ODBC di esempio

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Stringa di connessione PHP di esempio

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Stringa di connessione JDBC di esempio

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Per preservare la connessione in caso di brevi periodi di indisponibilità, è consigliabile impostare il timeout di connessione su 300 secondi.

## <a name="recommendations"></a>Consigli

Per eseguire le query del **pool SQL serverless**, gli strumenti consigliati sono [Azure Data Studio](get-started-azure-data-studio.md) e Azure Synapse Studio.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a eseguire query sull'analisi con Visual Studio e altre applicazioni, vedere [Eseguire query con Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
