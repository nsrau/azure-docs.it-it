---
title: Connettersi a Database di Azure per PostgreSQL usando il linguaggio Go | Microsoft Docs
description: "Questa guida introduttiva fornisce un esempio di linguaggio di programmazione Go che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: 8b52aeaadf7ba94d6b79ef447600cd7b57e70dfa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-use-go-language-to-connect-and-query-data"></a>Database di Azure per PostgreSQL: usare il linguaggio Go per connettersi ai dati ed eseguire query
Questa guida introduttiva illustra come connettersi a un database di Azure per PostgreSQL usando il codice scritto nel linguaggio [Go](https://golang.org/) (golang). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo articolo presuppone che si abbia familiarità con lo sviluppo con Go, ma non con Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Creare un database: portale](quickstart-create-server-database-portal.md)
- [Creare un database: interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Installare Go e il connettore pq
Installare [Go](https://golang.org/doc/install) e il [driver Pure Go per Postgres (pq)](https://github.com/lib/pq) nel computer. A seconda della piattaforma, seguire le istruzioni appropriate:

### <a name="windows"></a>Windows
1. [Scaricare](https://golang.org/dl/) e installare Go per Microsoft Windows seguendo le [istruzioni di installazione](https://golang.org/doc/install).
2. Avviare il prompt dei comandi dal menu Start.
3. Creare una cartella per il progetto, ad esempio `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Passare alla cartella del progetto, ad esempio `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Impostare la variabile di ambiente per GOPATH in modo che punti alla directory del codice sorgente. `set GOPATH=%USERPROFILE%\go`.
6. Installare il [driver Pure Go per Postgres (pq)](https://github.com/lib/pq) eseguendo il comando `go get github.com/lib/pq`.

   In sintesi, installare Go e quindi eseguire questi comandi nel prompt dei comandi:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Avviare la shell Bash. 
2. Installare Go eseguendo `sudo apt-get install golang-go`.
3. Creare una cartella per il progetto nella home directory, ad esempio `mkdir -p ~/go/src/postgresqlgo/`.
4. Passare alla cartella, ad esempio `cd ~/go/src/postgresqlgo/`.
5. Impostare la variabile di ambiente GOPATH in modo che punti a una directory di origine valida, ad esempio la cartella go della home directory corrente. Nella shell Bash eseguire `export GOPATH=~/go` per aggiungere la directory go come GOPATH per la sessione shell corrente.
6. Installare il [driver Pure Go per Postgres (pq)](https://github.com/lib/pq) eseguendo il comando `go get github.com/lib/pq`.

   In sintesi, eseguire questi comandi Bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Scaricare e installare Go seguendo le [istruzioni di installazione](https://golang.org/doc/install) per la piattaforma in uso. 
2. Avviare la shell Bash. 
3. Creare una cartella per il progetto nella home directory, ad esempio `mkdir -p ~/go/src/postgresqlgo/`.
4. Passare alla cartella, ad esempio `cd ~/go/src/postgresqlgo/`.
5. Impostare la variabile di ambiente GOPATH in modo che punti a una directory di origine valida, ad esempio la cartella go della home directory corrente. Nella shell Bash eseguire `export GOPATH=~/go` per aggiungere la directory go come GOPATH per la sessione shell corrente.
6. Installare il [driver Pure Go per Postgres (pq)](https://github.com/lib/pq) eseguendo il comando `go get github.com/lib/pq`.

   In sintesi, installare Go e quindi eseguire questi comandi Bash:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per PostgreSQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e cercare il server creato, ad esempio **mypgserver-20170401**.
3. Fare clic sul nome del server **mypgserver-20170401**.
4. Selezionare la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.
 ![Database di Azure per PostgreSQL - Accesso dell'amministratore del server](./media/connect-go/1-connection-string.png)
5. Se si dimenticano le informazioni di accesso per il server, passare alla pagina **Panoramica** e visualizzare il nome di accesso dell'amministratore del server. Se necessario, reimpostare la password.

## <a name="build-and-run-go-code"></a>Compilare ed eseguire il codice Go 
1. Per scrivere codice Golang, è possibile usare un editor di testo normale, ad esempio Blocco note di Microsoft Windows, [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) o [Nano](https://www.nano-editor.org/) in Ubuntu oppure TextEdit in macOS. Se si preferisce un ambiente IDE (Interactive Development Environment) avanzato, provare [Gogland](https://www.jetbrains.com/go/) di Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) di Microsoft o [Atom](https://atom.io/).
2. Incollare nel file di testo il codice Golang riportato nelle sezioni seguenti e quindi salvare il file nella cartella del progetto con l'estensione \*.go, ad esempio il percorso di Windows `%USERPROFILE%\go\src\postgresqlgo\createtable.go` o il percorso di Linux `~/go/src/postgresqlgo/createtable.go`.
3. Trovare le costanti `HOST`, `DATABASE`, `USER` e `PASSWORD` nel codice e sostituire i valori di esempio con i propri valori.  
4. Avviare il prompt dei comandi o la shell Bash. Passare alla cartella del progetto, ad esempio `cd %USERPROFILE%\go\src\postgresqlgo\` in Windows. In Linux `cd ~/go/src/postgresqlgo/`. Alcuni degli ambienti IDE indicati offrono funzionalità di debug e di runtime che non richiedono comandi della shell.
5. Eseguire il codice digitando il comando `go run createtable.go` per compilare l'applicazione ed eseguirla. 
6. In alternativa, per compilare il codice in un'applicazione nativa, digitare `go build createtable.go` e quindi avviare `createtable.exe` per eseguire l'applicazione.

## <a name="connect-and-create-a-table"></a>Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE**, seguita dalle istruzioni SQL **INSERT INTO** per aggiungere righe nella tabella.

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq) come driver per comunicare con il server PostgreSQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per il database PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) più volte per eseguire diversi comandi SQL. Ogni volta un metodo checkError() personalizzato controlla se si è verificato un errore e un metodo panic esce se si verifica un errore.

Sostituire i parametri `HOST`, `DATABASE`, `USER` e `PASSWORD` con valori personalizzati. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq) come driver per comunicare con il server PostgreSQL e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per il database PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. La query di selezione viene eseguita chiamando il metodo [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) e le righe risultanti vengono mantenute in una variabile di tipo [rows](https://golang.org/pkg/database/sql/#Rows). Il codice legge i valori dei dati delle colonne nella riga corrente usando il metodo [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) ed esegue il ciclo sulle righe usando l'iteratore [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) fino ad esaurimento delle righe. I valori delle colonne di ogni riga vengono trasmessi alla console. Ogni volta viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e il metodo panic per uscire se si verifica un errore.

Sostituire i parametri `HOST`, `DATABASE`, `USER` e `PASSWORD` con valori personalizzati. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per il database PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire l'istruzione SQL che aggiorna la tabella. Viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.

Sostituire i parametri `HOST`, `DATABASE`, `USER` e `PASSWORD` con valori personalizzati. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi ed eliminare i dati usando un'istruzione SQL **DELETE**. 

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per il database PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire l'istruzione SQL che elimina una riga dalla tabella. Viene usato un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.

Sostituire i parametri `HOST`, `DATABASE`, `USER` e `PASSWORD` con valori personalizzati. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
