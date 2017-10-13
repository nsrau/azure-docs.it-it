---
title: Usare Node.js per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Node.js per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: d2765d4606bf6f926def15788ab23efd2a000ad4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Usare Node.js per eseguire query su un database SQL di Azure

Questa esercitazione introduttiva illustra come usare [Node.js](https://nodejs.org/en/) per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione introduttiva, accertarsi di avere:

- un database SQL di Azure. Questa guida introduttiva usa le risorse create in una delle guide introduttive seguenti: 

   - [Creare un database: portale](sql-database-get-started-portal.md)
   - [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
   - [Creare un database: PowerShell](sql-database-get-started-powershell.md)

- Una [regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) per l'indirizzo IP pubblico del computer usato per questa esercitazione introduttiva.
- Avere installato Node.js e il software correlato per il sistema operativo.
    - **MacOS**: installare Homebrew e Node.js e quindi installare il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu**: installare Node.js e quindi installare il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows**: installare Chocolatey e Node.js e quindi installare il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si sono dimenticate le informazioni di accesso per il server del database SQL di Azure, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.

> [!IMPORTANT]
> È necessario avere una regola del firewall impostata per l'indirizzo IP pubblico del computer su cui si esegue questa esercitazione. Se si usa un computer o un indirizzo IP pubblico diverso, creare una [regola del firewall a livello di server con il portale di Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="create-a-nodejs-project"></a>Creare un progetto Web Node.js

Aprire un prompt dei comandi e creare una cartella denominata *sqltest*. Passare alla cartella creata ed eseguire questo comando:

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL

1. Nell'ambiente di sviluppo o nell'editor di testo preferito creare un nuovo file, **sqltest.js**.

2. Sostituire il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, l'utente e la password.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi eseguire questi comandi:

   ```js
   node sqltest.js
   ```

2. Verificare che vengano restituite le prime 20 righe e quindi chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [driver Microsoft Node.js per SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/).
- Informazioni su come [connettersi ed eseguire query su un database SQL di Azure usando .NET Core](sql-database-connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Informazioni su come [iniziare a usare .NET Core in Windows/Linux/macOS con la riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [progettare il primo database SQL di Azure con SSMS](sql-database-design-first-database.md) o su come [progettare il primo database SQL di Azure con .NET](sql-database-design-first-database-csharp.md).
- Informazioni su come [connettersi ed eseguire query con SSMS](sql-database-connect-query-ssms.md).
- Informazioni su come [connettersi ed eseguire query con Visual Studio Code](sql-database-connect-query-vscode.md).


