---
title: Usare Node.js per eseguire query su un database
description: Come usare Node.js per creare un programma che si connette a un database SQL di Azure ed eseguire query sul database con istruzioni T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768611"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare Node.js per eseguire query su un database SQL di Azure

Questo argomento di avvio rapido illustra come usare Node.js per connettersi a un database SQL di Azure e usare le istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un [database SQL di Azure](sql-database-single-database-get-started.md)
- Software correlato a [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Installare Homebrew, Node.js, quindi il driver ODBC e SQLCMD seguendo i passaggi **1.2** e **1.3** della procedura [Creare app Node.js con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installare Node.js, quindi il driver ODBC e SQLCMD seguendo i passaggi **1.2** e **1.3** della procedura [Creare app Node.js con SQL Server in Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installare Chocolatey, Node.js, quindi il driver ODBC e SQLCMD seguendo i passaggi **1.2** e **1.3** della procedura [Creare app Node.js con SQL Server in Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Gli script di questo articolo sono scritti in modo da usare il database **Adventure Works**.

> [!NOTE]
> È anche possibile scegliere di usare un'istanza gestita di Azure SQL.
>
> Per creare e configurare il progetto, usare il [portale di Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) o l'[interfaccia della riga di comando](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), quindi configurare la connettività [sul posto](sql-database-managed-instance-configure-p2s.md) o tramite [VM](sql-database-managed-instance-configure-vm.md).
>
> Per caricare i dati, vedere come eseguire il [ripristino con BACPAC](sql-database-import.md) con il file di [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) oppure come eseguire il [ripristino del database Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**. 

## <a name="create-the-project"></a>Creare il progetto

Aprire un prompt dei comandi e creare una cartella denominata *sqltest*. Aprire la cartella creata ed eseguire il comando seguente:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Aggiungere il codice per eseguire query sul database

1. Nell'editor di testo preferito creare un nuovo file, *sqltest.js*.

1. Sostituire il contenuto con il codice seguente. Aggiungere quindi i valori appropriati per il server, il database, l'utente e la password.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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
