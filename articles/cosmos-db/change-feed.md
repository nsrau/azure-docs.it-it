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
ms.date: 08/15/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 160fbc98e0f3dcc7d17cbe0c7f7425811596a896
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Uso del supporto del feed delle modifiche in Azure Cosmos DB
[Azure Cosmos DB](../cosmos-db/introduction.md) è un servizio di database con replica a livello globale rapido e flessibile, usato per archiviare volumi elevati di dati transazionali e operativi, con una latenza stimabile in pochissimi millisecondi a cifra singola per le operazioni di lettura e scrittura. Tutto questo lo rende particolarmente adatto per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Un modello di progettazione comune in tali applicazione consta nel tenere traccia delle modifiche apportate ai dati di Azure Cosmos DB, aggiornare le viste materializzate, eseguire analisi in tempo reale, memorizzare i dati nell'archiviazione offline sicura e attivare notifiche su determinati eventi in base a tali modifiche. Il **supporto del feed delle modifiche** in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli.

Con il supporto al feed delle modifiche, Azure Cosmos DB offre un elenco di documenti all'interno di una raccolta di Azure Cosmos DB, nell'ordine in cui questi sono stati modificati. Il feed può essere usato per tenere traccia delle modifiche ai dati presenti nella raccolta e per eseguire operazioni come quelle seguenti:

* Attivare una chiamata a un'API quando un documento viene inserito o modificato
* Eseguire l'elaborazione in tempo reale (flusso) per gli aggiornamenti
* Sincronizzare i dati con una cache, un motore di ricerca o un data warehouse

Le modifiche in Azure Cosmos DB sono persistenti e possono essere elaborate in modo asincrono. Vengono inoltre distribuite a uno o più consumer per l'elaborazione parallela. Verranno ora esaminate le API per il feed delle modifiche e verrà illustrato come usarle per creare applicazioni scalabili in tempo reale. Questo articolo mostra come usare il feed di modifiche di Azure Cosmos DB e l'API DocumentDB. 

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Il supporto per il feed di modifiche è attualmente disponibile solo per l'API DocumentDB. Non sono attualmente supportate l'API Graph e l'API di tabella.

## <a name="use-cases-and-scenarios"></a>Casi d'uso e scenari
Il feed delle modifiche consente di elaborare con efficienza set di dati di grandi dimensioni con volumi elevati di scritture e rappresenta un'alternativa alle query di interi set di dati per individuare le modifiche. Ad esempio, è possibile eseguire in modo efficiente le operazioni seguenti:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.
* Implementare la suddivisione in livelli e l'archiviazione a livello di applicazione, in altre parole archiviare i dati più usati ("hot data") in Azure Cosmos DB e trasferire quelli meno usati ("cold data") in [Archivio BLOB di Azure](../storage/common/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementare analisi in batch sui dati usando [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementare [pipeline lambda in Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB offre una soluzione di database scalabile che può gestire sia l'inserimento che le query e implementare architetture lambda con costo totale di proprietà ridotto. 
* Effettuare migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos DB con uno schema di partizionamento differente.

**Pipeline lambda con Azure Cosmos DB per l'inserimento e le query:**

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

È possibile usare Azure Cosmos DB per ricevere e archiviare i dati di eventi da dispositivi, sensori, infrastrutture e applicazioni, per poi elaborarli in tempo reale con [Analisi di flusso di Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Nelle app Web e per dispositivi mobili [senza server](http://azure.com/serverless) è possibile tenere traccia di eventi come le modifiche al profilo, alle preferenze o alle località dei clienti per attivare determinate azioni come l'invio di notifiche push ai lori dispositivi tramite [Funzioni di Azure](../azure-functions/functions-bindings-documentdb.md) o [Servizi app](https://azure.microsoft.com/services/app-service/). Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Funzionamento del feed delle modifiche in Azure Cosmos DB
Azure Cosmos DB offre la possibilità di leggere in modo incrementale gli aggiornamenti effettuati a una raccolta di Azure Cosmos DB. Questo feed di modifiche ha le seguenti proprietà:

* Le modifiche sono persistenti in Azure Cosmos DB e possono essere elaborate in modo asincrono.
* Le modifiche ai documenti all'interno di una raccolta sono immediatamente disponibili nel feed di modifiche.
* Ogni modifica apportata a un documento viene visualizzata una sola volta nel feed delle modifiche e i client gestiscono la logica di checkpoint. La libreria del processore del feed delle modifiche fornisce funzionalità di checkpoint automatici e semantica di tipo "at least once".
* Solo la modifica più recente per un determinato documento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.
* Il feed delle modifiche è ordinato in base all'ordine di modifica in ciascun valore di chiave della partizione. Non esiste alcun ordine garantito tra i valori partition-key.
* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione fisso per cui sono disponibili le modifiche.
* Le modifiche sono disponibili in blocchi di intervalli di chiavi di partizione. Questa funzionalità consente di apportare modifiche da raccolte di grandi dimensioni per poi elaborarle in parallelo da più consumer/server.
* Le applicazioni possono richiedere più feed delle modifiche alla volta nella stessa raccolta.

Il feed di modifiche di Azure Cosmos DB è abilitato per impostazione predefinita per tutti gli account. È possibile usare la [velocità effettiva con provisioning](request-units.md) nell'area di scrittura o in qualsiasi [area di lettura](distribute-data-globally.md) per leggere dal feed delle modifiche, proprio come ogni altra operazione da Azure Cosmos DB. Il feed delle modifiche include le operazioni di aggiunte e aggiornamenti eseguite sui documenti all'interno della raccolta. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno dei documenti al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza delimitato per i documenti tramite la [funzionalità TTL](time-to-live.md), ad esempio 24 ore, e usare il valore di tale proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL. Il feed delle modifiche è disponibile per ciascun intervallo di chiavi di partizioni all'interno nella raccolta dei documenti ed è pertanto possibile distribuirlo a uno o più consumer per l'elaborazione parallela. 

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Sono disponibili alcune opzioni per l'implementazione di un feed di modifiche nel codice client. Le sezioni immediatamente successive illustrano come implementare il feed di modifiche tramite l'API REST di Azure Cosmos DB e DocumentDB SDK. Per le applicazioni .NET è tuttavia consigliabile usare la nuova [Libreria del processore di feed di modifiche](#change-feed-processor) per l'elaborazione di eventi dal feed di modifiche, poiché semplifica la lettura delle modifiche tra le partizioni e abilita l'esecuzione in parallelo di più thread. 

## <a id="rest-apis"></a>Uso dell'API REST e di DocumentDB SDK
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

**Feed di documenti con lettura seriale**

È anche possibile recuperare il feed di documenti usando uno degli [SDK di Azure Cosmos DB](documentdb-sdk-dotnet.md) supportati. Il frammento di codice seguente mostra ad esempio come usare il [metodo ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet) in .NET.

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

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


**Partition Key Range Properties** (Proprietà dell'intervallo di chiavi di partizione): ogni intervallo di chiavi di partizione include le proprietà dei metadati nella tabella seguente:

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

È possibile farlo usando uno degli [SDK di Azure Cosmos DB](documentdb-sdk-dotnet.md). Ad esempio, il frammento seguente illustra come recuperare intervalli di chiavi di partizione in .NET usando il metodo [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet).

```csharp
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
```

Azure Cosmos DB supporta il recupero di documenti per ogni intervallo di chiavi di partizione tramite l'impostazione dell'intestazione `x-ms-documentdb-partitionkeyrangeid` facoltativa. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Esecuzione di un ReadDocumentFeed incrementale
ReadDocumentFeed supporta i seguenti scenari/attività per l'elaborazione incrementale di modifiche apportate alle raccolte di Azure Cosmos DB:

* Lettura di tutte le modifiche ai documenti fin dall'inizio, ovvero dalla creazione della raccolta.
* Lettura di tutte le modifiche agli aggiornamenti futuri dei documenti a partire dal momento corrente oppure tutte le modifiche da un momento specificato dall'utente.
* Lettura di tutte le modifiche ai documenti da una versione logica della raccolta (ETag). È possibile creare checkpoint dei consumer in base all'ETag restituito dalle richieste read-feed incrementali.

Le modifiche includono aggiunte e aggiornamenti ai documenti. Per acquisire le eliminazioni, è necessario usare una proprietà "eliminazione temporanea" all'interno di documenti oppure usare la [proprietà TTL integrata](time-to-live.md) per segnalare un'eliminazione in sospeso nel feed delle modifiche.

La tabella seguente elenca la [richiesta](/rest/api/documentdb/common-documentdb-rest-request-headers.md) e le [intestazioni delle risposte](/rest/api/documentdb/common-documentdb-rest-response-headers.md) per operazioni ReadDocumentFeed.

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
        <td>If-Modified-Since</td> 
        <td>Formato di ora RFC 1123. Ignorata se viene specificata l'intestazione If-None-Match</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>L'ID dell'intervallo di chiavi di partizione per la lettura dei dati.</td>
    </tr>
</table>

**Intestazioni delle risposte per ReadDocumentFeed incrementale**:

<table> <tr>
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

Quando si usa .NET SDK (1.17.0), impostare il campo `StartTime` in `ChangeFeedOptions` per restituire direttamente i documenti modificati dal momento indicato da `StartTime` quando si chiama `CreateDocumentChangeFeedQuery`. Specificando `If-Modified-Since` usando l'API REST, la richiesta restituirà non i documenti stessi, ma il token di continuazione o `etag` nell'intestazione della risposta. Per restituire i documenti modificati nel periodo specificato, è necessario usare il token di continuazione `etag` nella richiesta successiva con `If-None-Match` per restituire i documenti effettivi. 

.NET SDK fornisce le classi helper [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) e [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) per l'accesso alle modifiche apportate a una raccolta. Il frammento seguente mostra come recuperare tutte le modifiche dall'inizio usando l'SDK di .NET da un singolo client.

```csharp
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
```
Il frammento seguente, invece, mostra come elaborare le modifiche in tempo reale con Azure Cosmos DB usando il supporto al feed delle modifiche e la funzione precedente. La prima chiamata restituisce tutti i documenti nella raccolta e la seconda restituisce solo i due documenti creati dall'ultimo checkpoint.

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

È inoltre possibile filtrare il feed delle modifiche usando la logica lato client per elaborare eventi specifici. Ad esempio, ecco un frammento di codice che usa LINQ lato client per elaborare solo gli eventi di modifica della temperatura dai sensori del dispositivo.

```csharp
FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>Libreria del processore dei feed delle modifiche
Un'altra opzione consiste nell'usare la [libreria del processore dei feed delle modifiche di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed), che può semplificare la distribuzione dell'elaborazione di eventi da un feed di modifiche in più consumer. La libreria è ottimale per la creazione di lettori di feed di modifiche nella piattaforma .NET. Di seguito sono elencati alcuni flussi di lavoro che risulterebbero semplificati tramite l'uso della libreria del processore dei feed delle modifiche rispetto ai metodi inclusi in altri Cosmos DB SDK: 

* Pull degli aggiornamenti dal feed di modifiche quando i dati vengono archiviati in più partizioni
* Spostamento o replica di dati da una raccolta a un'altra
* Esecuzione parallela di azioni attivate da aggiornamenti ai dati e ai feed di modifiche 

Benché l'uso delle API in Cosmos SDK fornisca un accesso preciso agli aggiornamenti dei feed di modifiche in ogni partizione, l'uso della libreria del processore dei feed delle modifiche semplifica la lettura delle modifiche tra le partizioni e in più thread in esecuzione parallela. Invece di leggere manualmente le modifiche da ogni contenitore e salvare un token di continuazione per ogni partizione, il processore dei feed di modifiche gestisce automaticamente la lettura delle modifiche nelle partizioni tramite un meccanismo di lease.

La libreria è disponibile come pacchetto NuGet, [Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/), e dal codice sorgente come [esempio](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor) di GitHub. 

### <a name="understanding-change-feed-processor-library"></a>Informazioni sulla libreria del processore dei feed delle modifiche 

L'implementazione del processore dei feed di modifiche prevede quattro componenti principali, ovvero la raccolta monitorata, la raccolta di lease, l'host del processore e i consumer. 

**Raccolta monitorata:** la raccolta monitorata include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e modifiche alla raccolta monitorata vengono riflessi nel feed di modifiche della raccolta. 

**Raccolta di lease:** la raccolta di lease coordina l'elaborazione dei feed di modifiche in più processi di lavoro. Una raccolta separata viene usata per archiviare i lease con un lease per partizione. È consigliabile archiviare questa raccolta di lease in un account diverso, con un'area di scrittura più vicina alla posizione in cui è in esecuzione il processore dei feed di modifiche. Un oggetto lease contiene gli attributi seguenti: 
* Owner: specifica l'host proprietario del lease.
* Continuation: specifica la posizione (token di continuazione) nel feed di modifiche per una partizione specifica.
* Timestamp: data dell'ultimo aggiornamento del lease. Il timestamp può essere usato per verificare se il lease viene considerato scaduto. 

**Host del processore:** ogni host determina il numero di partizioni da elaborare in base al numero di istanze di host con lease attivi. 
1.  Quando un host viene avviato, acquisisce lease per bilanciare il carico di lavoro in tutti gli host. Un host rinnova periodicamente i lease, in modo che i lease rimangano attivi. 
2.  Un host imposta come checkpoint il token di continuazione più recente sul rispettivo lease per ogni lettura. Per assicurare la sicurezza della concorrenza, un host verifica il valore ETag per ogni aggiornamento di lease. Sono supportate anche altre strategie per i checkpoint.  
3.  All'arresto, un host rilascia tutti i lease ma mantiene le informazioni sulla continuazione, in modo che sia possibile riprendere la lettura dal checkpoint archiviato in un secondo momento. 

Il numero di host non può attualmente essere superiore al numero di partizioni (lease).

**Consumer:** i consumer, o processi di lavoro, sono thread che eseguono l'elaborazione del feed di modifiche avviata da ogni host. Ogni host di processori può includere più consumer. Ogni consumer legge il feed di modifiche dalla partizione a cui è assegnato e invia notifiche al rispettivo host in caso di modifiche e di lease scaduti.

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. La raccolta monitorata archivia i documenti e usa il valore "city" come chiave di partizione. Come si può notare, la partizione blu contiene i documenti con i campo "city" compreso tra "A-E" e così via. Sono disponibili due host, ognuno con due consumer che leggono dalle quattro partizioni in parallelo. Le frecce mostrano i consumer che leggono da un punto specifico nel feed di modifiche. Nella prima partizione il colore blu scuro rappresenta le modifiche non lette, mentre il colore blu chiaro rappresenta le modifiche già lette nel feed di modifiche. Gli host usano la raccolta di lease per archiviare un valore di tipo "continuation" per tenere traccia della posizione di lettura corrente per ogni consumer. 

![Uso dell'host del processore del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>Uso della libreria del processore dei feed delle modifiche 
La sezione seguente illustra come usare la libreria del processore dei feed delle modifiche nel contesto della replica delle modifiche da una raccolta di origine a una raccolta di destinazione. In questo caso la raccolta di origine è la raccolta monitorata nel processore dei feed di modifiche. 

**Installare e includere il pacchetto NuGet del processore dei feed di modifiche** 

Prima di installare il pacchetto NuGet del processore dei feed di modifiche, è necessario installare quanto segue: 
* Microsoft.Azure.DocumentDB, versione 1.13.1 o successiva 
* Newtonsoft.Json, versione 9.0.1 o successiva. Installare `Microsoft.Azure.DocumentDB.ChangeFeedProcessor` e includerlo come riferimento.

**Creare una raccolta monitorata, di lease e di destinazione** 

Per usare la libreria del processore dei feed delle modifiche, è necessario che la raccolta di lease venga creata prima dell'esecuzione degli host dei processori. Anche in questo caso, è consigliabile archiviare questa raccolta di lease in un account diverso, con un'area di scrittura più vicina alla posizione in cui è in esecuzione il processore dei feed di modifiche. In questo esempio di spostamento dei dati è necessario creare la raccolta di destinazione prima di eseguire l'host del processore dei feed di modifiche. Nel codice di esempio viene chiamato un metodo helper per creare la raccolta monitorata, la raccolta di lease e la raccolta di destinazione, se non esistono già. 

> [!WARNING]
> La creazione di una raccolta ha implicazioni a livello di prezzi, poiché si sta riservando velocità effettiva per l'applicazione per comunicare con Azure Cosmos DB. Per altre informazioni, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

*Creazione di un host di processori*

La classe `ChangeFeedProcessorHost` fornisce un ambiente di runtime thread-safe, multiprocesso e sicuro per le implementazioni del processore di eventi che consente inoltre di gestire lease di checkpoint e di partizione. Per usare la classe `ChangeFeedProcessorHost` è possibile implementare `IChangeFeedObserver`. Questa interfaccia contiene tre metodi:

* `OpenAsync`: questa funzione viene chiamata quando viene aperto l'osservatore del feed di modifiche. È possibile modificarla in modo che esegua un'azione specifica all'apertura di un consumer/osservatore.  
* `CloseAsync`: questa funzione viene chiamata quando viene arrestato l'osservatore del feed di modifiche. È possibile modificarla in modo che esegua un'azione specifica alla chiusura di un consumer/osservatore.  
* `ProcessChangesAsync`: questa funzione viene chiamata quando nel feed di modifiche sono disponibili nuove modifiche al documento. È possibile modificarla in modo che esegua un'azione specifica in corrispondenza di ogni aggiornamento al feed di modifiche.  

In questo esempio l'interfaccia `IChangeFeedObserver` viene implementata tramite la classe `DocumentFeedObserver`. In questo caso la funzione `ProcessChangesAsync` esegue l'upsert (aggiornamento) di un documento dal feed di modifiche nella raccolta di destinazione. Questo esempio risulta utile per lo spostamento di dati da una raccolta a un'altra in modo da modificare la chiave di partizione di un set di dati. 

*Esecuzione dell'host di processori*

Prima di avviare l'elaborazione degli eventi, è possibile personalizzare le opzioni relative al feed di modifiche e all'host del feed di modifiche. 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
Nelle tabelle seguenti sono riepilogati i campi specifici che è possibile personalizzare. 

**Opzioni del feed di modifiche**:
<table>
    <tr>
        <th>Nome proprietà</th>
        <th>Descrizione</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>Ottiene o imposta il numero massimo di elementi da restituire nell'operazione di enumerazione nel servizio di database Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>Ottiene o imposta l'ID di intervallo di chiavi di partizioni per la richiesta corrente nel servizio di database Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>Ottiene o imposta il token di continuazione della richiesta nel servizio di database Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>Ottiene o imposta il token di sessione da usare con la coerenza di sessione nel servizio di database Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>Ottiene o imposta informazioni che indicano se il feed di modifiche nel servizio di database Azure Cosmos DB deve partire dall'inizio (true) o dalla posizione corrente (false). Per impostazione predefinita, inizia dalla posizione corrente (false).</td>
    </tr>
</table>

**Opzioni dell'host di feed di modifiche**:
<table>
    <tr>
        <th>Nome proprietà</th>
        <th>Tipo</th>
        <th>Descrizione</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>TimeSpan</td>
        <td>Intervallo per tutti i lease per le partizioni attualmente disponibili nell'istanza ChangeFeedEventHost.</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>TimeSpan</td>
        <td>Intervallo per l'avvio di un'attività che consente di calcolare se le partizioni sono distribuite equamente tra le istanze di host note.</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>TimeSpan</td>
        <td>Intervallo che indica la durata di un lease su un lease che rappresenta una partizione. Se il lease non viene rinnovato entro questo intervallo, risulta scaduto e la proprietà della partizione passa a un'altra istanza di ChangeFeedEventHost.</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>TimeSpan</td>
        <td>Ritardo tra il polling di una partizione alla ricerca di nuove modifiche al feed, dopo la rimozione di tutte le modifiche correnti.</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>Frequenza di applicazione di checkpoint ai lease.</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>Numero minimo di partizioni per l'host.</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>Numero massimo di partizioni che possono essere gestite dall'host.</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Booleano</td>
        <td>Indica se all'avvio dell'host è necessario eliminare tutti i lease esistenti e se l'host deve iniziare da zero.</td>
    </tr>
</table>


Per avviare l'elaborazione di eventi, creare un'istanza di `ChangeFeedProcessorHost`, indicando i parametri appropriati per la raccolta di Azure Cosmos DB. Chiamare quindi `RegisterObserverAsync` per registrare l'implementazione di `IChangeFeedObserver` (DocumentFeedObserver in questo esempio) nel runtime. A questo punto, l'host prova ad acquisire un lease per ogni intervallo di chiavi di partizione nella raccolta di Azure Cosmos DB usando un algoritmo greedy. Tali lease durano per un determinato intervallo di tempo e quindi devono essere rinnovati. Appena nuovi nodi, in questo caso istanze di lavoro, passano online, inviano prenotazioni di lease e nel tempo il carico passa tra i nodi man mano che ogni host prova ad acquisire più lease. 

Nel codice di esempio viene usata una classe factory (DocumentFeedObserverFactory.cs) per creare un osservatore e quindi viene usato `RegistObserverFactoryAsync` per registrare l'osservatore. 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
Con il passare del tempo, viene stabilito un equilibrio. Questa funzionalità dinamica consente il ridimensionamento automatico basato su CPU da applicare ai consumer per la scalabilità verticale e orizzontale. Se le modifiche sono disponibili in Azure Cosmos DB con velocità maggiore rispetto ai tempi di elaborazione dei consumer, è possibile usare l'aumento di CPU per i consumer per realizzare una scalabilità automatica sul numero di istanze del ruolo di lavoro.

## <a name="next-steps"></a>Passaggi successivi
* Provare gli [esempi di codice relativi al feed delle modifiche di Azure Cosmos DB in GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* Introduzione alla scrittura di codice con [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) o l'[API REST](/rest/api/documentdb/).

