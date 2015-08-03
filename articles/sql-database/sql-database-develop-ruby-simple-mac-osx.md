<properties 
	pageTitle="Connettersi al Database SQL utilizzando Ruby con TinyTDS in Mac OS X (Yosemite)" 
	description="Fornire un esempio di codice Ruby che è possibile eseguire su Mac OS X (Yosemite) per connettersi al Database di SQL Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Connettersi al Database SQL utilizzando Ruby in Mac OS X (Yosemite)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento presenta un esempio di codice Ruby in esecuzione su computer Mac che esegue Yosemite per connettersi a un database di SQL Azure.

## Installare i moduli necessari

Aprire il terminale in uso e installare quanto segue:

**1) Homebrew**: eseguire il comando seguente dal terminale. In questo modo verrà scaricato Gestione pacchetto di Homebrew nel computer.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: eseguire il comando seguente dal terminale. Questo installerà FreeTDS sul computer ed è necessario affinché TinyTDS funzioni.

    brew install FreeTDS

**3) TinyTDS**: eseguire il comando seguente dal terminale. TinyTDS verrà installato nel computer.

    sudo ARCHFLAGS="-arch x86_64" gem install tiny_tds

## Creare un database e recuperare la stringa di connessione

L'esempio Ruby si basa sul database di esempio AdventureWorks. Se non si dispone già di AdventureWorks, è possibile vedere come crearlo nell'argomento seguente:[creare il primo Database di SQL Azure](sql-database-get-started.md)

Nell’argomento viene inoltre illustrato come recuperare la stringa di connessione del database.

## Connettersi al database SQL

La funzione[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds)viene utilizzata per connettersi al Database SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Eseguire un'istruzione SELECT e recuperare il set di risultati

La funzione[TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds)viene utilizzata per recuperare un set di risultati da una query sul Database SQL. Questa funzione accetta una query e restituisce un set di risultati. Il set di risultati è iterato utilizzando[result.each si |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Inserimento di una riga, passaggio di parametri e recupero del valore di chiave primaria generato

Nell'esempio di codice:

- Passa i parametri per i valori da inserire in una riga.
- Inserisce la riga.
- Recupera il valore che è stato generato per la chiave primaria.

Nel database SQL, per generare automaticamente i valori di [chiave primaria](http://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx).

Per utilizzare TinyTDS con Azure, si consiglia di eseguire diverse`SET`istruzioni per modificare la modalità di gestione informazioni specifiche della sessione corrente. Le istruzioni consigliate `SET`vengono fornite nell’esempio di codice Ad esempio,`SET ANSI_NULL_DFLT_ON`consentirà a nuove colonne create di consentire valori nulli anche se lo stato di supporto di valori nulli della colonna non è indicato in modo esplicito.

Per allinearlo con il formato Microsoft SQL Server[datetime](http://msdn.microsoft.com/library/ms187819.aspx), utilizzare la funzione[strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime)per eseguire il cast nel formato datetime corrispondente.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end

<!---HONumber=July15_HO4-->