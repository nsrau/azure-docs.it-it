<properties 
	pageTitle="Connessione al database SQL utilizzando Ruby con TinyTDS su Windows" 
	description="Fornire un esempio di codice Ruby che è possibile eseguire su Windows per connettersi al database SQL di Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Connessione al database SQL tramite Ruby in Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento presenta un esempio di codice Ruby in esecuzione su computer con Windows 8.1 per la connessione a un database SQL di Azure.

## Installare i moduli necessari

Aprire il terminale in uso e installare quanto segue:

**1) Ruby:** se non è presente nel computer, installare Ruby. Per i nuovi utenti di Ruby, è consigliabile utilizzare programmi di installazione di Ruby 2.1.X. Questi forniscono un linguaggio stabile e un elenco completo dei pacchetti (gemme) compatibili e aggiornati. [Visitare la pagina di download di Ruby](http://rubyinstaller.org/downloads/)e scaricare il programma di installazione 2.1.x appropriato. Ad esempio, se si utilizza un computer a 64 bit, scaricare il programma di installazione **Ruby 2.1.6 (x64)**. <br/><br/>Una volta scaricato il programma di installazione, eseguire le operazioni seguenti:


- Fare doppio clic sul file per avviare il programma di installazione.

- Selezionare la lingua e accettare le condizioni.

- Nella schermata relativa alle impostazioni di installazione, selezionare le caselle di controllo accanto a *Add Ruby executables to your PATH* e *Associate .rb and .rbw files with this Ruby installation*.


**2) DevKit:** scaricare il DevKit dalla [pagina di RubyInstaller](http://rubyinstaller.org/downloads/)

Una volta completato il download, eseguire le operazioni seguenti:


- Fare doppio clic sul file. Verrà chiesto dove estrarre i file.

- Fare clic sul pulsante "..." e selezionare "C:\\DevKit". Sarà probabilmente necessario creare la cartella facendo clic su "Make New Folder".

- Fare clic su "OK" e quindi "Extract", per estrarre i file.


Aprire il prompt dei comandi e immettere i comandi seguenti:

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Adesso si dispone di una versione di Ruby completamente funzionale e RubyGems!


**3) TinyTDS:** andare a C:\\DevKit ed eseguire il comando seguente dal terminale. TinyTDS verrà installato nel computer.

	gem inst tiny_tds --pre

## Creare un database e recuperare la stringa di connessione

L'esempio di Ruby si basa sul database di esempio di `AdventureWorks`. Se non dispone già di `AdventureWorks` è possibile consultare le istruzioni di creazione visitando [Creare il primo database SQL di Azure](sql-database-get-started.md).

Nell’argomento viene inoltre illustrato come recuperare la stringa di connessione del database.

## Connettersi al database SQL

La funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) viene utilizzata per connettersi al database SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Eseguire un'istruzione SELECT e recuperare il set di risultati

Copiare e incollare codice seguente in un file vuoto. Denominarlo test.rb. Quindi eseguirlo immettendo il comando seguente dal prompt dei comandi:

	ruby test.rb

Nel codice di esempio, la funzione [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) viene utilizzata per recuperare un set di risultati da una query sul database SQL. Questa funzione accetta una query e restituisce un set di risultati. Il set di risultati è iterato utilizzando [result.each si |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC") 
    results.each do |row| 
    puts row 
    end 

## Inserimento di una riga, passaggio di parametri e recupero del valore di chiave primaria generato

Nell'esempio di codice:

- Passa i parametri per i valori da inserire in una riga.
- Inserisce la riga.
- Recupera il valore che è stato generato per la chiave primaria.

Nel database SQL, per generare automaticamente i [valori delle chiavi primarie](http://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx).

Per utilizzare TinyTDS con Azure, si consiglia di eseguire diverse istruzioni `SET` per modificare la modalità di gestione delle informazioni specifiche della sessione corrente. L'esempio di codice comprende le istruzioni `SET` consigliate. Ad esempio, `SET ANSI_NULL_DFLT_ON` farà sì che le nuove colonne create consentano valori nulli anche se lo stato di supporto di valori Null della colonna non è indicato in modo esplicito.

Per allinearlo con il formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) di Microsoft SQL Server, utilizzare la funzione[strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) per eseguire il cast nel formato datetime corrispondente.

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

<!---HONumber=Oct15_HO1-->