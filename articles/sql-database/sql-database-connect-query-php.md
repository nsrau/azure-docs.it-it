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
ms.openlocfilehash: be3ac9fab6c89c65ad9673811e108cefe2c80d00
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724248"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare PHP per eseguire query su un database SQL di Azure

Questo articolo illustra come usare [PHP](http://php.net/manual/en/intro-whatis.php) per connettersi a un database SQL di Azure. È quindi possibile usare istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo esempio, accertarsi di avere i prerequisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regola del firewall a livello di server](sql-database-get-started-portal-firewall.md) per l'indirizzo IP pubblico del computer usato

- Software correlato a PHP installato per il sistema operativo in uso:

    - **MacOS**: installare Homebrew e PHP, il driver ODBC e SQLCMD, quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2, 1.3 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).

    - **Ubuntu**: installare PHP e gli altri pacchetti necessari, quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).

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

- [Esempio di logica di ripetizione dei tentativi: connettersi in modo resiliente a SQL tramite PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
