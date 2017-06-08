---
title: Connettersi al database SQL di Azure tramite Node.js | Documentazione Microsoft
description: Presentazione di un esempio di codice Node.js da usare per connettersi al database SQL di Azure ed eseguire query.
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Database SQL di Azure: usare Node.js per connettersi ai dati ed eseguire query

Questa guida introduttiva illustra come connettersi a un database SQL di Azure tramite [Node.js](https://nodejs.org/en/) e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database da piattaforme Windows, Ubuntu Linux e Mac OS.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
- [Creare un database: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>Installare Node.js 

Le procedure descritte in questa sezione presuppongono che si abbia familiarità con lo sviluppo con Node.js ma non con il database SQL di Azure. Se non si ha esperienza con lo sviluppo con Node.js, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Node.js** e quindi il sistema operativo in uso.

### <a name="mac-os"></a>**Mac OS**
Immettere i comandi seguenti per installare **brew**, una gestione pacchetti facile da usare per Mac OS X e **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Immettere i comandi seguenti per installare la gestione pacchetti **Node.js** e **npm** per Node.js.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Visitare la [pagina di download di Node.js](https://nodejs.org/en/download/) e selezionare l'opzione di installazione di Windows desiderata.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Installare il driver del server Tedious SQL per il Node.js
Il driver consigliato per il Node.js è **[tedious](https://github.com/tediousjs/tedious)**. Tedious è un'iniziativa open source a cui Microsoft contribuisce per le applicazioni Node.js su qualsiasi piattaforma. Per questa esercitazione è necessaria una directory vuota per contenere il codice e le dipendenze `npm` che verranno installate.

Per installare il driver **tedious** eseguire il comando seguente all'interno della directory:

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si sono dimenticate le informazioni di accesso per il server del database SQL di Azure, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.
    
## <a name="select-data"></a>Selezionare i dati

Usare il codice seguente per eseguire query sul database SQL di Azure per individuare i primi 20 prodotti per categoria. In primo luogo, importare le classi di connessione e di richiesta del driver dalla libreria del driver tedious. Successivamente, creare l'oggetto di configurazione e sostituire le variabili **nome utente**, **password**, **server** e **database** con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. Creare un oggetto `Connection` tramite l'oggetto `config` specificato. Successivamente, definire la callback per l'evento `connect` dell'oggetto `connection` per eseguire la funzione `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
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

## <a name="insert-data-into-the-database"></a>Inserire i dati nel database
Usare il codice seguente per inserire un nuovo prodotto nella tabella SalesLT.Product tramite la funzione `insertIntoDatabase()` e l'istruzione Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Sostituire le variabili **nome utente**, **password**, **server** e **database** con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Aggiornare i dati nel database
Usare il codice seguente per eliminare il nuovo prodotto aggiunto in precedenza tramite la funzione `updateInDatabase()` e l'istruzione Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Sostituire le variabili **nome utente**, **password**, **server** e **database** con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. Questo esempio usa il nome del Prodotto inserito nell'esempio precedente.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Eliminare i dati dal database
Usare il codice seguente per eliminare i dati da un database. Sostituire le variabili **nome utente**, **password**, **server** e **database** con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. Questa volta, usare un'**istruzione DELETE** nella funzione `deleteFromDatabase()`. Questo esempio usa anche il nome del Prodotto inserito nell'esempio precedente.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Driver Microsoft Node.js per SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Connettersi ed eseguire query con Visual Studio Code](sql-database-connect-query-vscode.md)



