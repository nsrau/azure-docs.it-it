---
title: Connettersi a Database di Azure per PostgreSQL usando il linguaggio Go | Microsoft Docs
description: "Questa guida introduttiva fornisce un esempio di linguaggio di programmazione Go che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# Database di Azure per PostgreSQL: usare il linguaggio Go per connettersi ai dati ed eseguire query
Questa guida introduttiva illustra come connettersi a un database di Azure per PostgreSQL usando il codice scritto nel linguaggio [Go](https://golang.org/). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo articolo presuppone che si abbia familiarità con lo sviluppo con Go, ma non con Database di Azure per PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Creare un database: portale](quickstart-create-server-database-portal.md)
- [Creare un database: interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Installare Go e pq
- Scaricare e installare Go seguendo le [istruzioni di installazione](https://golang.org/doc/install) per la piattaforma in uso.
- Creare una cartella per il progetto, ad esempio C:\Postgresql\. Usando la riga di comando, sostituire la directory con la cartella del progetto, ad esempio `cd C:\Postgres\`.
- Scaricare il [driver Pure Go Postgres (pq)](https://github.com/lib/pq) nella cartella del progetto digitando il comando `go get github.com/lib/pq` sempre nella stessa directory.

<a id="build-and-run-go-code" class="xliff"></a>

## Compilare ed eseguire il codice Go 
- Salvare il codice in un file di testo con estensione *.go e salvarlo nella cartella del progetto, ad esempio `C:\postgres\read.go`.
- Per eseguire il codice, sostituire la directory con la cartella del progetto `cd postgres`, quindi digitare il comando `go run read.go` per compilare l'applicazione ed eseguirla.
- Per compilare il codice in un'applicazione nativa, `go build read.go`, quindi avviare read.exe per eseguire l'applicazione.

<a id="get-connection-information" class="xliff"></a>

## Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per PostgreSQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e cercare il server creato, ad esempio **mypgserver-20170401**.
3. Fare clic sul nome del server **mypgserver-20170401**.
4. Selezionare la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.
 ![Database di Azure per PostgreSQL - Accesso dell'amministratore del server](./media/connect-go/1-connection-string.png)
5. Se si dimenticano le informazioni di accesso per il server, passare alla pagina **Panoramica** e visualizzare il nome di accesso dell'amministratore del server. Se necessario, reimpostare la password.

<a id="connect-and-create-a-table" class="xliff"></a>

## Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE**, seguita dalle istruzioni SQL **INSERT INTO** per aggiungere righe nella tabella.

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) più volte per eseguire diversi comandi SQL. Ogni volta vengono applicati un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.


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

<a id="read-data" class="xliff"></a>

## Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. La query di selezione viene eseguita chiamando il metodo [db.Query()](https://golang.org/pkg/database/sql/#DB.Query) e le righe risultanti vengono mantenute in una variabile di tipo [rows](https://golang.org/pkg/database/sql/#Rows). Il codice legge i valori dei dati delle colonne nella riga corrente usando il metodo [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) ed esegue il ciclo sulle righe usando l'iteratore [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) fino ad esaurimento delle righe. I valori delle colonne di ogni riga vengono trasmessi alla console. Ogni volta vengono applicati un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.

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

<a id="update-data" class="xliff"></a>

## Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire l'istruzione SQL che aggiorna la tabella. Vengono applicati un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.

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

<a id="delete-data" class="xliff"></a>

## Eliminare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **DELETE**. 

Il codice importa tre pacchetti: il [pacchetto sql](https://golang.org/pkg/database/sql/), il [pacchetto pq](http://godoc.org/github.com/lib/pq), come driver per comunicare con il server Postgres, e il [pacchetto fmt](https://golang.org/pkg/fmt/) per l'input e l'output stampati nella riga di comando.

Il codice chiama il metodo [sql.Open()](http://godoc.org/github.com/lib/pq#Open) per la connessione a Database di Azure per PostgreSQL e controlla la connessione usando il metodo [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Viene usato un [handle di database](https://golang.org/pkg/database/sql/#DB), contenente il pool di connessioni per il server di database. Il codice chiama il metodo [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) per eseguire l'istruzione SQL che aggiorna la tabella. Vengono applicati un metodo checkError() personalizzato per controllare se si è verificato un errore e un metodo panic per uscire se si verifica un errore.

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

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)

