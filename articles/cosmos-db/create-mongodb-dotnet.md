---
title: Creare un'app Web usando l'API Azure Cosmos DB per MongoDB e .NET SDK
description: Illustra un esempio di codice .NET che è possibile usare per la connessione e l'esecuzione di query usando l'API Azure Cosmos DB per MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: cc910359b5519d2cf1bd8a8bf019924dff660362
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852286"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Avvio rapido: Creare un'app Web .NET usando l'API Azure Cosmos DB per MongoDB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare rapidamente database di documenti, chiave/valore e a grafo ed eseguire query su di essi sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Cosmos DB. 

Questo argomento di avvio rapido illustra come creare un account Cosmos con l'[API Azure Cosmos DB per MongoDB](mongodb-introduction.md). Si creerà e si distribuirà quindi un'app Web di tipo elenco attività basata sul [driver .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Prerequisiti per eseguire l'app di esempio

Per eseguire l'esempio, sono necessari [Visual Studio](https://www.visualstudio.com/downloads/) e un account Azure Cosmos DB valido.

Se non si ha già Visual Studio, scaricare [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web** installato con il programma di installazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

L'esempio descritto in questo articolo è compatibile con MongoDB.Driver versione 2.6.1.

## <a name="clone-the-sample-app"></a>Clonare l'app di esempio

Prima di tutto, scaricare l'app di esempio da GitHub. 

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Se non si vuole usare Git, è anche possibile [scaricare il progetto come file ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

Tutti i frammenti di codice seguenti sono tratti dal file Dal.cs nella directory DAL.

* Inizializzare il client.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Recuperare il database e la raccolta.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Recuperare tutti i documenti.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Creare un'attività e inserirla nella raccolta

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Analogamente, è possibile aggiornare ed eliminare documenti tramite i metodi [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) e [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Cosmos nel [portale di Azure](https://portal.azure.com/) fare clic su **Stringa di connessione** nel riquadro di spostamento sinistro e quindi fare clic su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare nome utente, password e host nel file Dal.cs nel passaggio seguente.

2. Aprire il file **Dal.cs** nella directory **DAL**. 

3. Copiare il valore di **nomeutente** dal portale (usando il pulsante di copia) e impostarlo come valore di **nomeutente** nel file **Dal.cs**. 

4. Copiare quindi il valore di **host** dal portale e impostarlo come valore di **host** nel file **Dal.cs**. 

5. Infine, copiare il valore di **password** dal portale e impostarlo come valore di **password** nel file **Dal.cs**. 

L'app è stata ora aggiornata con tutte le informazioni necessarie per comunicare con Cosmos DB. 
    
## <a name="run-the-web-app"></a>Eseguire l'app Web

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**. 

2. Nella casella **Sfoglia** di NuGet digitare *MongoDB.Driver*.

3. Dai risultati installare la libreria **MongoDB.Driver**. Viene installato il pacchetto MongoDB.Driver, insieme a tutte le dipendenze.

4. Premere CTRL + F5 per eseguire l'applicazione. L'app viene visualizzata nel browser. 

5. Fare clic su **Crea** nel browser e creare alcune nuove attività nell'app elenco attività.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account Cosmos, creare una raccolta ed eseguire un'app console. È ora possibile importare dati aggiuntivi nel database Cosmos. 

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)
