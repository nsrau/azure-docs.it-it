---
title: Connettersi al database SQL tramite Python | Documentazione Microsoft
description: "Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2793ddb1c903f6732a193276a2d804192b7ab53b
ms.openlocfilehash: 86524dd1a73df3b60245cb664c0e17a63df00763


---
# <a name="connect-to-sql-database-by-using-python"></a>Connettersi al database SQL tramite Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

In questo argomento viene illustrato come connettersi ed eseguire query in un database SQL di Azure usando Python. È possibile eseguire questo esempio da piattaforme Windows, Ubuntu Linux o Mac.

## <a name="step-1-create-a-sql-database"></a>Passaggio 1: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**. Dopo aver creato il database, abilitare l'accesso all'indirizzo IP attivando le regole del firewall come descritto nella [pagina introduttiva](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Passaggio 2: Configurare l'ambiente di sviluppo
### <a name="mac-os"></a>**Mac OS**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare il **brew**, il **driver ODBC di Microsoft per Mac** e **pyodbc**. pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL.

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare il **driver ODBC di Microsoft per Linux** e **pyodbc**. pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Installare il [driver ODBC13.1 di Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=53339). pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL. 

Installare pyodbc tramite pip

```
pip install pyodbc==3.1.1
```

Per istruzioni su come abilitare l'uso di pip, fare clic [qui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3-run-sample-code"></a>Passaggio 3: Eseguire il codice di esempio
Creare un file denominato **sql_sample.py** e incollare il codice seguente all'interno. È possibile eseguire questa operazione dalla riga di comando tramite:

```
python sql_sample.py
```

### <a name="connect-to-your-sql-database"></a>Connettersi al database SQL
Per connettersi al database SQL, viene usata la funzione [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html).

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
if row:
    print row
```

### <a name="execute-an-sql-select-statement"></a>Eseguire un'istruzione SQL SELECT
Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) . Questa funzione accetta essenzialmente qualsiasi query e restituisce un set di risultati su cui è possibile eseguire l'iterazione tramite [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1]) + " " + str(row[2])
    row = cursor.fetchone()
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserire una riga, passare i parametri e recuperare la chiave primaria generata
Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx). 

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print "Inserted Product ID : " +str(row[0])
    row = cursor.fetchone()
```

### <a name="transactions"></a>Transazioni
Questo esempio di codice illustra l'uso di transazioni con le operazioni seguenti:

* Avvio di una transazione
* Inserimento di una riga di dati
* Rollback della transazione per annullare l'inserimento 

Incollare il codice seguente nel file sql_sample.py.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("BEGIN TRANSACTION")
cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
cnxn.rollback()
```

## <a name="next-steps"></a>Passaggi successivi
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni sui [driver Microsoft Python per SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
* Visitare il [Centro per sviluppatori Python](/develop/python/).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Feb17_HO2-->


