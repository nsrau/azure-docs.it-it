---
title: 'Avvio rapido: Connettersi con Ruby - Database di Azure per PostgreSQL - Server singolo'
description: Questa guida introduttiva fornisce un esempio di codice Ruby che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL - Server singolo.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: bb2ff066089e3348c40f5a22bd73ea376ecef6de
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341400"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Avvio rapido: Usare Ruby per connettersi ai dati ed eseguire query in Database di Azure per PostgreSQL - Server singolo

Questa guida introduttiva illustra come connettersi a un database di Azure per PostgreSQL usando un'applicazione [Ruby](https://www.ruby-lang.org). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Le procedure descritte in questo articolo presuppongono che si abbia familiarità con lo sviluppo con Ruby, ma non con Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Creare un database - Portale](quickstart-create-server-database-portal.md)
- [Creare un database: interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

È anche necessario che sia installato:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby pg](https://rubygems.org/gems/pg/), il modulo PostgreSQL per Ruby

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per PostgreSQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Fare clic sul nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Nome del server Database di Azure per PostgreSQL":::

> [!NOTE]
> Il simbolo `@` nel nome utente di Postgres in Azure è stato codificato tramite URL (`%40`) in tutte le stringhe di connessione.

## <a name="connect-and-create-a-table"></a>Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE** , seguita dalle istruzioni SQL **INSERT INTO** per aggiungere righe nella tabella.

Il codice usa un ```PG::Connection```oggetto con il costruttore```new``` per la connessione a Database di Azure per PostgreSQL. Chiama quindi il metodo ```exec()``` per eseguire i comandi DROP, CREATE TABLE e INSERT INTO. Il codice cerca gli errori usando la classe ```PG::Error```. Chiama infine il metodo ```close()``` per chiudere la connessione prima di terminare. Per altre informazioni su queste classi e questi metodi, vedere [Documentazione di riferimento su Ruby Pg](https://www.rubydoc.info/gems/pg/PG).

Sostituire le stringhe `host`, `database`, `user` e `password` con valori personalizzati.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**.

Il codice usa un oggetto ```PG::Connection``` con il costruttore ```new``` per la connessione a Database di Azure per PostgreSQL. Chiama quindi il metodo ```exec()``` per eseguire il comando SELECT, mantenendo i risultati in un set di risultati. Viene eseguita l'iterazione della raccolta di set di risultati usando il ciclo `resultSet.each do`, mantenendo i valori della riga corrente nella variabile `row`. Il codice cerca gli errori usando la classe ```PG::Error```. Chiama infine il metodo ```close()``` per chiudere la connessione prima di terminare. Per altre informazioni su queste classi e questi metodi, vedere [Documentazione di riferimento su Ruby Pg](https://www.rubydoc.info/gems/pg/PG).

Sostituire le stringhe `host`, `database`, `user` e `password` con valori personalizzati.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice usa un oggetto ```PG::Connection``` con il costruttore ```new``` per la connessione a Database di Azure per PostgreSQL. Chiama quindi il metodo ```exec()``` per eseguire il comando UPDATE. Il codice cerca gli errori usando la classe ```PG::Error```. Chiama infine il metodo ```close()``` per chiudere la connessione prima di terminare. Per altre informazioni su queste classi e questi metodi, vedere [Documentazione di riferimento su Ruby Pg](https://www.rubydoc.info/gems/pg/PG).

Sostituire le stringhe `host`, `database`, `user` e `password` con valori personalizzati.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **DELETE**.

Il codice usa un oggetto ```PG::Connection``` con il costruttore ```new``` per la connessione a Database di Azure per PostgreSQL. Chiama quindi il metodo ```exec()``` per eseguire il comando UPDATE. Il codice cerca gli errori usando la classe ```PG::Error```. Chiama infine il metodo ```close()``` per chiudere la connessione prima di terminare.

Sostituire le stringhe `host`, `database`, `user` e `password` con valori personalizzati.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Documentazione di riferimento su Ruby Pg](https://www.rubydoc.info/gems/pg/PG)
