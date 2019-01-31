---
title: Usare PHP per eseguire query sul database SQL di Azure | Microsoft Docs
description: Come usare PHP per creare un programma che si connette a un database SQL di Azure ed eseguire query sul database con istruzioni T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b3fe6e0249143b27cb763401a8d328922ed1fe99
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173922"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare PHP per eseguire query su un database SQL di Azure

Questo articolo illustra come usare [PHP](http://php.net/manual/en/intro-whatis.php) per connettersi a un database SQL di Azure. È quindi possibile usare istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo esempio, accertarsi di avere i prerequisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Software correlato a PHP installato per il sistema operativo in uso:

    - **MacOS**: installare PHP, il driver ODBC e quindi il driver PHP per SQL Server. Vedere i [passaggi 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Linux**: installare PHP, il driver ODBC e quindi il driver PHP per SQL Server. Vedere i [passaggi 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Windows**: installare PHP per IIS Express e Chocolatey, quindi installare il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Ottenere la connessione di database

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Aggiungere il codice per eseguire query sul database

1. Nell'editor di testo preferito creare un nuovo file, *sqltest.php*.  

1. Sostituire il contenuto con il codice seguente. Aggiungere quindi i valori appropriati per il server, il database, l'utente e la password.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
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

1. Al prompt dei comandi eseguire l'app.

   ```bash
   php sqltest.php
   ```

1. Verificare che vengano restituite le prime 20 righe e chiudere la finestra dell'app.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)

- [Driver Microsoft PHP per SQL Server](https://github.com/Microsoft/msphpsql/)

- [Segnalare problemi o porre domande](https://github.com/Microsoft/msphpsql/issues)

- [Esempio di logica di ripetizione dei tentativi: connettersi in modo resiliente a SQL con PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
