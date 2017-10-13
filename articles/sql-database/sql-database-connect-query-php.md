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
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.openlocfilehash: 1cf95cf2c9413f99a4013ff961c8a18c31cc51f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Usare PHP per eseguire query su un database SQL di Azure

Questa esercitazione introduttiva illustra come usare [PHP](http://php.net/manual/en/intro-whatis.php) per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione introduttiva, accertarsi di avere:

- un database SQL di Azure. Questa guida introduttiva usa le risorse create in una delle guide introduttive seguenti: 

   - [Creare un database: portale](sql-database-get-started-portal.md)
   - [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
   - [Creare un database: PowerShell](sql-database-get-started-powershell.md)

- Una [regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) per l'indirizzo IP pubblico del computer usato per questa esercitazione introduttiva.

- Avere installato PHP e il software correlato per il sistema operativo.

    - **MacOS**: installare Homebrew e PHP, installare il driver ODBC e SQLCMD e quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2, 1.3 e 2.1](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: installare PHP e gli altri pacchetti necessari e quindi installare il driver PHP per SQL Server. Vedere i [passaggi 1.2 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: installare la versione più recente di PHP per IIS Express, la versione più recente dei driver Microsoft per SQL Server in IIS Express, Chocolatey, il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si dimenticano le informazioni di accesso per il server, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.     
    
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
