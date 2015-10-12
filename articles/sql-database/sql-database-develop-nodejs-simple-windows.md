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
	ms.date="07/30/2015" 
	ms.author="mebha"/>


# Connettersi al database SQL tramite Node.js in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure. Il programma Node.js viene eseguito su un computer client Windows. Per gestire la connessione viene usato il driver msnodesql.


## Requisiti


Nel computer di sviluppo client devono essere presenti gli elementi software seguenti.


-  Node.js: [versione 0.8.9 (a 32 bit)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Scorrere e fare clic sul download di Windows Installer per x86 a 32 bit, e non di Windows Installer per x64 a 64 bit.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/): il programma di installazione per x86 o x64. 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2): l'Express Edition viene fornita gratuitamente da Microsoft.
- SQL Server Native Client 11.0: disponibile come Microsoft SQL Server 2012 Native Client disponibile nel [Feature Pack di SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29065).


### Installare i moduli necessari

Una volta soddisfatti i requisiti, assicurarsi che si è nella versione 0.8.9. di Node.js. Per verificarlo, utilizzare il comando seguente dal terminale della riga di comando: node -v. <br>In una finestra della riga di comando **cmd.exe**, passare alla directory del progetto- ad esempio C:\\NodeJSSQLProject. Immettere i comandi seguenti nella sequenza indicata.

	npm init
	npm install msnodesql

Quindi passare alla cartella node\_modules\\msnodesql ed eseguire il file eseguibile **msnodesql-0.2.1-v0.8 ia32**. Seguire i passaggi dall'installazione guidata e scegliere Fine al termine. A questo punto dovrebbe essere stato installato il driver SQL Server Node.js. Seguire i passaggi successivi per ottenere la stringa di connessione e quindi si dovrebbe essere in grado di connettersi al database SQL di Azure dall'applicazione Node.js.

### Creare un database e recuperare la stringa di connessione
 
Vedere l'[argomento introduttivo](sql-database-get-started.md) per informazioni su come creare un database di esempio e recuperare la stringa di connessione. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Connettersi al database SQL


- Copiare il codice seguente in un file JS che si trova nella directory del progetto.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- Eseguire quindi il file JS immettendo il comando seguente.


		node index.js


## Eseguire un'istruzione SQL SELECT


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Inserire una riga, passare i parametri e recuperare la chiave primaria generata


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Transazioni


Il metodo **conn.beginTransactions** non funziona nel database SQL di Azure. In alternativa, per eseguire le transazioni nel database SQL, seguire l'esempio di codice.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Stored procedure


Per il funzionamento di questo esempio di codice, è necessario innanzitutto avere o creare una stored procedure che non prevede l'input di parametri. È possibile creare una stored procedure con uno strumento quale SQL Server Management Studio (SSMS.exe).


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

 
## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](/develop/nodejs/).

<!---HONumber=Oct15_HO1-->