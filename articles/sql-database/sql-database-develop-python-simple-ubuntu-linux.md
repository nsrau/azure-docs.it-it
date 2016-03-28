<properties
	pageTitle="Connettersi al database SQL tramite Python con pymssql in Ubuntu Linux"
	description="Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure. L'esempio viene eseguito in un computer client Ubuntu Linux."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Connettersi al database SQL tramite Python in Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Python che viene eseguito in un computer client Ubuntu Linux per connettersi a un database SQL di Azure.


## Prerequisiti


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).


### Installare i moduli necessari


Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare **FreeTDS** e **pymssql**. pymssql usa FreeTDS per connettersi al database SQL.

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### Un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## Passaggio 1: Ottenere i dettagli di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Passaggio 2: Connettersi

!!!!!sql-database-include-connection-string-details-20-portalshots.md

Per connettersi al database SQL viene usata la funzione [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html).

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Passaggio 3: Eseguire una query

Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute). Questa funzione accetta essenzialmente qualsiasi query e restituisce un set di risultati su cui è possibile eseguire l'iterazione mediante [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Passaggio 4: Inserire una riga

Questo esempio illustra come eseguire un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) in modo sicuro, come passare i parametri che proteggono l'applicazione da attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [Chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) generato automaticamente.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Passaggio 5: Rollback di una transazione


Questo esempio di codice illustra l'uso di transazioni con le operazioni seguenti:


-Avvio di una transazione

-Inserimento di una riga di dati

-Rollback della transazione per annullare l'inserimento


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

<!---HONumber=AcomDC_0316_2016-->