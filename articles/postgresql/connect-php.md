---
title: Connettersi a Database di Azure per PostgreSQL usando PHP | Microsoft Docs
description: "Questa guida introduttiva fornisce un esempio di codice PHP che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 06/29/2017
ms.openlocfilehash: 9beb92895056b51f40988fc6c47dfa6f80ca3294
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-postgresql-use-php-to-connect-and-query-data"></a>Database di Azure per PostgreSQL: usare PHP per connettersi ai dati ed eseguire query
Questa guida introduttiva illustra come connettersi a un database di Azure per PostgreSQL usando un'applicazione [PHP](http://php.net/manual/intro-whatis.php). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo articolo presuppone che si abbia familiarità con lo sviluppo con PHP, ma non con Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Creare un database: portale](quickstart-create-server-database-portal.md)
- [Creare un database: interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Installare PHP
Installare PHP nel server o creare un'[app Web](../app-service/app-service-web-overview.md) di Azure che includa PHP.

### <a name="windows"></a>Windows
- Scaricare [PHP versione 7.1.4 non thread-safe (x64)](http://windows.php.net/download#php-7.1)
- Installare PHP e vedere il [manuale di PHP](http://php.net/manual/install.windows.php) per le altre opzioni di configurazione
- Il codice usa la classe **pgsql** (ext/php_pgsql.dll) inclusa nell'installazione di PHP. 
- Abilitare l'estensione **pgsql** modificando il file di configurazione php.ini, che in genere si trova in `C:\Program Files\PHP\v7.1\php.ini`. Il file di configurazione dovrebbe contenere una riga con il testo `extension=php_pgsql.so`. Se non è visualizzata, aggiungere il testo e salvare il file. Se il testo è presente, ma commentato con un punto e virgola come prefisso, rimuovere il commento dal testo rimuovendo il punto e virgola.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Scaricare [PHP versione 7.1.4 non thread-safe (x64)](http://php.net/downloads.php) 
- Installare PHP e vedere il [manuale di PHP](http://php.net/manual/install.unix.php) per le altre opzioni di configurazione
- Il codice usa la classe **pgsql** class (php_pgsql.so). Installarla eseguendo `sudo apt-get install php-pgsql`.
- Abilitare l'estensione **pgsql** modificando il file di configurazione `/etc/php/7.0/mods-available/pgsql.ini`. Il file di configurazione dovrebbe contenere una riga con il testo `extension=php_pgsql.so`. Se non è visualizzata, aggiungere il testo e salvare il file. Se il testo è presente, ma commentato con un punto e virgola come prefisso, rimuovere il commento dal testo rimuovendo il punto e virgola.

### <a name="macos"></a>MacOS
- Scaricare [PHP versione 7.1.4](http://php.net/downloads.php)
- Installare PHP e vedere il [manuale di PHP](http://php.net/manual/install.macosx.php) per le altre opzioni di configurazione

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per PostgreSQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e cercare il server creato, ad esempio **mypgserver-20170401**.
3. Fare clic sul nome del server **mypgserver-20170401**.
4. Selezionare la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.
 ![Database di Azure per PostgreSQL - Accesso dell'amministratore del server](./media/connect-php/1-connection-string.png)
5. Se si dimenticano le informazioni di accesso per il server, passare alla pagina **Panoramica** per visualizzare il nome di accesso dell'amministratore del server e, se necessario, reimpostare la password.

## <a name="connect-and-create-a-table"></a>Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE**, seguita dalle istruzioni SQL **INSERT INTO** per aggiungere righe nella tabella.

Il codice chiama il metodo [pg_connect()](http://php.net/manual/en/function.pg-connect.php) per connettersi a Database di Azure per PostgreSQL. Chiama quindi il metodo [pg_query()](http://php.net/manual/en/function.pg-query.php) più volte per eseguire diversi comandi e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) ogni volta per controllare i dettagli se si è verificato un errore. Chiama infine il metodo [pg_close()](http://php.net/manual/en/function.pg-close.php) per chiudere la connessione.

Sostituire i parametri `$host`, `$database`, `$user` e `$password` con valori personalizzati. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($1, $2);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

 Il codice chiama il metodo [pg_connect()](http://php.net/manual/en/function.pg-connect.php) per connettersi a Database di Azure per PostgreSQL. Chiama quindi il metodo [pg_query()](http://php.net/manual/en/function.pg-query.php) per eseguire il comando SELECT, mantenendo i risultati in un set di risultati, e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) per controllare i dettagli se si è verificato un errore.  Per leggere il set di risultati, viene chiamato il metodo [pg_fetch_row()](http://php.net/manual/en/function.pg-fetch-row.php) in un ciclo, una volta per riga, e i dati della riga vengono recuperati in una matrice `$row`, con un valore di dati per colonna in ogni posizione della matrice.  Per liberare il set di risultati, viene chiamato il metodo [pg_free_result()](http://php.net/manual/en/function.pg-free-result.php). Chiama infine il metodo [pg_close()](http://php.net/manual/en/function.pg-close.php) per chiudere la connessione.

Sostituire i parametri `$host`, `$database`, `$user` e `$password` con valori personalizzati. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice chiama il metodo [pg_connect()](http://php.net/manual/en/function.pg-connect.php) per connettersi a Database di Azure per PostgreSQL. Chiama quindi il metodo [pg_query()](http://php.net/manual/en/function.pg-query.php) per eseguire un comando e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) per controllare i dettagli se si è verificato un errore. Chiama infine il metodo [pg_close()](http://php.net/manual/en/function.pg-close.php) per chiudere la connessione.

Sostituire i parametri `$host`, `$database`, `$user` e `$password` con valori personalizzati. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **DELETE**. 

 Il codice chiama il metodo [pg_connect()](http://php.net/manual/en/function.pg-connect.php) per connettersi a Database di Azure per PostgreSQL. Chiama quindi il metodo [pg_query()](http://php.net/manual/en/function.pg-query.php) per eseguire un comando e [pg_last_error()](http://php.net/manual/en/function.pg-last-error.php) per controllare i dettagli se si è verificato un errore. Chiama infine il metodo [pg_close()](http://php.net/manual/en/function.pg-close.php) per chiudere la connessione.

Sostituire i parametri `$host`, `$database`, `$user` e `$password` con valori personalizzati. 

```php
<?php
    // Initialize connection variables.
    $host = "mypgserver-20170401.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mypgserver-20170401";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
