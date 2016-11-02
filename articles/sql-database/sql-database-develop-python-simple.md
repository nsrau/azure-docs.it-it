<properties
    pageTitle="Connettersi al database SQL tramite Python | Microsoft Azure"
    description="Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>



# <a name="connect-to-sql-database-by-using-python"></a>Connettersi al database SQL tramite Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


In questo argomento viene illustrato come connettersi ed eseguire query in un database SQL di Azure usando Python. È possibile eseguire questo esempio da piattaforme Windows, Ubuntu Linux o Mac.


## <a name="step-1:-create-a-sql-database"></a>Passaggio 1: Creare un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**. Dopo aver creato il database, abilitare l'accesso all'indirizzo IP attivando le regole del firewall come descritto nella [pagina introduttiva](sql-database-get-started.md).

## <a name="step-2:-configure-development-environment"></a>Passaggio 2: Configurare l'ambiente di sviluppo

### <a name="**mac-os**"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Installare i moduli necessari
Aprire il terminale in uso e installare

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="**linux-(ubuntu)**"></a>**Linux (Ubuntu)**

Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare **FreeTDS** e **pymssql**. pymssql usa FreeTDS per connettersi ai database SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="**windows**"></a>**Windows**

Installare pymssql da [**qui**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Accertarsi di scegliere il file con estensione whl corretto. Ad esempio, se si usa Python 2.7 su un computer a 64 bit, scegliere: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Una volta scaricato il file con estensione whl, posizionarlo nella cartella C:/Python27.

Installare il driver pymssql tramite pip dalla riga di comando. cd in C:/Python27 ed eseguire il comando seguente
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Per istruzioni su come abilitare l'uso di pip, fare clic [qui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3:-run-sample-code"></a>Passaggio 3: Eseguire il codice di esempio

Creare un file denominato **sql_sample.py** e incollare il codice seguente all'interno. È possibile eseguire questa operazione dalla riga di comando tramite:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Connettersi al database SQL

Per connettersi al database SQL viene usata la funzione [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) .

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Eseguire un'istruzione SQL SELECT

Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) . Questa funzione accetta essenzialmente qualsiasi query e restituisce un set di risultati su cui è possibile eseguire l'iterazione tramite [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row,-pass-parameters,-and-retrieve-the-generated-primary-key"></a>Inserire una riga, passare i parametri e recuperare la chiave primaria generata

Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx). 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transazioni


Questo esempio di codice illustra l'uso di transazioni con le operazioni seguenti:

* Avvio di una transazione
* Inserimento di una riga di dati
* Rollback della transazione per annullare l'inserimento 

Incollare il codice seguente nel file sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Passaggi successivi

* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni sui [driver Microsoft Python per SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visitare il [Centro per sviluppatori Python](/develop/python/).

## <a name="additional-resources"></a>Risorse aggiuntive 

* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Oct16_HO2-->


