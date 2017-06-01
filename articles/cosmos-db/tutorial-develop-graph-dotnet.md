---
title: 'Azure Cosmos DB: Sviluppare con l&quot;API Graph in .NET | Documentazione Microsoft'
description: Informazioni su come sviluppare con l&quot;API di DocumentDB di Azure Cosmos DB usando .NET
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 94909fd1db426267eb60e5d7f4d753de82ca0377
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Sviluppare con l'API Graph in .NET
Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa esercitazione illustra come creare un account Azure Cosmos DB usando il portale di Azure e come creare un contenitore e un database di grafi. L'applicazione crea quindi una social network semplice con quattro utenti usando l'[API Graph](graph-sdk-dotnet.md) (anteprima), quindi attraversa ed esegue query sul grafo usando Gremlin.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un account Azure Cosmos DB 
> * Creare un contenitore e un database di grafi
> * Serializzare vertici e archi negli oggetti .NET
> * Aggiungere vertici e archi
> * Eseguire query sul grafo usando Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Grafi in Azure Cosmos DB
È possibile usare Azure Cosmos DB per creare, aggiornare ed eseguire query di grafi usando la libreria [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). La libreria Microsoft.Azure.Graph offre un metodo di estensione singolo `CreateGremlinQuery<T>` oltre alla classe `DocumentClient` per eseguire query Gremlin.

Il linguaggio di programmazione funzionale Gremlin supporta operazioni di scrittura (DML) e le operazioni di query e attraversamento. Questo articolo descrive alcuni esempi per iniziare a usare Gremlin. Vedere [Query di Gremlin](gremlin-support.md) per una procedura dettagliata delle funzionalità Gremlin disponibili in Azure Cosmos DB. 

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi di disporre di quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/). 
    * In alternativa, per questa esercitazione è possibile usare l'[emulatore DocumentDB di Azure](local-emulator.md).
* [Visual Studio](http://www.visualstudio.com/)

## <a name="create-database-account"></a>Creare un account di database

Si inizia creando un account Azure Cosmos DB nel portale di Azure.  

> [!TIP]
> * È stato già creato un account Azure Cosmos DB? In questo caso passare a [Configurare la soluzione di Visual Studio](#SetupVS)
> * Si dispone di un account Azure DocumentDB? In questo caso l'account è ora un account Azure Cosmos DB ed è possibile passare a [Configurare la soluzione di Visual Studio](#SetupVS).  
> * Se si usa l'emulatore Azure Cosmos DB, seguire i passaggi descritti nell'articolo [Azure Cosmos DB Emulator](local-emulator.md) (Emulatore Azure Cosmos DB) per configurare l'emulatore e proseguire con il passaggio [Configurare la soluzione di Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Configurare la soluzione di Visual Studio
1. Aprire **Visual Studio** nel computer.
2. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
3. Nella finestra di dialogo **Nuovo progetto** selezionare **Modelli** / **Visual C#** / **App console (.NET Framework)**, assegnare un nome al progetto e quindi fare clic su **OK**.
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla nuova applicazione console, disponibile nella soluzione di Visual Studio, quindi scegliere **Gestisci pacchetti NuGet**.
5. Nella scheda **NuGet** fare clic su **Sfoglia**e digitare **Microsoft.Azure.Graphs** nella casella di ricerca e selezionare **Includi versione preliminare**.
6. Nei risultati trovare **Microsoft.Azure.Graphs** e fare clic su **Installa**.
   
   Se viene visualizzato un messaggio sulla verifica delle modifiche alla soluzione, fare clic su **OK**. Se viene visualizzato un messaggio sull'accettazione della licenza, fare clic su **Accetto**.
   
    La libreria`Microsoft.Azure.Graphs` fornisce un metodo di estensione singolo `CreateGremlinQuery<T>` per l'esecuzione di operazioni Gremlin. Il linguaggio di programmazione funzionale Gremlin supporta operazioni di scrittura (DML) e le operazioni di query e attraversamento. Questo articolo descrive alcuni esempi per iniziare a usare Gremlin. [Query Gremlin](gremlin-support.md) include una procedura dettagliata delle funzionalità di Gremlin in Azure Cosmos DB.

## <a id="add-references"></a>Connessione dell'app

Aggiungere queste due costanti e la variabile *client* nell'applicazione. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Tornare al [portale di Azure](https://portal.azure.com) per recuperare la chiave primaria e l'URL dell'endpoint. L'URL e la chiave primaria dell'endpoint sono necessari all'applicazione per conoscere la destinazione della connessione e ad Azure Cosmos DB per considerare attendibile la connessione dell'applicazione. 

Nel portale di Azure passare all'account Azure Cosmos DB, fare clic su **Chiavi**, quindi su **Chiavi di lettura/scrittura**. 

Copiare l'URI dal portale e incollarlo su `Endpoint` nella proprietà dell'endpoint. Copiare quindi la CHIAVE PRIMARIA dal portale e incollarla nella proprietà `AuthKey` precedente. 

![Screenshot del portale di Azure usato nell'esercitazione per creare un'applicazione C#. Mostra un account Azure Cosmos DB, il pulsante CHIAVI evidenziato nel Pannello di navigazione Azure Cosmos DB e i valori URI e CHIAVE PRIMARIA evidenziati nel pannello chiavi] [keys] 
 
## <a id="instantiate"></a>Creare un'istanza di DocumentClient 
Creare quindi una nuova istanza di **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Creare un database 

A questo punto creare un [database](documentdb-resources.md#databases) di Azure Cosmos DB usando il metodo [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) o [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) della classe **DocumentClient** da [DocumentDB .NET SDK](documentdb-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Creare un grafo 

Creare quindi un contenitore di grafi usando il metodo [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) o [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) della classe **DocumentClient**. Una raccolta è un contenitore di entità di grafi. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serializzare vertici e archi negli oggetti .NET
Azure Cosmos DB usa il [formato wire GraphSON](gremlin-support.md), che definisce uno schema JSON per vertici, archi e proprietà. Azure Cosmos DB .NET SDK include JSON.NET come dipendenza e ciò consente di serializzare o deserializzare GraphSON negli oggetti .NET che è possibile usare nel codice.

Ad esempio, è possibile usare una social network semplice con quattro utenti. Verrà illustrato come creare vertici `Person`, aggiungere relazioni `Knows` tra i vertici, quindi eseguire query e attraversare il grafo per individuare le relazioni "amico di amico". 

Lo spazio dei nomi `Microsoft.Azure.Graphs.Elements` fornisce le classi `Vertex`, `Edge`, `Property` e `VertexProperty` per la deserializzazione delle risposte GraphSON a oggetti .NET ben definiti.

## <a name="run-gremlin-using-creategremlinquery"></a>Eseguire Gremlin usando CreateGremlinQuery
Gremlin, come SQL, supporta le operazioni di lettura, scrittura e le query. Ad esempio, il frammento seguente illustra come creare vertici e archi, eseguire alcune query di esempio usando `CreateGremlinQuery<T>` e iterare in modo asincrono questi risultati usando `ExecuteNextAsync` e 'HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
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
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Aggiungere vertici e archi

Verranno esaminati in maggiore dettaglio i comandi Gremlin illustrati nella sezione precedente. Prima si aggiungono vertici usando il metodo `addV` Gremlin. Ad esempio, il frammento seguente crea un vertice "Thomas Andersen" di tipo "Person", con proprietà per il nome, il cognome e l'età.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Creare quindi alcuni archi tra i vertici usando il metodo `addE` Gremlin. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

È possibile aggiornare un vertice esistente usando il comando `properties` Gremlin. Non verrà esaminata la chiamata per eseguire la query tramite `HasMoreResults` e `ExecuteNextAsync` negli altri esempi.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

È possibile trascinare gli archi e i vertici usando il comando `drop` Gremlin. Ecco un frammento di codice che illustra come eliminare un vertice e un arco. Si noti che l'eliminazione di un vertice implica l'eliminazione a catena degli archi associati.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Eseguire una query sul grafo

È possibile anche eseguire operazioni di query e attraversamento usando Gremlin. Ad esempio, il frammento seguente illustra come contare il numero di vertici nel grafo:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
È possibile eseguire filtri usando i comandi `has` e `hasLabel` Gremlin e combinarli usando `and`, `or` e `not` per creare filtri più complessi:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

È possibile proiettare determinate proprietà nei risultati della query usando il comando `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Finora sono stati esaminati solo gli operatori di query che è possibile usare in qualsiasi database. I grafi sono veloci ed efficienti per le operazioni di attraversamento quando è necessario passare agli archi e ai vertici correlati. Verranno ora individuati tutti gli amici di Thomas. Questa operazione viene eseguita usando il comando `outE` di Gremlin per individuare tutti gli archi in uscita da Thomas, quindi attraversando i vertici in ingresso da tali archi usando il comando `inV` di Gremlin:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

La query successiva esegue due passaggi per trovare tutti "gli amici di amici" di Thomas chiamando `outE` e `inV` due volte. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

È possibile creare query più complesse e implementare la potente logica di attraversamento di grafi usando Gremlin, incluse la combinazione di espressioni di filtro, l'esecuzione di cicli con il comando `loop` e l'implementazione dello spostamento condizionale usando il comando `choose`. Altre informazioni sulle operazioni che è possibile eseguire con il [supporto per Gremlin](gremlin-support.md).

L'esercitazione di Azure Cosmos DB è stata completata. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare a usare questa app, seguire questa procedura per eliminare tutte le risorse create da questa esercitazione nel portale di Azure.  

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Creazione di un account Azure Cosmos DB 
> * Creazione di un contenitore e un database di grafi
> * Serializzazione di vertici e archi negli oggetti .NET
> * Aggiunta di vertici e archi
> * Esecuzione di query sul grafo usando Gremlin

È ora possibile creare query più complesse e implementare la potente logica di attraversamento di grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

