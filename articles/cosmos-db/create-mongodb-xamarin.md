---
title: Creare un'app Xamarin.Forms con .NET e l'API di Azure Cosmos DB per MongoDB
description: Presenta un esempio di codice Xamarin che permette di connettersi all'API di Azure Cosmos DB per MongoDB ed eseguire query su di essa
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 9236a340c05bc5e342b9fc2c377d6d8eacb94f7d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036426"
---
# <a name="quickstart-quickstart-build-a-xamarinforms-app-with-net-and-azure-cosmos-dbs-api-for-mongodb"></a>Guida introduttiva: Guida introduttiva: Creare un'app Xamarin.Forms con .NET e l'API di Azure Cosmos DB per MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB.

Questa guida introduttiva illustra come creare un [account Cosmos configurato con l'API di Azure Cosmos DB per MongoDB](mongodb-introduction.md), un database di documenti e una raccolta usando il portale di Azure. Si creerà quindi un'app Xamarin.Forms Todo usando il [driver .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Prerequisiti per eseguire l'app di esempio

Per eseguire l'esempio, sono necessari [Visual Studio](https://www.visualstudio.com/downloads/) o [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e un account Azure CosmosDB valido.

Se non si ha già Visual Studio, scaricare [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo per dispositivi mobili con .NET** installato con il programma di installazione.

Se si preferisce lavorare con un computer Mac, scaricare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) ed eseguire il programma di installazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

L'esempio descritto in questo articolo è compatibile con MongoDB.Driver versione 2.6.1.

## <a name="clone-the-sample-app"></a>Clonare l'app di esempio

Prima di tutto, scaricare l'app di esempio da GitHub. L'app implementa un'app Todo con il modello di archiviazione dei documenti di MongoDB.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Se non si vuole usare Git, è anche possibile [scaricare il progetto come file ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string).

I frammenti seguenti provengono tutti dalla classe `MongoService`, nel percorso seguente: src/TaskList.Core/Services/MongoService.cs.

* Inizializzare il client Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Recuperare un riferimento al database e alla raccolta. Se non esistono già, il .NET SDK di MongoDB creerà automaticamente il database e la raccolta.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Recuperare tutti i documenti come Elenco.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Eseguire una query per documenti specifici.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Creare un'attività e inserirla nella raccolta.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Aggiornare un'attività in una raccolta.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Eliminare un'attività in una raccolta.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) fare clic su **Stringa di connessione** nel riquadro di spostamento a sinistra e quindi su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro della schermata per copiare la stringa di connessione primaria nei passaggi successivi.

2. Aprire il file **APIKeys.cs** nella directory **Helpers** del progetto **TaskList.Core**.

3. Copia il valore della **stringa di connessione primaria** dal portale (usando il pulsante di copia) e impostarlo come valore del campo **ConnectionString** nel file **APIKeys.cs**.

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB.

## <a name="run-the-app"></a>Esecuzione dell'app

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. In Visual Studio fare, clic con il pulsante destro del mouse su ciascun progetto in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**.
2. Fare clic su **Ripristina tutti i pacchetti NuGet**.
3. Fare clic con il pulsante destro su **TaskList.Android** e selezionare **Imposta come progetto di avvio**.
4. Premere F5 per avviare il debug dell'applicazione.
5. Se si desidera eseguire l' su iOS, in primo luogo assicurarsi che il dispositivo sia connesso a un computer Mac (vedere [istruzioni](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) su come eseguire questa operazione).
6. Fare clic con il pulsante destro sul progetto **TaskList.iOS** e selezionare **Imposta come progetto di avvio**.
7. Fare clic su F5 per avviare il debug dell'applicazione.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Nell'elenco a discesa della piattaforma, selezionare TaskList.iOS o TaskList.Android, a seconda della piattaforma su cui si desidera eseguire l'applicazione.
2. Premere cmd+Invio per avviare il debug dell'applicazione.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un account Azure Cosmos DB ed eseguire un'app Xamarin.Forms con l'API per MongoDB. È ora possibile importare dati aggiuntivi nell'account Cosmos DB.

> [!div class="nextstepaction"]
> [Importare i dati in Azure Cosmos DB configurato con l'API di Azure Cosmos DB per MongoDB](mongodb-migrate.md)
