<properties
	pageTitle="Connettersi al Database SQL utilizzando Ruby con TinyTDS su Ubuntu"
	description="Fornire un esempio di codice Ruby che è possibile eseguire come un client su Ubuntu Linux per connettersi al Database di SQL Azure."
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
	ms.date="12/08/2015"
	ms.author="andrela"/>


# Connettersi al database SQL tramite Ruby in Ubuntu Linux

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento presenta un esempio di codice Ruby che viene eseguito in un computer client Ubuntu Linux per connettersi a un database SQL di Azure.

## Prerequisiti

### Installare i moduli necessari

Aprire il terminale in uso e installare FreeTDS se non è nel computer.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin

Dopo che il computer è configurato con FreeTDS, installare Ruby se non è già nel computer.

    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
    curl -L https://get.rvm.io | bash -s stable

Se si verificano problemi con le firme, eseguire il comando riportato di seguito.

    command curl -sSL https://rvm.io/mpapis.asc | gpg --import -

Se non si verificano problemi con le firme, eseguire il seguente comando.

    source ~/.rvm/scripts/rvm
    rvm install 2.1.2
    rvm use 2.1.2 --default
    ruby -v

Assicurarsi che siano in esecuzione la versione 2.1.2 o la macchina virtuale Ruby.

Successivamente, installare TinyTDS.

    gem install tiny_tds

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

La funzione [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) viene usata per recuperare un set di risultati di una query sul database SQL. Questa funzione accetta una query e restituisce un set di risultati. Il set di risultati è iterato usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("select * from SalesLT.Product")
    results.each do |row|
    puts row
    end

## Passaggio 4: Inserire una riga

Questo esempio illustra come eseguire un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) in modo sicuro, come passare i parametri che proteggono l'applicazione da attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [Chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) generato automaticamente.

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

<!---HONumber=AcomDC_1210_2015-->