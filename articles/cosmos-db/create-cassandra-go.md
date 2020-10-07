---
title: Creare un'app Go con l'API Cassandra di Azure Cosmos DB usando il client gocql
description: Questo argomento di avvio rapido illustra come usare un client Go per interagire con l'API Cassandra di Azure Cosmos DB
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: ba53fb786b1d1f61535168cda2152049a12dfb99
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "86535652"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Avvio rapido: Creare un'app Go con il client `gocql` per gestire i dati dell'API Cassandra di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale. In questo argomento di avvio rapido si inizierà creando un account API Cassandra di Azure Cosmos DB. Si eseguirà quindi un'applicazione Go per creare gli elementi table e keyspace di Cassandra e quindi eseguire alcune operazioni. Questa app Go usa [gocql](https://github.com/gocql/gocql), che è un client Cassandra per il linguaggio Go. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) senza una sottoscrizione di Azure.
- [Go](https://golang.org/) installato nel computer e conoscenza del funzionamento di Go.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database, è necessario creare un account di Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Per iniziare, clonare l'applicazione da GitHub.

1. Aprire un prompt dei comandi e creare una cartella denominata `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash. Usare il comando `cd` per passare alla nuova cartella e installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per comprendere in che modo il codice crea le risorse del database, è possibile esaminare i frammenti di codice seguenti. In caso contrario, passare direttamente a [Eseguire l'applicazione](#run-the-application)

La funzione `GetSession` (inclusa in `utils\utils.go`) restituisce un oggetto [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) usato per eseguire operazioni del cluster, ad esempio inserimento, ricerca e così via.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

L'host Cassandra di Azure Cosmos DB viene passato alla funzione [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) per ottenere uno struct [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) che viene quindi configurato per usare il nome utente, la password, la porta e la versione appropriata di TLS ([requisito di sicurezza della crittografia HTTPS/SSL/TLS](https://docs.microsoft.com/azure/cosmos-db/database-security?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

La funzione `GetSession` viene quindi chiamata dalla funzione `main` (`main.go`).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

Le credenziali e le informazioni sulla connettività vengono accettate sotto forma di variabili di ambiente (risolte nel metodo `init`)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Viene quindi usata per eseguire varie operazioni (incluse in `operations\setup.go`) su Azure Cosmos DB a partire dalla creazione di `keyspace` e `table`.

Come suggerisce il nome, la funzione `DropKeySpaceIfExists` elimina l'elemento `keyspace` solo se esiste.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

La funzione `CreateKeySpace` consente di creare l'elemento `keyspace` (`user_profile`)

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Questa operazione è seguita dalla creazione della tabella (`user`) che viene gestita dalla funzione `CreateUserTable`

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Dopo aver creato gli elementi keyspace e table, vengono richiamate le operazioni CRUD (incluse in `operations\crud.go`). 

Per creare un elemento `User`, si usa `InsertUser`. Questa funzione consente di impostare le informazioni utente (ID, nome e città) come argomenti di query usando [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

Per cercare un utente (`model\user.go`) usando un ID utente specifico, si usa `FindUser`, mentre [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) consente di associare gli attributi utente (restituiti da Cassandra) a singole variabili (`userid`, `name`, `city`). Questo è solo uno dei modi in cui è possibile usare il risultato ottenuto come risultato della query di ricerca

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

Per recuperare tutti gli utenti, si usa `FindAllUsers`. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) è la versione abbreviata per ottenere tutte le informazioni sull'utente sotto forma di sezione dell'elemento `map`. Si pensi a ogni elemento `map` come una coppia chiave-valore in cui il nome della colonna (ad esempio `user_id`) rappresenta la chiave, unitamente al rispettivo valore.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Ogni elemento `map` delle informazioni utente viene convertito in un elemento `User` tramite la funzione `mapToUser`, che estrae semplicemente il valore dalla rispettiva colonna e lo usa per creare un'istanza dello struct `User`

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Come menzionato in precedenza, l'applicazione accetta le informazioni su credenziali e connettività sotto forma di variabili di ambiente. 

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Stringa di connessione**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Visualizzare e copiare i dettagli della pagina Stringa di connessione nel portale di Azure":::

Copiare i valori relativi agli attributi seguenti (`CONTACT POINT`, `PORT`, `USERNAME` e `PRIMARY PASSWORD`) e impostarli sulle rispettive variabili di ambiente

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Nella finestra del terminale passare alla cartella corretta. Ad esempio:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. Nel terminale eseguire il comando seguente per avviare l'applicazione.

```shell
go run main.go
```

3. Nella finestra del terminale vengono visualizzate le notifiche per le varie operazioni, ad esempio la configurazione di keyspace e table, la creazione dell'utente e così via.

4. Nel portale di Azure aprire **Esplora dati** per modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Visualizzare e copiare i dettagli della pagina Stringa di connessione nel portale di Azure":::

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account di Azure Cosmos DB con l'API Cassandra e come eseguire un'app Go per creare un database e un contenitore Cassandra. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)
