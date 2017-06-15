---
title: Connettersi al database SQL di Azure tramite Python | Documentazione Microsoft
description: Presenta un esempio di codice Python da usare per connettersi al database SQL di Azure ed eseguire query.
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5fae11119500fd3be3af3e573d45f6cc5880e037
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Database SQL di Azure: usare Python per connettersi ai dati ed eseguire query

 Questa guida introduttiva illustra come usare [Python](https://python.org) per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database da piattaforme Mac OS, Ubuntu Linux e Windows.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
- [Creare un database: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Installare le librerie di comunicazione Python e del database

Le procedure descritte in questa sezione presuppongono che si abbia familiarità con lo sviluppo con Python ma non con il database SQL di Azure. Se non si ha esperienza con lo sviluppo con Python, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Python** e quindi il sistema operativo in uso.

### <a name="mac-os"></a>**Mac OS**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare il **brew**, il **driver ODBC di Microsoft per Mac** e **pyodbc**. pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare il **driver ODBC di Microsoft per Linux** e **pyodbc**. pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Installare il [driver ODBC 13.1 di Microsoft](https://www.microsoft.com/download/details.aspx?id=53339) (se richiesto, aggiornare il driver). Pyodbc usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL. 

Installare **pyodbc** tramite pip.

```cmd
pip install pyodbc==3.1.1
```

Per istruzioni su come abilitare l'uso di pip, fare clic [qui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si dimenticano le informazioni di accesso per il server, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.     
   
## <a name="select-data"></a>Selezionare i dati

Usare il codice seguente per eseguire una query per individuare i primi 20 prodotti per categoria tramite la funzione [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) con un'istruzione Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Per recuperare un set di risultati di una query sul database SQL viene usata la funzione [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html). Questa funzione accetta una query e restituisce un set di risultati su cui è possibile eseguire l'iterazione tramite [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>Inserire dati
Usare il codice seguente per inserire un nuovo prodotto nella tabella SalesLT.Product tramite la funzione [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) e l'istruzione Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per aggiornare il nuovo prodotto aggiunto in precedenza tramite la funzione [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) e l'istruzione Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per eliminare il nuovo prodotto aggiunto in precedenza tramite la funzione [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) e l'istruzione Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Driver Microsoft Python per SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro per sviluppatori Python](/develop/python/)


