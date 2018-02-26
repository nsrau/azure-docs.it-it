---
title: Creare un'applicazione Framework o Core Cosmos DB tramite l'API Graph | Microsoft Docs
description: Presenta un esempio di codice .NET Framework/Core che permette di connettersi ad Azure Cosmos DB ed eseguire query sul servizio
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 38869444d43a3fb5c37a222ef58d30fc607106aa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-graph-api"></a>Azure Cosmos DB: Creare un'applicazione .NET Framework o Core tramite l'API Graph

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida di avvio rapido mostra come creare un account, un database e un grafo (contenitore) di Azure Cosmos DB tramite il portale di Azure. In seguito, si crea e si esegue un'app console basata sul driver open source [Gremlin.Net](http://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>prerequisiti

Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

Se si dispone già di Visual Studio 2017 installato, assicurarsi che sia installata la versione [Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Graph da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare alla directory di lavoro.  

2. Eseguire il comando seguente per clonare l'archivio di esempio. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

3. Aprire quindi Visual Studio e il file della soluzione.

4. Ripristinare i pacchetti NuGet nel progetto. Sono inclusi il driver Gremlin.Net e il pacchetto Newtonsoft.Json.

5. È anche possibile installare il driver Gremlin.Net versione 3.2.7 manualmente, tramite Gestione pacchetti NuGet o l'[utilità della riga di comando NuGet](https://docs.microsoft.com/en-us/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net -Version 3.2.7
    ```

## <a name="review-the-code"></a>Esaminare il codice

Ecco una breve panoramica delle operazioni eseguire nell'app. Aprire il file Program.cs. Come si noterà, queste righe di codice creano le risorse di Azure Cosmos DB. 

* Impostare i parametri di connessione in base all'account creato in precedenza (riga 19): 

    ```csharp
    private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";
    private static int port = 443;
    private static string authKey = "your-authentication-key";
    private static string database = "your-database";
    private static string collection = "your-collection-or-graph";
    ```

* I comandi Gremlin da eseguire sono elencati in un dizionario (riga 26):

    ```csharp
    private static Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
    {
        { "Cleanup",        "g.V().drop()" },
        { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
        { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
        { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
        { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
        { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
        { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
        { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
        { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
        { "CountVertices",  "g.V().count()" },
        { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
        { "Project",        "g.V().hasLabel('person').values('firstName')" },
        { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
        { "Traverse",       "g.V('thomas').out('knows').hasLabel('person')" },
        { "Traverse 2x",    "g.V('thomas').out('knows').hasLabel('person').out('knows').hasLabel('person')" },
        { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
        { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
        { "CountEdges",     "g.E().count()" },
        { "DropVertex",     "g.V('thomas').drop()" },
    };
    ```


* Creare un oggetto connessione `GremlinServer` usando i parametri forniti in precedenza (riga 52):

    ```csharp
    var gremlinServer = new GremlinServer(hostname, port, enableSsl: true, 
                                                    username: "/dbs/" + database + "/colls/" + collection, 
                                                    password: authKey);
    ```

* Creare un nuovo oggetto `GremlinClient` (riga 56):

    ```csharp
    var gremlinClient = new GremlinClient(gremlinServer);
    ```

* Eseguire ogni query Gremlin usando l'oggetto `GremlinClient` con un'attività asincrona (riga 63). In questo modo, le query Gremlin verranno lette dal dizionario definito in precedenza (riga 26):

    ```csharp
    var task = gremlinClient.SubmitAsync<dynamic>(query.Value);
    task.Wait();
    ```

* Recuperare il risultato e leggere i valori, formattati come dizionario, usando la classe `JsonSerializer` da Newtonsoft.Json:

    ```csharp
    foreach (var result in task.Result)
    {
        // The vertex results are formed as dictionaries with a nested dictionary for their properties
        string output = JsonConvert.SerializeObject(result);
        Console.WriteLine(String.Format("\tResult:\n\t{0}", output));
    }
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **Chiavi**. 

    Copiare la prima parte del valore dell'URI.

    ![Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure](./media/create-graph-dotnet/keys.png)

2. In Program.cs incollare il valore sopra `your-endpoint` nella variabile `hostname` nella riga 19. 

    `"private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";`

    Il valore dell'endpoint dovrebbe ora essere simile all'output seguente:

    `"private static string hostname = "testgraphacct.gremlin.cosmosdb.azure.com";`

3. Copiare il valore di **CHIAVE PRIMARIA** dal portale e incollarlo nella variabile `authkey`, sostituendo il segnaposto `"your-authentication-key"` nella riga 21. 

    `private static string authKey = "your-authentication-key";`

4. Usando le informazioni del database creato in precedenza, incollare il nome del database nella variabile `database` nella riga 22. 

    `private static string database = "your-database";`

5. Analogamente, usando le informazioni della raccolta creata in precedenza, incollare il nome della raccolta, che corrisponde al nome del grafico, nella variabile `collection` nella riga 23. 

    `private static string collection = "your-collection-or-graph";`

6. Salvare il file Program.cs. 

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Eseguire l'app console

Premere CTRL + F5 per eseguire l'applicazione. L'applicazione stamperà i comandi della query Gremlin e i risultati nella console.

   La finestra della console visualizza i vertici e gli archi aggiunti al grafo. Al completamento dello script, premere INVIO per chiudere la finestra della console.

## <a name="browse-using-the-data-explorer"></a>Esplorare i dati con Esplora dati

È ora possibile tornare a Esplora dati nel portale di Azure per esplorare i nuovi dati del grafo ed eseguire query su di essi.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Graph. Espandere il database e i nodi della raccolta e quindi fare clic su **Grafico**.

2. Fare clic sul pulsante **Applica filtro** per usare la query predefinita per visualizzare tutti i vertici nel grafico. I dati generati dall'app di esempio vengono visualizzati nel riquadro Graphs (Grafi).

    È possibile ingrandire o ridurre il grafico, espanderne lo spazio di visualizzazione, aggiungere altri vertici e spostare i vertici nell'area di visualizzazione.

    ![Visualizzare il grafico in Esplora dati nel portale di Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi: 

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

