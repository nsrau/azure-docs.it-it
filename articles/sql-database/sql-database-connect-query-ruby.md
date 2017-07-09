---
title: Connettersi al database SQL di Azure tramite Ruby | Documentazione Microsoft
description: Presentazione di un esempio di codice Ruby da usare per connettersi al database SQL di Azure ed eseguire query.
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: c5d09cf03c87c8da1d8588be62fea3f0cc3eec4f
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Database SQL di Azure: usare Ruby per connettersi ai dati ed eseguire query

Questa guida introduttiva illustra come usare [Ruby](https://www.ruby-lang.org) per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database da piattaforme Mac OS e Ubuntu Linux.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
- [Creare un database: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Installare le librerie di comunicazione Ruby e del database

Le procedure descritte in questa sezione presuppongono che si abbia familiarità con lo sviluppo con Ruby ma non con il database SQL di Azure. Se non si ha esperienza con lo sviluppo con Ruby, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Ruby** e quindi il sistema operativo in uso.

### <a name="mac-os"></a>**Mac OS**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script Ruby. Immettere i comandi seguenti per installare **brew**, **FreeTDS** e **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script Ruby. Immettere i comandi seguenti per installare **FreeTDS** e **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si dimenticano le informazioni di accesso per il server, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.
    

## <a name="select-data"></a>Selezionare i dati
Usare il codice seguente per eseguire una query per individuare i primi 20 prodotti per categoria tramite la funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con un'istruzione Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Questa funzione TinyTDS::Client accetta una query e restituisce un set di risultati. Il set di risultati è iterato usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>Inserire dati
Usare il codice seguente per inserire un nuovo prodotto nella tabella SalesLT.Product tramite la funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con un'istruzione Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

Questo esempio illustra come eseguire un'istruzione INSERT in modo sicuro, come passare parametri che proteggono l'applicazione dalla vulnerabilità ad attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [chiave primaria](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints) generato automaticamente.    
  
Per usare TinyTDS con Azure, si consiglia di eseguire diverse istruzioni `SET` per modificare la modalità di gestione di informazioni specifiche della sessione corrente. Le istruzioni consigliate `SET`vengono fornite nell’esempio di codice Ad esempio, `SET ANSI_NULL_DFLT_ON` consentirà a nuove colonne create di autorizzare valori nulli anche se lo stato di supporto di valori nulli della colonna non è indicato in modo esplicito.  
  
Per allinearlo con il formato [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) di Microsoft SQL Server, usare la funzione [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) per eseguire il cast nel formato datetime corrispondente.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per aggiornare il nuovo prodotto aggiunto in precedenza tramite la funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con un'istruzione Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per eliminare il nuovo prodotto aggiunto in precedenza tramite la funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con un'istruzione Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Repository GitHub per TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Segnalare problemi e porre domande](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Driver Ruby per SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


