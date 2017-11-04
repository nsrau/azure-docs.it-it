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
ms.devlang: 
ms.topic: article
ms.date: 10/10/2017
ms.author: arramac
ms.openlocfilehash: 0971959fb168d92096531d1c081666cf301608cf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Uso del supporto del feed delle modifiche in Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) è un database con replica a livello globale rapido e flessibile, ideale per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per avviare azioni aggiuntive. Queste azioni aggiuntive possono essere una delle seguenti: 

* Attivazione di una notifica o di una chiamata a un'API quando un documento viene inserito o modificato.
* Elaborazione di flussi per IoT o esecuzione di analisi.
* Spostamento dei dati aggiuntivi tramite sincronizzazione con una cache, un motore di ricerca o un data warehouse oppure tramite archiviazione dei dati nella risorsa di archiviazione offline sicura.

Il **supporto del feed di modifiche** in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Il supporto del feed di modifiche viene fornito per tutti i modelli di dati e contenitori in Azure Cosmos DB. Il feed di modifiche tuttavia viene letto usando il client DocumentDB e serializza gli elementi in formato JSON. A causa della formattazione JSON, nei client MongoDB i documenti in formato JSON non corrisponderanno al feed di modifiche in formato JSON. 

## <a name="how-does-change-feed-work"></a>Funzionamento del feed di modifiche

Il supporto del feed di modifiche in Azure Cosmos DB è in ascolto di eventuali modifiche in una raccolta di Azure Cosmos DB. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Le modifiche sono persistenti, possono essere elaborate in modo asincrono e incrementale e l'output può essere distribuito a uno o più consumer per l'elaborazione parallela. 

È possibile leggere il feed di modifiche in tre modi diversi, come verrà illustrato più avanti in questo articolo:

1.  [Uso di Funzioni di Azure](#azure-functions)
2.  [Uso dell'SDK di Azure Cosmos DB](#rest-apis)
3.  [Uso della libreria del processore dei feed delle modifiche di Azure Cosmos DB](#change-feed-processor)

Il feed di modifiche è disponibile per ogni intervallo di chiavi di partizioni nella raccolta dei documenti ed è quindi possibile distribuirlo a uno o più consumer per l'elaborazione parallela, come illustrato nell'immagine seguente.

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Informazioni aggiuntive:
* Il feed di modifiche è abilitato per impostazione predefinita per tutti gli account.
* È possibile usare la [velocità effettiva con provisioning](request-units.md) nell'area di scrittura o in qualsiasi [area di lettura](distribute-data-globally.md) per leggere dal feed di modifiche, proprio come ogni altra operazione di Azure Cosmos DB.
* Il feed delle modifiche include le operazioni di aggiunte e aggiornamenti eseguite sui documenti all'interno della raccolta. È possibile acquisire le eliminazioni impostando un flag "eliminazione temporanea" all'interno dei documenti al posto delle eliminazioni. In alternativa, è possibile impostare un periodo di scadenza delimitato per i documenti tramite la [funzionalità TTL](time-to-live.md), ad esempio 24 ore, e usare il valore di tale proprietà per acquisire le eliminazioni. Con questa soluzione è necessario elaborare le modifiche in un intervallo di tempo minore rispetto al periodo di scadenza TTL.
* Ogni modifica apportata a un documento viene visualizzata una sola volta nel feed delle modifiche e i client gestiscono la logica di checkpoint. La libreria del processore del feed delle modifiche fornisce funzionalità di checkpoint automatici e semantica di tipo "at least once".
* Solo la modifica più recente per un determinato documento viene inclusa nel registro modifiche. Le modifiche intermedie potrebbero non essere disponibili.
* Il feed delle modifiche è ordinato in base all'ordine di modifica in ciascun valore di chiave della partizione. Non esiste alcun ordine garantito tra i valori partition-key.
* Le modifiche possono essere sincronizzate da qualsiasi punto nel tempo, in altre parole non è previsto un periodo di conservazione fisso per cui sono disponibili le modifiche.
* Le modifiche sono disponibili in blocchi di intervalli di chiavi di partizione. Questa funzionalità consente di apportare modifiche da raccolte di grandi dimensioni per poi elaborarle in parallelo da più consumer/server.
* Le applicazioni possono richiedere più feed di modifiche alla volta nella stessa raccolta.

## <a name="use-cases-and-scenarios"></a>Casi d'uso e scenari

Il feed di modifiche consente di elaborare con efficienza set di dati di grandi dimensioni con volumi elevati di scritture e rappresenta un'alternativa alla query di un intero set di dati per identificare le modifiche. 

Ad esempio, con un feed di modifiche è possibile eseguire in modo efficiente le operazioni seguenti:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.
* Implementare la suddivisione in livelli e l'archiviazione a livello di applicazione, in altre parole archiviare i dati più usati ("hot data") in Azure Cosmos DB e trasferire quelli meno usati ("cold data") in [Archivio BLOB di Azure](../storage/common/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementare analisi in batch sui dati usando [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Effettuare migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos DB con uno schema di partizionamento differente.
* Implementare [pipeline lambda in Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB offre una soluzione di database scalabile che può gestire sia l'inserimento che le query e implementare architetture lambda con costo totale di proprietà ridotto. 
* Ricevere e archiviare i dati di eventi da dispositivi, sensori, infrastrutture e applicazioni, per poi elaborarli in tempo reale con [analisi di flusso di Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

L'immagine seguente illustra come le pipeline lambda che eseguono sia inserimenti che query con Azure Cosmos DB possono usare il supporto del feed di modifiche: 

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

Nelle app Web e per dispositivi mobili [senza server](http://azure.com/serverless) è anche possibile tenere traccia di eventi come le modifiche al profilo, alle preferenze o alle località dei clienti per attivare determinate azioni come l'invio di notifiche push ai dispositivi tramite [Funzioni di Azure](#azure-functions). Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Uso di Funzioni di Azure 

Se si usa Funzioni di Azure, il modo più semplice per connettersi a un feed di modifiche di Azure Cosmos DB consiste nell'aggiungere un trigger di Azure Cosmos DB all'app Funzioni di Azure. Quando si crea un trigger di Azure Cosmos DB in un'app Funzioni di Azure, si seleziona la raccolta di Azure Cosmos DB a cui connettersi e la funzione viene attivata quando viene apportata una modifica alla raccolta. 

I trigger possono essere creati nel portale di Funzioni di Azure, nel portale di Azure Cosmos DB o a livello di codice. Per altre informazioni, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](serverless-computing-database.md).

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Uso dell'SDK

[SDK di DocumentDB](documentdb-sdk-dotnet.md) per Azure Cosmos DB offre tutta la potenza necessaria per leggere e gestire un feed di modifiche, con tutte le responsabilità che ne derivano. Se è necessario gestire checkpoint e numeri di sequenza di documenti e avere un controllo granulare sulle chiavi di partizione, usare l'SDK può essere l'approccio ideale.

Questa sezione illustra come usare l'SDK di DocumentDB per lavorare con un feed di modifiche.

1. Iniziare leggendo le risorse seguenti da appconfig. Le istruzioni per recuperare l'endpoint e la chiave di autorizzazione sono disponibili in [Aggiornare la stringa di connessione](create-documentdb-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Creare il client come illustrato di seguito:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Ottenere gli intervalli di chiavi di partizione:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Chiamare ExecuteNextAsync per ogni intervallo di chiavi di partizione:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

Se si hanno più lettori, è possibile usare **ChangeFeedOptions** per distribuire il carico delle letture in thread diversi o in client diversi.

Con queste poche righe di codice è possibile iniziare a leggere il feed di modifiche. È possibile ottenere il codice completo usato in questo articolo dal [repository GitHub azure-cosmos-db-DocumentFeed](https://github.com/rsarosh/azure-cosmos-db-DocumentFeed).

Nel codice del passaggio 4 **ResponseContinuation** nell'ultima riga ha l'ultimo numero di sequenza logico (LSN) del documento, che verrà usato la volta successiva in cui si leggeranno nuovi documenti dopo questo numero di sequenza. Usando il valore **StartTime** di **ChangeFeedOption** è possibile ampliare il campo di ricerca per ottenere i documenti. Se quindi **ResponseContinuation** è null, ma **StartTime** risale indietro nel tempo, si otterranno tutti i documenti modificati dopo **StartTime**. Se invece per **ResponseContinuation** viene specificato un valore, il sistema restituirà tutti i documenti dopo tale LSN.

La matrice di checkpoint quindi conserva solo l'ultimo numero di sequenza per ogni partizione, ma se non si vogliono gestire le partizioni, i checkpoint, l'ultimo numero di sequenza, l'ora di inizio e così via, l'opzione più semplice consiste nell'usare la libreria del processore dei feed delle modifiche.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed delle modifiche 

La [libreria del processore dei feed delle modifiche di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed) consente di distribuire facilmente l'elaborazione degli eventi in più consumer. Questa libreria semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo.

Il vantaggio principale della libreria del processore dei feed delle modifiche è che non è necessario gestire ogni partizione e token di continuazione né eseguire il poll manuale di ogni raccolta.

La libreria del processore dei feed delle modifiche semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo.  Gestisce automaticamente la lettura delle modifiche nelle partizioni usando un meccanismo di lease. Come si può osservare nell'immagine seguente, se si avviano due client che usano la libreria del processore dei feed delle modifiche, il lavoro viene diviso tra di essi. Se si aumenta ancora il numero di client, il lavoro viene ulteriormente diviso tra di essi.

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Il client a sinistra è stato avviato per primo e ha iniziato a monitorare tutte le partizioni, quindi è stato avviato il secondo client e infine il primo ha assegnato alcuni lease al secondo client. Come si può notare, questo è il modo migliore per distribuire il lavoro tra computer e client diversi.

Si noti che, se si hanno due funzioni di Azure senza server che monitorano la stessa raccolta e usano lo stesso lease, le due funzioni possono ottenere documenti diversi a seconda di come la libreria del processore decide di elaborare le partizioni.

### <a name="understanding-the-change-feed-processor-library"></a>Informazioni sulla libreria del processore dei feed delle modifiche

L'implementazione del processore dei feed di modifiche prevede quattro componenti principali, ovvero la raccolta monitorata, la raccolta di lease, l'host del processore e i consumer. 

> [!WARNING]
> La creazione di una raccolta ha implicazioni a livello di prezzi, poiché si sta riservando velocità effettiva per l'applicazione per comunicare con Azure Cosmos DB. Per altre informazioni, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Raccolta monitorata:** la raccolta monitorata include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e modifiche alla raccolta monitorata vengono riflessi nel feed di modifiche della raccolta. 

**Raccolta di lease:** la raccolta di lease coordina l'elaborazione dei feed di modifiche in più processi di lavoro. Una raccolta separata viene usata per archiviare i lease con un lease per partizione. È consigliabile archiviare questa raccolta di lease in un account diverso, con un'area di scrittura più vicina alla posizione in cui è in esecuzione il processore dei feed di modifiche. Un oggetto lease contiene gli attributi seguenti: 
* Owner: specifica l'host proprietario del lease.
* Continuation: specifica la posizione (token di continuazione) nel feed di modifiche per una partizione specifica.
* Timestamp: data dell'ultimo aggiornamento del lease. Il timestamp può essere usato per verificare se il lease viene considerato scaduto. 

**Host del processore:** ogni host determina il numero di partizioni da elaborare in base al numero di istanze di host con lease attivi. 
1.  Quando un host viene avviato, acquisisce lease per bilanciare il carico di lavoro in tutti gli host. Un host rinnova periodicamente i lease, in modo che i lease rimangano attivi. 
2.  Un host imposta come checkpoint il token di continuazione più recente sul rispettivo lease per ogni lettura. Per garantire la sicurezza della concorrenza, un host verifica il valore ETag per ogni aggiornamento di lease. Sono supportate anche altre strategie per i checkpoint.  
3.  All'arresto, un host rilascia tutti i lease ma mantiene le informazioni sulla continuazione, in modo che sia possibile riprendere la lettura dal checkpoint archiviato in un secondo momento. 

Il numero di host non può attualmente essere superiore al numero di partizioni (lease).

**Consumer:** i consumer, o processi di lavoro, sono thread che eseguono l'elaborazione del feed di modifiche avviata da ogni host. Ogni host di processori può includere più consumer. Ogni consumer legge il feed di modifiche dalla partizione a cui è assegnato e invia notifiche al rispettivo host in caso di modifiche e di lease scaduti.

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. La raccolta monitorata archivia i documenti e usa il valore "city" come chiave di partizione. Come si può notare, la partizione blu contiene i documenti con il campo "city" compreso tra "A-E" e così via. Sono disponibili due host, ognuno con due consumer che leggono dalle quattro partizioni in parallelo. Le frecce mostrano i consumer che leggono da un punto specifico nel feed di modifiche. Nella prima partizione il colore blu scuro rappresenta le modifiche non lette, mentre il colore blu chiaro rappresenta le modifiche già lette nel feed di modifiche. Gli host usano la raccolta di lease per archiviare un valore di tipo "continuation" per tenere traccia della posizione di lettura corrente per ogni consumer. 

![Uso dell'host del processore del feed delle modifiche di Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed delle modifiche

Prima di installare il pacchetto NuGet del processore dei feed delle modifiche, è necessario installare quanto segue: 

* Microsoft.Azure.DocumentDB, versione 1.13.1 o successiva 
* Newtonsoft.Json, versione 9.0.1 o successiva

Installare quindi il [pacchetto Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e includerlo come riferimento.

Per implementare la libreria del processore dei feed delle modifiche, è necessario seguire questa procedura:

1. Implementare un oggetto **DocumentFeedObserver**, che implementa **IChangeFeedObserver**.

2. Implementare un oggetto **DocumentFeedObserverFactory**, che implementa **IChangeFeedObserverFactory**.

3. Nel metodo **CreateObserver** di **DocumentFeedObserverFacory**, creare un'istanza di **ChangeFeedObserver** creato nel passaggio 1 e restituirlo.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Creare un'istanza di **DocumentObserverFactory**.

5. Creare un'istanza di **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Registrare **DocumentFeedObserverFactory** con l'host.

Il codice per i passaggi da 4 a 6 è: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

La procedura è terminata. Dopo questi passaggi i documenti inizieranno a essere inclusi nel metodo **DocumentFeedObserver ProcessChangesAsync**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Azure Cosmos DB con Funzioni di Azure, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](serverless-computing-database.md).

Per altre informazioni sull'uso della libreria del processore dei feed delle modifiche, usare le risorse seguenti:

* [Pagina delle informazioni](documentdb-sdk-dotnet-changefeed.md) 
* [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Codice di esempio che illustra i passaggi precedenti da 1 a 6](https://github.com/rsarosh/Cosmos-ChangeFeedProcessor)
* [Altri esempi in GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Per altre informazioni sull'uso del feed di modifiche tramite l'SDK, usare le risorse seguenti:

* [Pagina delle informazioni sull'SDK](documentdb-sdk-dotnet.md)
