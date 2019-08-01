---
title: Usare PHP per eseguire query sul database SQL di Azure | Microsoft Docs
description: Come usare PHP per creare un programma che si connette a un database SQL di Azure ed eseguire query sul database con istruzioni T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: a7da65438ffd7db6c43cf2ede7cc52378af056f1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569221"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare PHP per eseguire query su un database SQL di Azure

Questo articolo illustra come usare [PHP](https://php.net/manual/en/intro-whatis.php) per connettersi a un database SQL di Azure. È quindi possibile usare istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo esempio, accertarsi di avere i prerequisiti seguenti:

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurare | [Regola del firewall IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |Caricare i dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Ripristinare o importare Adventure Works dal file [BACPAC](sql-database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.

- Software correlato a PHP installato per il sistema operativo in uso:

  - **MacOS**: installare PHP, il driver ODBC e quindi il driver PHP per SQL Server. Vedere i [passaggi 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**: installare PHP, il driver ODBC e quindi il driver PHP per SQL Server. Vedere i [passaggi 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**: installare PHP per IIS Express e Chocolatey, quindi installare il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

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
