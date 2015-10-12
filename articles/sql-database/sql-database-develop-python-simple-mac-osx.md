<properties 
	pageTitle="Connettersi al database SQL tramite Python su Mac OS" 
	description="Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure da un Mac. L'esempio usa il driver pymssql."
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
	ms.date="07/16/2015" 
	ms.author="mebha"/>


# Connettersi al database SQL tramite Python su Mac OS


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice scritto in Python. L'esempio viene eseguito su un computer Mac e si connette a un database SQL di Azure tramite il driver **pymssql**. Inoltre, utilizzare il video [Guida introduttiva a Python su Mac](https://www.youtube.com/watch?v=OMpugPTwnTI) per integrare questa documentazione.


## Requisiti


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [Pymssql](https://github.com/pymssql/pymssql)

### Installare i moduli necessari


Aprire il terminale in uso e installare

**1) Homebrew**: eseguire il comando seguente dal terminale. In questo modo verrà scaricato Gestione pacchetto di Homebrew nel computer.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: eseguire il comando seguente dal terminale. In questo modo verrà scaricato FreeTDS nel computer. FreeTDS è necessario per il funzionamento corretto di pymmsql.

    brew install FreeTDS
  
**3) Pymmsql**: eseguire il comando seguente dal terminale. In questo modo pymmsql verrà installato nel computer

    sudo -H pip install pymssql

### Creare un database e recuperare la stringa di connessione


Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio e ottenere la stringa di connessione. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


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

Nel database SQL, per generare automaticamente i [valori delle chiavi primarie](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx).


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

 

<!---HONumber=Oct15_HO1-->