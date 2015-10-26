<properties 
	pageTitle="Connettersi al database SQL tramite Node.js con Tedious in Ubuntu Linux" 
	description="Presentazione di un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure. L'esempio stabilisce la connessione tramite il driver Tedious."
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
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Connettersi al database SQL tramite Node.js con Tedious in Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Node.js che viene eseguito in Ubuntu Linux. L'esempio si connette a un database SQL di Azure tramite il driver Tedious.


## Elementi software necessari


Aprire il terminale in uso e installare **node** e **npm**, a meno che non siano già installati nel computer.


	sudo apt-get install node
	sudo apt-get install npm


Una volta configurato il computer con **node** e **npm**, passare a una directory in cui si intende creare il progetto Node.js e immettere i comandi seguenti.


	sudo npm init
	sudo npm install tedious


**npm init** crea un progetto node. Per mantenere i valori predefiniti durante la creazione del progetto, premere INVIO finché non viene creato il progetto. A questo punto è possibile visualizzare un file **package.json** nella directory del progetto.


### Creare un database AdventureWorks


L'esempio di codice in questo argomento prevede l'uso di un database di test **AdventureWorks**. Se non si ha già questo database, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Connettersi al database SQL

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


## Eseguire un'istruzione SQL SELECT


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


## Inserire una riga, applicare i parametri e recuperare la chiave primaria generata


Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx). Questo esempio illustra come eseguire un'istruzione insert, come passare i parametri in modo sicuro per evitare attacchi SQL injection e come recuperare il valore di chiave primaria generato automaticamente.


L'esempio di codice in questa sezione applica parametri a un'istruzione SQL INSERT. Il valore di chiave primaria generato viene recuperato dal programma.


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

 

<!---HONumber=Oct15_HO3-->