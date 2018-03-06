---
title: Connettersi a Database di Azure per MySQL con Go
description: "Questa guida introduttiva fornisce diversi esempi di codice Go che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: af4027835ca503c0875d098d0daf7a98bdef44fb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-go-language-to-connect-and-query-data"></a>Database di Azure per MySQL: usare il linguaggio Go per connettersi ai dati ed eseguire query
Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL dalle piattaforme Windows, Ubuntu Linux e Apple macOS usando codice scritto nel linguaggio [Go](https://golang.org/). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo argomento presuppone che si abbia familiarità con lo sviluppo con Go, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-go-and-mysql-connector"></a>Installare Go e il connettore MySQL
Installare [Go](https://golang.org/doc/install) e il [driver go-sql per MySQL](https://github.com/go-sql-driver/mysql#installation) nel computer. A seconda della piattaforma, seguire le istruzioni nella sezione appropriata:

### <a name="windows"></a>Windows
1. [Scaricare](https://golang.org/dl/) e installare Go per Microsoft Windows seguendo le [istruzioni di installazione](https://golang.org/doc/install).
2. Avviare il prompt dei comandi dal menu Start.
3. Creare una cartella per il progetto, ad esempio `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Passare alla cartella del progetto, ad esempio `cd %USERPROFILE%\go\src\mysqlgo`.
5. Impostare la variabile di ambiente per GOPATH in modo che punti alla directory del codice sorgente. `set GOPATH=%USERPROFILE%\go`.
6. Installare il [driver go-sql per MySQL](https://github.com/go-sql-driver/mysql#installation) eseguendo il comando `go get github.com/go-sql-driver/mysql`.

   In sintesi, installare Go e quindi eseguire questi comandi nel prompt dei comandi:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Avviare la shell Bash. 
2. Installare Go eseguendo `sudo apt-get install golang-go`.
3. Creare una cartella per il progetto nella home directory, ad esempio `mkdir -p ~/go/src/mysqlgo/`.
4. Passare alla cartella, ad esempio `cd ~/go/src/mysqlgo/`.
5. Impostare la variabile di ambiente GOPATH in modo che punti a una directory di origine valida, ad esempio la cartella go della home directory corrente. Nella shell Bash eseguire `export GOPATH=~/go` per aggiungere la directory go come GOPATH per la sessione shell corrente.
6. Installare il [driver go-sql per MySQL](https://github.com/go-sql-driver/mysql#installation) eseguendo il comando `go get github.com/go-sql-driver/mysql`.

   In sintesi, eseguire questi comandi Bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Scaricare e installare Go seguendo le [istruzioni di installazione](https://golang.org/doc/install) per la piattaforma in uso. 
2. Avviare la shell Bash.
3. Creare una cartella per il progetto nella home directory, ad esempio `mkdir -p ~/go/src/mysqlgo/`.
4. Passare alla cartella, ad esempio `cd ~/go/src/mysqlgo/`.
5. Impostare la variabile di ambiente GOPATH in modo che punti a una directory di origine valida, ad esempio la cartella go della home directory corrente. Nella shell Bash eseguire `export GOPATH=~/go` per aggiungere la directory go come GOPATH per la sessione shell corrente.
6. Installare il [driver go-sql per MySQL](https://github.com/go-sql-driver/mysql#installation) eseguendo il comando `go get github.com/go-sql-driver/mysql`.

   In sintesi, installare Go e quindi eseguire questi comandi Bash:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Fare clic sul nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 ![Nome del server del database di Azure per MySQL](./media/connect-go/1_server-overview-name-login.png)
   

## <a name="build-and-run-go-code"></a>Compilare ed eseguire il codice Go 
1. Per scrivere codice Golang si può usare un editor di testo semplice, ad esempio Blocco note di Microsoft Windows, [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) o [Nano](https://www.nano-editor.org/) in Ubuntu oppure TextEdit in macOS. Se si preferisce un ambiente IDE (Interactive Development Environment) avanzato, provare [Gogland](https://www.jetbrains.com/go/) di Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) di Microsoft o [Atom](https://atom.io/).
2. Incollare nei file di testo il codice Go riportato nelle sezioni seguenti, quindi salvare i file nella cartella del progetto con l'estensione \*.go, ad esempio il percorso di Windows `%USERPROFILE%\go\src\mysqlgo\createtable.go` o il percorso di Linux `~/go/src/mysqlgo/createtable.go`.
3. Trovare le costanti `HOST`, `DATABASE`, `USER` e `PASSWORD` nel codice e quindi sostituire i valori di esempio con i propri valori. 
4. Avviare il prompt dei comandi o la shell Bash. Passare alla cartella del progetto, ad esempio `cd %USERPROFILE%\go\src\mysqlgo\` in Windows. In Linux `cd ~/go/src/mysqlgo/`.  Alcuni degli editor dell'ambiente di sviluppo integrato indicati offrono funzionalità di debug e di runtime che non richiedono comandi della shell.
5. Eseguire il codice digitando il comando `go run createtable.go` per compilare l'applicazione ed eseguirla. 
6. In alternativa, per compilare il codice in un'applicazione nativa, digitare `go build createtable.go` e quindi avviare `createtable.exe` per eseguire l'applicazione.

## <a name="connect-create-table-and-insert-data"></a>Connettersi, creare tabelle e inserire dati
Usare il codice seguente per connettersi al server, creare una tabella e caricare i dati usando un'istruzione SQL **INSERT**. 

Il codice importa tre pacchetti: il [pacchetto SQL](https://golang.org/pkg/database/sql/), il [driver go sql per MySql](https://github.com/go-sql-driver/mysql#installation) come driver per comunicare con il database di Azure per MySQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://go-database-sql.org/accessing.html) per la connessione al database di Azure per MySQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) più volte per eseguire diversi comandi DDL. Il codice usa anche [Prepare()](http://go-database-sql.org/prepared.html) ed Exec() per eseguire le istruzioni preparate con parametri diversi per inserire tre righe. Ogni volta viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire.

Sostituire le costanti `host`, `database`, `user` e `password` con i propri valori. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice importa tre pacchetti: il [pacchetto SQL](https://golang.org/pkg/database/sql/), il [driver go sql per MySql](https://github.com/go-sql-driver/mysql#installation) come driver per comunicare con il database di Azure per MySQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://go-database-sql.org/accessing.html) per la connessione al database di Azure per MySQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Query()](https://golang.org/pkg/database/sql/#DB.Query) per eseguire il comando SELECT. Esegue quindi il metodo [Next()](https://golang.org/pkg/database/sql/#Rows.Next) per scorrere il set di risultati e [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) per analizzare i valori delle colonne, salvando il valore nelle variabili. Ogni volta viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire.

Sostituire le costanti `host`, `database`, `user` e `password` con i propri valori. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**. 

Il codice importa tre pacchetti: il [pacchetto SQL](https://golang.org/pkg/database/sql/), il [driver go sql per MySql](https://github.com/go-sql-driver/mysql#installation) come driver per comunicare con il database di Azure per MySQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://go-database-sql.org/accessing.html) per la connessione al database di Azure per MySQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire il comando UPDATE. Ogni volta viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire.

Sostituire le costanti `host`, `database`, `user` e `password` con i propri valori. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e rimuovere i dati usando un'istruzione SQL **DELETE**. 

Il codice importa tre pacchetti: il [pacchetto SQL](https://golang.org/pkg/database/sql/), il [driver go sql per MySql](https://github.com/go-sql-driver/mysql#installation) come driver per comunicare con il database di Azure per MySQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://go-database-sql.org/accessing.html) per la connessione al database di Azure per MySQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire il comando DELETE. Ogni volta viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire.

Sostituire le costanti `host`, `database`, `user` e `password` con i propri valori. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./concepts-migrate-import-export.md)
