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
	ms.date="12/08/2015"
	ms.author="mebha"/>


# Connessione al database SQL tramite Ruby in Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento presenta un esempio di codice Ruby in esecuzione su computer con Windows 8.1 per la connessione a un database SQL di Azure.

## Prerequisiti

###Installare i moduli necessari

Aprire il terminale in uso e installare quanto segue:

**1) Ruby:** se non è presente nel computer, installare Ruby. Per i nuovi utenti di Ruby, è consigliabile utilizzare programmi di installazione di Ruby 2.1.X. Questi forniscono un linguaggio stabile e un elenco completo dei pacchetti (gemme) compatibili e aggiornati. [Visitare la pagina di download di Ruby](http://rubyinstaller.org/downloads/) e scaricare il programma di installazione 2.1.x appropriato. Ad esempio, se si usa un computer a 64 bit, scaricare il programma di installazione **Ruby 2.1.6 (x64)**. <br/><br/>Una volta scaricato il programma di installazione, eseguire le operazioni seguenti:


- Fare doppio clic sul file per avviare il programma di installazione.

- Selezionare la lingua e accettare le condizioni.

- Nella schermata relativa alle impostazioni di installazione selezionare le caselle di controllo accanto a *Add Ruby executables to your PATH* e *Associate .rb and .rbw files with this Ruby installation*.


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


**3) TinyTDS:** passare a C:\\DevKit ed eseguire il comando seguente dal terminale. TinyTDS verrà installato nel computer.

	gem inst tiny_tds --pre

### Un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Passaggio 1: Ottenere i dettagli di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Passaggio 2: Connettersi

La funzione [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) viene usata per connettersi al database SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Passaggio 3: Eseguire una query

Copiare e incollare codice seguente in un file vuoto. Denominarlo test.rb. Quindi eseguirlo immettendo il comando seguente dal prompt dei comandi:

	ruby test.rb

Nel codice di esempio la funzione [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) viene usata per recuperare un set di risultati da una query sul database SQL. Questa funzione accetta una query e restituisce un set di risultati. Il set di risultati è iterato usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## Passaggio 4: Inserire una riga

Questo esempio illustra come eseguire un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) in modo sicuro, come passare i parametri che proteggono l'applicazione da attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [Chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) generato automaticamente.

Per usare TinyTDS con Azure, si consiglia di eseguire diverse istruzioni `SET` per modificare la modalità di gestione di informazioni specifiche della sessione corrente. Le istruzioni `SET` consigliate vengono fornite nell'esempio di codice. Ad esempio, `SET ANSI_NULL_DFLT_ON` consentirà alle nuove colonne create di consentire valori nulli anche se lo stato di supporto dei valori Null della colonna non è indicato in modo esplicito.

Per allinearlo con il formato Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx), usare la funzione [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) per eseguire il cast nel formato datetime corrispondente.

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

<!---HONumber=AcomDC_1210_2015-->