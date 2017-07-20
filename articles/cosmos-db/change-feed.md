---
title: Uso del supporto del feed delle modifiche in Azure Cosmos DB | Microsoft Docs
description: Usare il supporto del feed delle modifiche di Azure Cosmos DB per tenere traccia delle modifiche nei documenti, eseguire elaborazioni basate su eventi come i trigger e mantenere aggiornati i sistemi di cache e analisi.
keywords: feed delle modifiche
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/23/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d04d1240fb353a973953b2a90eadc65705219edb
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Uso del supporto del feed delle modifiche in Azure Cosmos DB
[Azure Cosmos DB](../cosmos-db/introduction.md) è un servizio di database con replica a livello globale rapido e flessibile, usato per archiviare volumi elevati di dati transazionali e operativi, con una latenza stimabile in pochissimi millisecondi a cifra singola per le operazioni di lettura e scrittura. Tutto questo lo rende particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Un modello di progettazione comune in tali applicazione consta nel tenere traccia delle modifiche apportate ai dati di Azure Cosmos DB, aggiornare le viste materializzate, eseguire analisi in tempo reale, memorizzare i dati nell'archiviazione offline sicura e attivare notifiche su determinati eventi in base a tali modifiche. Il **supporto del feed delle modifiche** in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli.

Con il supporto al feed delle modifiche, Azure Cosmos DB offre un elenco di documenti all'interno di una raccolta di Azure Cosmos DB, nell'ordine in cui questi sono stati modificati. Il feed può essere usato per tenere traccia delle modifiche ai dati presenti nella raccolta e per eseguire operazioni come quelle seguenti:

* Attivare una chiamata a un'API quando un documento viene inserito o modificato
* Eseguire l'elaborazione in tempo reale (flusso) per gli aggiornamenti
* Sincronizzare i dati con una cache, un motore di ricerca o un data warehouse

Le modifiche in Azure Cosmos DB sono persistenti e possono essere elaborate in modo asincrono. Vengono inoltre distribuite a uno o più consumer per l'elaborazione parallela. Verranno ora esaminate le API per il feed delle modifiche e verrà illustrato come usarle per creare applicazioni scalabili in tempo reale. Questo articolo illustra come usare i dati spaziali con l'API DocumentDB di Azure Cosmos DB. 

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>Casi d'uso e scenari
Il feed delle modifiche consente di elaborare con efficienza set di dati di grandi dimensioni con volumi elevati di scritture e rappresenta un'alternativa alle query di interi set di dati per individuare le modifiche. Ad esempio, è possibile eseguire in modo efficiente le operazioni seguenti:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.
* Implementare la suddivisione in livelli e l'archiviazione a livello di applicazione, in altre parole archiviare i dati più usati ("hot data") in Azure Cosmos DB e trasferire quelli meno usati ("cold data") in [Archivio BLOB di Azure](../storage/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementare analisi in batch sui dati usando [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementare [pipeline lambda in Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB offre una soluzione di database scalabile che può gestire sia l'inserimento che le query e implementare architetture lambda con costo totale di proprietà ridotto. 
* Effettuare migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos DB con uno schema di partizionamento differente.

**Pipeline lambda con Azure Cosmos DB per l'inserimento e le query:**

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

È possibile usare Azure Cosmos DB per ricevere e archiviare i dati di eventi da dispositivi, sensori, infrastrutture e applicazioni, per poi elaborarli in tempo reale con [Analisi di flusso di Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Nelle app Web e per dispositivi mobili è possibile tenere traccia di modifiche come quelle apportate al profilo, alle preferenze o alle località dei clienti per attivare determinate azioni come l'invio di notifiche push ai loro dispositivi tramite [Funzioni di Azure](../azure-functions/functions-bindings-documentdb.md) o [Servizi app](https://azure.microsoft.com/services/app-service/). Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Funzionamento del feed delle modifiche in Azure Cosmos DB
Azure Cosmos DB offre la possibilità di leggere in modo incrementale gli aggiornamenti effettuati a una raccolta di Azure Cosmos DB. Questo feed di modifiche ha le seguenti proprietà:

* Le modifiche sono persistenti in Azure Cosmos DB e possono essere elaborate in modo asincrono.
* Le modifiche ai documenti all'interno di una raccolta sono immediatamente disponibili nel feed di modifiche.
* Ogni modifica apportata a un documento viene visualizzata solo una volta nel feed di modifiche. Solo la modifica più recente per un determinato documento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.
* Il feed delle modifiche è ordinato in base all'ordine di modifica in ciascun valore di chiave della partizione. Non esiste alcun ordine garantito tra i valori partition-key.
* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione fisso per cui sono disponibili le modifiche.
* Le modifiche sono disponibili in blocchi di intervalli di chiavi di partizione. Questa funzionalità consente di apportare modifiche da raccolte di grandi dimensioni per poi elaborarle in parallelo da più consumer/server.
* Le applicazioni possono richiedere più feed delle modifiche alla volta nella stessa raccolta.

Il feed delle modifiche di Azure Cosmos DB è abilitato in tutti gli account per impostazione predefinita e non comporta costi aggiuntivi. È possibile usare la [velocità effettiva con provisioning](request-units.md) nell'area di scrittura o in qualsiasi [area di lettura](distribute-data-globally.md) per leggere dal feed delle modifiche, proprio come ogni altra operazione da Azure Cosmos DB. Il feed delle modifiche include le operazioni di aggiunte e aggiornamenti eseguite sui documenti all'interno della raccolta. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno dei documenti al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza delimitato per i documenti tramite la [funzionalità TTL](time-to-live.md), ad esempio 24 ore, e usare il valore di tale proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL. Il feed delle modifiche è disponibile per ciascun intervallo di chiavi di partizioni all'interno nella raccolta dei documenti ed è pertanto possibile distribuirlo a uno o più consumer per l'elaborazione parallela. 

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

La sezione seguente descrive come accedere al feed delle modifiche con l'API REST e gli SDK di Azure Cosmos DB. Per le applicazioni .NET, è consigliabile usare la [libreria del processore del feed delle modifiche]() per elaborare gli eventi dal feed delle modifiche.

## <a id="rest-apis"></a>Uso dell'API REST e degli SDK
Azure Cosmos DB offre contenitori elastici di archiviazione e velocità effettiva chiamati **raccolte**. I dati nelle raccolte vengono raggruppati in modo logico tramite [chiavi di partizione](partition-data.md), per motivi di scalabilità e prestazioni. Azure Cosmos DB offre varie API per l'accesso ai dati, tra cui la ricerca per ID (Read/Get), le query e i feed di lettura (analisi). È possibile ottenere il feed delle modifiche popolando due nuove intestazioni di richiesta nell'API `ReadDocumentFeed` di DocumentDB. Il feed può essere elaborato in parallelo su più intervalli di chiavi di partizione.

### <a name="readdocumentfeed-api"></a>API ReadDocumentFeed
Esaminiamo brevemente il funzionamento di ReadDocumentFeed. Azure Cosmos DB supporta la lettura di un feed di documenti all'interno di una raccolta tramite l'API `ReadDocumentFeed`. La richiesta seguente restituisce ad esempio una pagina di documenti all'interno della raccolta `serverlogs`. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

È possibile limitare i risultati usando l'intestazione `x-ms-max-item-count` e riprendere le letture inviando nuovamente la richiesta con un'intestazione `x-ms-continuation` restituita nella risposta precedente. Quando eseguita da un singolo client, `ReadDocumentFeed` passa da un risultato all'altro sulle partizioni in modo seriale. 

**ReadDocumentFeed seriale**

È anche possibile recuperare il feed di documenti usando uno degli [SDK di Azure Cosmos DB](documentdb-sdk-dotnet.md) supportati. Ad esempio, il frammento seguente illustra come eseguire ReadDocumentFeed in .NET.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

### <a name="distributed-execution-of-readdocumentfeed"></a>Esecuzione distribuite di ReadDocumentFeed
Per le raccolte che contengono terabyte di dati o più o che ricevono volumi di aggiornamenti di grandi dimensioni, l'esecuzione seriale di un feed di lettura da una singola macchina client potrebbe non essere una soluzione pratica. Per supportare questi scenari di Big Data, Azure Cosmos DB offre API per distribuire in modo trasparente le chiamate `ReadDocumentFeed` su più lettori/consumer client. 

**ReadDocumentFeed distribuito**

Per offrire l'elaborazione scalabile delle modifiche incrementali, Azure Cosmos DB supporta un modello a scalabilità orizzontale per l'API del feed delle modifiche, basato sugli intervalli di chiavi di partizione.

* È possibile ottenere un elenco degli intervalli di chiavi di partizione per una raccolta tramite una chiamata `ReadPartitionKeyRanges`. 
* Per ogni intervallo di chiavi di partizioni, è possibile eseguire un'operazione `ReadDocumentFeed` per leggere i documenti con chiavi di partizione comprese in tale intervallo.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>Recupero degli intervalli di chiavi di partizione per una raccolta
È possibile recuperare gli intervalli di chiavi di partizione richiedendo la risorsa `pkranges` all'interno di una raccolta. Ad esempio, la richiesta seguente recupera l'elenco di intervalli di chiavi di partizione per la raccolta `serverlogs`:

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

Questa richiesta restituisce la risposta seguente che contiene i metadati sugli intervalli di chiavi di partizione:

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**Partition Key Range Properties** (Proprietà dell'intervallo di chiavi di partizione): ciascun intervallo di chiavi di partizione include le proprietà dei metadati nella tabella seguente:

<table>
    <tr>
        <th>Nome intestazione</th>
        <th>Descrizione</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>L'ID per l'intervallo di chiavi di partizione. Si tratta di un ID stabile e univoco in ciascuna raccolta.</p>
            <p>Deve essere usato nella chiamata seguente per leggere le modifiche in base all'intervallo di chiavi di partizione.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>Il valore hash di chiave di partizione massimo per l'intervallo di chiavi di partizione. Solo per uso interno.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>Il valore hash di chiave di partizione minimo per l'intervallo di chiavi di partizione. Solo per uso interno.</td>
    </tr>       
</table>

È possibile farlo usando uno degli [SDK di Azure Cosmos DB](documentdb-sdk-dotnet.md). Ad esempio, il frammento seguente illustra come recuperare intervalli di chiavi di partizione in .NET.

    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

Azure Cosmos DB supporta il recupero di documenti per ogni intervallo di chiavi di partizione tramite l'impostazione dell'intestazione `x-ms-documentdb-partitionkeyrangeid` facoltativa. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Esecuzione di un ReadDocumentFeed incrementale
ReadDocumentFeed supporta i seguenti scenari/attività per l'elaborazione incrementale di modifiche apportate alle raccolte di Azure Cosmos DB:

* Lettura di tutte le modifiche ai documenti fin dall'inizio, ovvero dalla creazione della raccolta.
* Lettura di tutte le modifiche agli aggiornamenti futuri eseguiti per i documenti a partire dalla data attuale.
* Lettura di tutte le modifiche ai documenti da una versione logica della raccolta (ETag). È possibile creare checkpoint dei consumer in base all'ETag restituito dalle richieste read-feed incrementali.

Le modifiche includono aggiunte e aggiornamenti ai documenti. Per acquisire le eliminazioni, è necessario usare una proprietà "eliminazione temporanea" all'interno di documenti oppure usare la [proprietà TTL integrata](time-to-live.md) per segnalare un'eliminazione in sospeso nel feed delle modifiche.

Nella tabella seguente vengono elencate le intestazioni di richieste e risposte per le operazioni ReadDocumentFeed.

**Intestazioni delle richieste per ReadDocumentFeed incrementale**:

<table>
    <tr>
        <th>Nome intestazione</th>
        <th>Descrizione</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>Deve essere impostata su "Incremental feed" (Feed incrementale) oppure omessa</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>Nessuna intestazione: restituisce tutte le modifiche fin dall'inizio (creazione della raccolta)</p>
            <p>"*": restituisce tutte le nuove modifiche ai dati all'interno della raccolta</p>
            <p>&lt;etag&gt;: se impostato su un ETag di raccolta, restituisce tutte le modifiche effettuate da quel timestamp logico</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>L'ID dell'intervallo di chiavi di partizione per la lettura dei dati.</td>
    </tr>
</table>

**Intestazioni delle risposte per ReadDocumentFeed incrementale**:

<table>
    <tr>
        <th>Nome intestazione</th>
        <th>Descrizione</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>Il numero di sequenza logica (LSN) dell'ultimo documento restituito nella risposta.</p>
            <p>Un ReadDocumentFeed incrementale può essere ripreso inviando nuovamente questo valore in If-None-Match.</p>
        </td>
    </tr>
</table>

Ecco una richiesta di esempio per restituire tutte le modifiche incrementali nella raccolta dalla versione logica/ETag `28535` con intervallo di chiavi di partizione = `16`:

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Le modifiche vengono ordinate in base all'ora in ciascun valore di chiave di partizione all'interno dell'intervallo. Non esiste alcun ordine garantito tra i valori partition-key. Se sono presenti più risultati di quanti sia possibile visualizzare in una singola pagina, è possibile leggere la pagina successiva inviando nuovamente la richiesta con l'intestazione `If-None-Match` avente un valore uguale a `etag` della risposta precedente. Se sono stati inseriti o aggiornati più documenti in modo transazionale in una procedura o in un trigger archiviati, questi verranno restituiti in un'unica pagina di risposta.

> [!NOTE]
> Il feed delle modifiche può consentire di ottenere un numero maggiore di elementi in una pagina rispetto a quanto specificato in `x-ms-max-item-count` in caso di inserimento o aggiornamento di più documenti in stored procedure o trigger. 

.NET SDK fornisce le classi helper [CreateDocumentChangeFeedQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) e [ChangeFeedOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.changefeedoptions.aspx) per l'accesso alle modifiche apportate a una raccolta. Il frammento seguente mostra come recuperare tutte le modifiche dall'inizio usando l'SDK di .NET da un singolo client.

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        string pkRangesResponseContinuation = null;
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

        do
        {
            FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
                collectionUri, 
                new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

            partitionKeyRanges.AddRange(pkRangesResponse);
            pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
        }
        while (pkRangesResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

Il frammento seguente, invece, mostra come elaborare le modifiche in tempo reale con Azure Cosmos DB usando il supporto al feed delle modifiche e la funzione precedente. La prima chiamata restituisce tutti i documenti nella raccolta e la seconda restituisce solo i due documenti creati dall'ultimo checkpoint.

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


È inoltre possibile filtrare il feed delle modifiche usando la logica lato client per elaborare eventi specifici. Ad esempio, ecco un frammento di codice che usa LINQ lato client per elaborare solo gli eventi di modifica della temperatura dai sensori del dispositivo.

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

## <a id="change-feed-processor"></a>Libreria del processore dei feed delle modifiche
La [libreria del processore del feed delle modifiche di Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor) consente di distribuire l'elaborazione degli eventi dal feed delle modifiche in più consumer. È consigliabile usare questa implementazione durante la creazione di lettori del feed delle modifiche sulla piattaforma .NET. La classe `ChangeFeedProcessorHost` fornisce un ambiente di runtime thread-safe, multiprocesso e sicuro per le implementazioni del processore di eventi che consente inoltre di gestire lease di checkpoint e di partizione.

Per usare la classe [`ChangeFeedProcessorHost`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/ChangeFeedEventHost.cs), è possibile implementare [`IChangeFeedObserver`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/IChangeFeedObserver.cs). Questa interfaccia contiene tre metodi:

* OpenAsync
* CloseAsync
* ProcessEventsAsync

Per avviare l'elaborazione di eventi, creare un'istanza di ChangeFeedProcessorHost, indicando i parametri appropriati per la raccolta di Azure Cosmos DB. Chiamare quindi `RegisterObserverAsync` per registrare l'implementazione di `IChangeFeedObserver` con il runtime. A questo punto, l'host prova ad acquisire un lease per ogni intervallo di chiavi di partizione nella raccolta di Azure Cosmos DB usando un algoritmo greedy. Tali lease dureranno per un determinato intervallo di tempo e quindi devono essere rinnovati. Appena nuovi nodi, in questo caso istanze di lavoro, passano online, inviano prenotazioni di lease e nel tempo il carico passa tra i nodi man mano che ognuno tenta di acquisire più lease.

![Uso dell'host del processore del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedprocessor.png)

Con il passare del tempo, viene stabilito un equilibrio. Questa funzionalità dinamica consente il ridimensionamento automatico basato su CPU da applicare ai consumer per la scalabilità verticale e orizzontale. Se le modifiche sono disponibili in Azure Cosmos DB con velocità maggiore rispetto ai tempi di elaborazione dei consumer, è possibile usare l'aumento di CPU per i consumer per realizzare una scalabilità automatica sul numero di istanze del ruolo di lavoro.

La classe `ChangeFeedProcessorHost` implementa anche un meccanismo di checkpoint con una raccolta di lease Azure Cosmos DB separata. Questo meccanismo archivia l'offset per ogni partizione, in modo che ogni consumer possa determinare qual è stato l'ultimo checkpoint per il consumer precedente. Man mano che le partizioni passano tra i nodi tramite lease, questo è il meccanismo di sincronizzazione che semplifica lo spostamento del carico.


Di seguito è riportato un frammento di codice per un semplice host di processore feed delle modifiche che stampa le modifiche alla console:

```cs
    class DocumentFeedObserver : IChangeFeedObserver
    {
        private static int s_totalDocs = 0;
        public Task OpenAsync(ChangeFeedObserverContext context)
        {
            Console.WriteLine("Worker opened, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;  // Requires targeting .NET 4.6+.
        }
        public Task CloseAsync(ChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.WriteLine("Worker closed, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }
        public Task ProcessEventsAsync(IReadOnlyList<Document> docs, ChangeFeedObserverContext context)
        {
            Console.WriteLine("Change feed: total {0} doc(s)", Interlocked.Add(ref s_totalDocs, docs.Count));
            return Task.CompletedTask;
        }
    }
```

Il frammento di codice seguente illustra come registrare un nuovo host per l'ascolto delle modifiche da una raccolta di Azure Cosmos DB. In questo caso, si configura una raccolta separata per gestire i lease per le partizioni tra più consumer:

```cs
    string hostName = Guid.NewGuid().ToString();
    DocumentCollectionInfo documentCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "documents"
    };

    DocumentCollectionInfo leaseCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "leases"
    };

    ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation);
    await host.RegisterObserverAsync<DocumentFeedObserver>();
```

Questo articolo illustra una procedura dettagliata sul supporto del feed delle modifiche di Azure Cosmos DB e su come tenere traccia delle modifiche apportate ai dati di Azure Cosmos DB usando l'API REST e/o gli SDK. 

## <a name="next-steps"></a>Passaggi successivi
* Provare gli [esempi di codice relativi al feed delle modifiche di Azure Cosmos DB in GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* Introduzione alla programmazione con gli [SDK](documentdb-sdk-dotnet.md) o l'[API REST](/rest/api/documentdb/) di Azure Cosmos DB

