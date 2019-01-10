---
title: Creare un'app console usando l'API Azure Cosmos DB per MongoDB e Golang SDK
description: Illustra un esempio di codice Golang che è possibile usare per la connessione e l'esecuzione di query usando l'API Azure Cosmos DB per MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 4a7d8a8df291c1bf4ca527ce2c98a7f9ada79e5f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036036"
---
# <a name="build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Creare un'app console usando l'API Azure Cosmos DB per MongoDB e Golang SDK

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare rapidamente database di documenti, chiave/valore e a grafo ed eseguire query su di essi sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Cosmos DB.

Questo argomento di avvio rapido illustra come prendere un'app MongoDB esistente scritta in [Golang](https://golang.org/) e connetterla al database Cosmos usando l'API Azure Cosmos DB per MongoDB.

In altre parole, l'applicazione Golang rileva solo la connessione tramite un client MongoDB. Il fatto che i dati siano archiviati in un database Cosmos è trasparente per l'applicazione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) e una conoscenza di base del linguaggio [Go](https://golang.org/).
- Un IDE. [GoLand](https://www.jetbrains.com/go/) di Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) di Microsoft o [Atom](https://atom.io/). In questa esercitazione, si userà GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare l'applicazione di esempio e installare i pacchetti necessari.

1. Creare una cartella denominata CosmosDBSample all'interno della cartella GOROOT\src, che per impostazione predefinita è C:\Go\.
2. Eseguire questo comando usando una finestra del terminale git, ad esempio git bash, per clonare il repository di esempio nella cartella CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Eseguire questo comando per ottenere il pacchetto mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

Il driver [mgo](https://labix.org/mgo) è un driver [MongoDB](https://www.mongodb.com/) per il [linguaggio Go](https://golang.org/) che implementa una selezione estesa e ben collaudata di funzionalità in un'API molto semplice basata sui termini Go standard.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Fare clic su **Avvio rapido** nel menu di spostamento a sinistra e quindi su **Altri** per visualizzare le informazioni relative alla stringa di connessione necessarie per l'applicazione Go.

2. In Gogland aprire il file main.go nella directory GOROOT\CosmosDBSample e aggiornare le righe di codice seguenti usando le informazioni relative alla stringa di connessione del portale di Azure illustrate nello screenshot riportato di seguito. 

    Il nome del database è il prefisso del valore **Host** nel riquadro relativo alla stringa di connessione del portale di Azure. Per l'account riportato nell'immagine seguente, il nome del database è golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Scheda Altri del riquadro Avvio rapido nel portale di Azure con le informazioni relative alla stringa di connessione](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Salvare il file main.go.

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. Altrimenti è possibile passare direttamente a [Esecuzione dell'app](#run-the-app). 

Tutti i frammenti di codice seguenti sono tratti dal file main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Connessione dell'app Go a Cosmos DB

L'API Azure Cosmos DB per MongoDB supporta la connessione abilitata per SSL. Per stabilire la connessione, è necessario definire la funzione **DialServer** in [mgo.DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo) e usare la funzione [tls.*Dial*](https://golang.org/pkg/crypto/tls#Dial) per la connessione.

Il frammento di codice Golang seguente connette l'app Go con l'API Azure Cosmos DB per MongoDB. La classe *DialInfo* contiene le opzioni per stabilire una sessione.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

Il metodo **mgo.Dial()** viene usato quando non è disponibile una connessione SSL. Per una connessione SSL è necessario il metodo **mgo.DialWithInfo()**.

Per creare l'oggetto sessione viene usata un'istanza dell'oggetto **DialWIthInfo{}**. Dopo che la sessione è stata stabilita, è possibile accedere alla raccolta usando il frammento di codice seguente:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Creare un documento

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Leggere o eseguire query in un documento

Cosmos DB supporta le query avanzate sui dati archiviati in ogni raccolta. L'esempio di codice seguente illustra una query eseguibile nei documenti della raccolta.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Aggiornare un documento

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Eliminare un documento

Cosmos DB supporta l'eliminazione di documenti.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Esecuzione dell'app

1. In Golang verificare che il valore GOPATH, disponibile in **File**, **Settings** (Impostazioni), **Go**, **GOPATH**, includa il percorso di installazione di gopkg, che per impostazione predefinita è USERPROFILE\go. 
2. Impostare come commento le righe da 103 a 107, che eliminano il documento, per poter visualizzare il documento dopo l'esecuzione dell'app.
3. In Golang fare clic su **Run** (Esegui) e quindi su **Run 'Build main.go and run'** (Esegui "Compila main.go ed esegui").

    L'app viene completata e visualizza la descrizione del documento creato in [Creare un documento](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Visualizzazione dell'output dell'app in Golang](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Esaminare il documento in Esplora dati

Tornare al portale di Azure per visualizzare il documento in Esplora dati.

1. Fare clic su **Esplora dati (anteprima)** nel menu di spostamento a sinistra, espandere **golang-coach**, **pacchetto** e quindi fare clic su **Documenti**. Nella scheda **Documenti** fare clic su \_id per visualizzare il documento nel riquadro destro. 

    ![Esplora dati con documento appena creato](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Si può quindi lavorare sul documento inline e fare clic su **Aggiorna** per salvarlo. È anche possibile eliminare il documento oppure creare nuovi documenti o nuove query.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account Cosmos ed eseguire un'app Golang. È ora possibile importare dati aggiuntivi nel database Cosmos. 

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)
