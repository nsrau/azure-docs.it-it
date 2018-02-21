---
title: Usare PHP per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare PHP per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: carlrab
ms.openlocfilehash: b45acf8a7abdee070c6db2c5d7f4c108a073b1bb
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Usare PHP per eseguire query su un database SQL di Azure

Questa esercitazione introduttiva illustra come usare [PHP](http://php.net/manual/en/intro-whatis.php) per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione introduttiva, accertarsi di soddisfare i requisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Avere una [regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) per l'indirizzo IP pubblico del computer usato per questa esercitazione introduttiva.

- Avere installato PHP e il software correlato adatti per il sistema operativo in uso:

    - **MacOS**: installare Homebrew e PHP, installare il driver ODBC e SQLCMD e quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2, 1.3 e 2.1](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: installare PHP e gli altri pacchetti necessari e quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: installare la versione più recente di PHP per IIS Express, la versione più recente dei driver Microsoft per SQL Server in IIS Express, Chocolatey, il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL

1. Nell'editor di testo preferito creare un nuovo file, **sqltest.php**.  

2. Sostituire il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, l'utente e la password.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi eseguire questi comandi:

   ```php
   php sqltest.php
   ```

2. Verificare che vengano restituite le prime 20 righe e quindi chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Driver Microsoft PHP per SQL Server](https://github.com/Microsoft/msphpsql/)
- [Segnalare problemi o porre domande](https://github.com/Microsoft/msphpsql/issues)
- [Esempio di logica di ripetizione dei tentativi: connettersi in modo resiliente a SQL tramite PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

