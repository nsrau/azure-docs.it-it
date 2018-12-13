---
title: Usare Node.js per eseguire query sul database SQL di Azure | Microsoft Docs
description: Come usare Node.js per creare un programma che si connette a un database SQL di Azure ed eseguire query sul database con istruzioni T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 22c783574897076b697b4b2bb3e8fc1da284dcae
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724673"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare Node.js per eseguire query su un database SQL di Azure

Questo articolo illustra come usare [Node.js](https://nodejs.org) per connettersi a un database SQL di Azure. È quindi possibile usare istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo esempio, accertarsi di avere i prerequisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regola del firewall a livello di server](sql-database-get-started-portal-firewall.md) per l'indirizzo IP pubblico del computer usato

- Software correlato a Node.js per il sistema operativo in uso

  - **MacOS**: installare Homebrew e Node.js e quindi il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**: installare Node.js e quindi il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**: installare Chocolatey e Node.js e quindi il driver ODBC e SQLCMD. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Ottenere la connessione di database

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> È necessario avere una regola del firewall impostata per l'indirizzo IP pubblico del computer su cui si esegue questa esercitazione. Se si usa un computer o un indirizzo IP pubblico diverso, creare una [regola del firewall a livello di server con il portale di Azure](sql-database-get-started-portal-firewall.md).

## <a name="create-the-project"></a>Creare il progetto

Aprire un prompt dei comandi e creare una cartella denominata *sqltest*. Passare alla cartella creata ed eseguire questo comando:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Aggiungere il codice per eseguire query sul database

1. Nell'editor di testo preferito creare un nuovo file, *sqltest.js*.

1. Sostituire il contenuto con il codice seguente. Aggiungere quindi i valori appropriati per il server, il database, l'utente e la password.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
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
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
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

> [!NOTE]
> L'esempio di codice usa il database di esempio **AdventureWorksLT** per Azure SQL.

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi eseguire il programma.

    ```bash
    node sqltest.js
    ```

1. Verificare che vengano restituite le prime 20 righe e chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Driver Microsoft Node.js per SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Connettersi ed eseguire query in Windows/Linux/macOS con [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) o [SSMS](sql-database-connect-query-ssms.md) (solo Windows)

- [Introduzione all'uso di .NET Core su Windows/Linux/macOS dalla riga di comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Progettare il primo database SQL di Azure con [.NET](sql-database-design-first-database-csharp.md) o [SSMS](sql-database-design-first-database.md)
