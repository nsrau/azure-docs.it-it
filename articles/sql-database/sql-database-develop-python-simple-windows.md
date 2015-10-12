<properties 
	pageTitle="Connettersi al database SQL tramite Python in Windows" 
	description="Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure da un client Windows. L'esempio usa il driver pymssql."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Connettersi al database SQL tramite Python in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice scritto in Python. L'esempio viene eseguito su un computer Windows e si connette a un database SQL di Azure tramite il driver **pymssql**.


## Requisiti


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### Installare i moduli necessari


Installare [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql).

Accertarsi di scegliere il file con estensione whl corretto.

Ad esempio, se si usa Python 2.7 su un computer a 64 bit, scegliere pymssql‑2.1.1‑cp27‑none‑win\_amd64.whl. Una volta scaricato il file con estensione whl, posizionarlo nella cartella C:/Python27.

A questo punto, installare il driver pymssql tramite pip dalla riga di comando. Eseguire cd in C:/Python27 e quindi il comando seguente:
	
	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Per istruzioni su come abilitare l'uso di pip, fare clic [qui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).


## Creare un database e recuperare la stringa di connessione


Vedere l'[argomento introduttivo](sql-database-get-started.md) per informazioni su come creare un database di esempio e recuperare la stringa di connessione. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Connettersi al database SQL


Per connettersi al database SQL viene usata la funzione [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html).

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Eseguire un'istruzione SQL SELECT

Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute). Questa funzione accetta essenzialmente qualsiasi query e restituisce un set di risultati su cui è possibile eseguire l'iterazione mediante [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Inserire una riga, passare i parametri e recuperare la chiave primaria generata

Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Transazioni


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

<!---HONumber=Oct15_HO1-->