<properties
	pageTitle="Connettersi al database SQL tramite Node.js in Windows"
	description="Presentazione di un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure. L'esempio viene eseguito su un computer client Windows."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Connettersi al database SQL tramite Node.js in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure. Il programma Node.js viene eseguito su un computer client Windows. Per gestire la connessione viene usato il driver msnodesql.


## Prerequisiti


Nel computer di sviluppo client devono essere presenti gli elementi software seguenti.


-  [Node.js](https://nodejs.org/en/download/) - fare clic sul programma di installazione di windows e scaricare il programma di installazione msi appropriato. Una volta scaricato il file msi eseguirlo per installare Node.js


### Installare i moduli necessari

Una volta configurato il computer con **node**, aprire cmd.exe e passare a una directory in cui si intende creare il progetto Node.js e immettere i comandi seguenti.


	npm init
	npm install tedious


**npm init** crea un progetto node. Per mantenere i valori predefiniti durante la creazione del progetto, premere INVIO finché non viene creato il progetto. A questo punto è possibile visualizzare un file **package.json** nella directory del progetto.


### Un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Passaggio 1: Ottenere i dettagli di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Passaggio 2: Connettersi

Per connettersi al database SQL viene usata la funzione [new Connection](http://pekim.github.io/tedious/api-connection.html).

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Passaggio 3: Eseguire una query


Tutte le istruzioni SQL vengono eseguite mediante la funzione [new Request()](http://pekim.github.io/tedious/api-request.html). Se l'istruzione restituisce righe, ad esempio un'istruzione select, è possibile recuperarle usando la funzione [request.on()](http://pekim.github.io/tedious/api-request.html). Se non sono presenti righe, la funzione [request.on()](http://pekim.github.io/tedious/api-request.html) restituisce elenchi vuoti.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});

	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;

	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);}
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});

		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Passaggio 4: Inserire una riga

Questo esempio illustra come eseguire un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) in modo sicuro, come passare i parametri che proteggono l'applicazione da attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [Chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) generato automaticamente.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});

	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;

	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);}
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

<!---HONumber=AcomDC_0316_2016-->