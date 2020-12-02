---
title: Connettere un'applicazione Go all'API Azure Cosmos DB per MongoDB
description: Questa guida introduttiva illustra come connettere un'applicazione Go esistente all'API Azure Cosmos DB per MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 548deeec456537c64dc5d8ebe95ed1e4802e91c5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349215"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Avvio rapido: Connettere un'applicazione Go all'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale. Questa guida introduttiva illustra come creare e gestire un account Azure Cosmos DB tramite Azure Cloud Shell e come clonare un'applicazione di esempio esistente da GitHub e configurarla per l'uso con Azure Cosmos DB. 

L'applicazione di esempio è uno strumento di gestione `todo` basato su riga di comando scritto in Go. L'API Azure Cosmos DB per MongoDB è [compatibile con il protocollo di collegamento MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), rendendo possibile la connessione di qualsiasi driver client di MongoDB. Questa applicazione usa il [driver Go per MongoDB](https://github.com/mongodb/mongo-go-driver) in modo trasparente per l'applicazione di archiviazione dei dati in un database Azure Cosmos DB.

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure. È anche possibile usare l'[Emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con la stringa di connessione `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Go](https://golang.org/) installato nel computer e conoscenza del funzionamento di Go.
- [Git](https://git-scm.com/downloads).
- Se non si vuole usare Azure Cloud Shell, [Azure CLI 2.0+](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Eseguire i comandi seguenti per clonare il repository di esempio.

1. Aprire un prompt dei comandi, creare una nuova cartella denominata `git-samples` e quindi chiudere il prompt dei comandi.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Se si è interessati a comprendere il funzionamento dell'applicazione, è possibile esaminare i frammenti di codice seguenti. In caso contrario, passare direttamente a [Eseguire l'applicazione](#run-the-application). Il layout dell'applicazione è il seguente:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Tutti i frammenti di codice seguenti sono tratti dal file `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Connessione dell'app Go ad Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) incapsula la stringa di connessione per Azure Cosmos DB, che viene passata tramite una variabile di ambiente (dettagli disponibili nella sezione successiva). La connessione viene inizializzata tramite [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) a cui viene passata l'istanza di `clientOptions`. La funzione [`Ping` viene richiamata](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) per confermare la riuscita della connettività (si tratta di una strategia con risposta immediata agli errori)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> L'uso della configurazione [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) è importante per non ricevere l'errore di connettività seguente: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Creare un elemento `todo`

Per creare un elemento `todo`, è necessario ottenere un punto di controllo per [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) e richiamare la funzione [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne). 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Viene passato un oggetto struct `Todo` che contiene la descrizione e lo stato (inizialmente impostato su `pending`)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Elencare elementi `todo`

È possibile elencare elementi TODO in base a criteri specifici. Viene creato un elemento [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) per incapsulare i criteri di filtro

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) consente di cercare i documenti in base al filtro e il risultato viene convertito in una sezione di `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Le informazioni vengono infine visualizzate in formato tabulare

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Aggiornare un elemento `todo`

Un elemento `todo` può essere aggiornato in base al relativo `_id`. Viene creato un filtro [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) in base all'elemento `_id` e ne viene creato un altro per le informazioni aggiornate, che in questo caso rappresentano un nuovo stato (`completed` o `pending`). Viene richiamata infine la funzione [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) con il filtro e il documento aggiornati

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Eliminare un elemento `todo`

Un elemento `todo` viene eliminato in base al relativo `_id` e viene incapsulato in forma di un'istanza di [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D). La funzione [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) viene richiamata per eliminare il documento.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Compilare l'applicazione

Passare alla directory in cui è stata clonata l'applicazione e compilarla (tramite `go build`).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Per verificare che l'applicazione sia stata compilata correttamente.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Configurazione di Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]. 

Se si usa un'interfaccia della riga di comando di Azure installata, accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index#az-login) e seguire le istruzioni visualizzate. Se si usa Azure Cloud Shell, è possibile ignorare questo passaggio.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Aggiungere il modulo Azure Cosmos DB

Se si usa un'interfaccia della riga di comando di Azure installata, verificare se il componente `cosmosdb` è già installato eseguendo il comando `az`. Se `cosmosdb` è nell'elenco di comandi di base, passare al comando successivo. Se si usa Azure Cloud Shell, è possibile ignorare questo passaggio.

Se `cosmosdb` non è nell'elenco dei comandi di base, reinstallare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../azure-resource-manager/management/overview.md) con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione. 

L'esempio seguente crea un gruppo di risorse nell'area Europa occidentale. Scegliere un nome univoco per il gruppo di risorse.

Se si usa Azure Cloud Shell, selezionare **Prova**, seguire le istruzioni visualizzate per eseguire l'accesso e quindi copiare il comando nel prompt dei comandi.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare un account Cosmos con il comando [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

Nel comando seguente sostituire il segnaposto `<cosmosdb-name>` con il nome univoco dell'account Cosmos. Questo nome univoco verrà usato come parte dell'endpoint Cosmos DB, `https://<cosmosdb-name>.documents.azure.com/`, quindi deve essere univoco tra tutti gli account Cosmos in Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Il parametro `--kind MongoDB` consente le connessioni al client MongoDB.

Dopo la creazione dell'account Azure Cosmos DB, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. 

> [!NOTE]
> Questo esempio usa JSON come formato di output dell'interfaccia della riga di comando di Azure, ovvero l'impostazione predefinita. Per usare un altro formato di output, vedere [Formati di output per i comandi dell'interfaccia della riga di comando di Azure](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Recuperare la chiave del database

Per connettersi a un database Cosmos, è necessaria la chiave del database. Usare il comando [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) per recuperare la chiave primaria.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

L'interfaccia della riga di comando di Azure restituisce informazioni simili all'esempio seguente. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Configurare l'applicazione 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Esportare la stringa di connessione, il database MongoDB e i nomi di raccolta come variabili di ambiente. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> L'opzione `ssl=true` è importante in considerazione dei requisiti di Cosmos DB. Per altre informazioni, vedere [Requisiti della stringa di connessione](connect-mongodb-account.md#connection-string-requirements).
>

Per la variabile di ambiente `MONGODB_CONNECTION_STRING`, sostituire i segnaposto per `<COSMOSDB_ACCOUNT_NAME>` e `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: nome dell'account Azure Cosmos DB creato
2. `<COSMOSDB_PASSWORD>`: chiave di database estratta nel passaggio precedente

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

È possibile scegliere i valori desiderati per `MONGODB_DATABASE` e `MONGODB_COLLECTION` o lasciarli invariati.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per creare un elemento `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

In caso di esito positivo, verrà visualizzato un output con l'elemento `_id` MongoDB del documento appena creato:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Creare un altro elemento `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Elencare tutti gli elementi `todo`

```bash
./todo --list all
```

Vengono visualizzati gli elementi aggiunti in un formato tabulare

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Per aggiornare lo stato di un elemento `todo` (ad esempio per impostare lo stato `completed`), usare l'ID di `todo`

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Elencare solo gli elementi `todo` completati

```bash
./todo --list completed
```

Viene visualizzato solo l'elemento appena aggiornato

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Visualizzare i dati in Esplora dati

I dati archiviati in Azure Cosmos DB sono disponibili per la visualizzazione e l'esecuzione di query nel portale di Azure.

Per visualizzare e usare i dati utente creati nel passaggio precedente, nonché eseguire query su di essi, accedere al [portale di Azure](https://portal.azure.com) nel Web browser.

Nella casella di ricerca nella parte superiore immettere **Azure Cosmos DB**. Quando viene aperto il pannello dell'account Cosmos, selezionare l'account Cosmos. Nel riquadro di spostamento a sinistra selezionare **Esplora dati**. Espandere la raccolta nel riquadro Raccolte. Sarà quindi possibile visualizzare i documenti nella raccolta, eseguire query sui dati e anche creare ed eseguire stored procedure, trigger e funzioni definite dall'utente. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Esplora dati con documento appena creato":::


Eliminare un elemento `todo` tramite il relativo ID

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Elencare gli elementi `todo` da confermare

```bash
./todo --list all
```

L'elemento `todo` eliminato non deve essere presente

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un account dell'API MongoDB Azure Cosmos DB usando Azure Cloud Shell e come creare ed eseguire un'app Go da riga di comando per gestire gli elementi `todo`. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)