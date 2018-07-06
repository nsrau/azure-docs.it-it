---
title: Uso del supporto del feed delle modifiche in Azure Cosmos DB | Microsoft Docs
description: Usare il supporto del feed delle modifiche di Azure Cosmos DB per tenere traccia delle modifiche nei documenti, eseguire elaborazioni basate su eventi come i trigger e mantenere aggiornati i sistemi di cache e analisi.
keywords: feed delle modifiche
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8475c79782730e989f9590566c31ccd50af9f144
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302047"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Uso del supporto del feed delle modifiche in Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) è un database con replica a livello globale rapido e flessibile, ideale per le applicazioni IoT, di videogiochi, del settore della vendita al dettaglio e di registrazioni di operazioni. Uno schema progettuale comune in queste applicazioni prevede l'uso delle modifiche ai dati per avviare azioni aggiuntive. Queste azioni aggiuntive possono essere una delle seguenti: 

* Attivazione di una notifica o di una chiamata a un'API quando un documento viene inserito o modificato.
* Elaborazione di flussi per IoT o esecuzione di analisi.
* Spostamento dei dati aggiuntivi tramite sincronizzazione con una cache, un motore di ricerca o un data warehouse oppure tramite archiviazione dei dati nella risorsa di archiviazione offline sicura.

Il **supporto del feed di modifiche** in Azure Cosmos DB consente di creare soluzioni efficienti e scalabili per ognuno di questi modelli, come illustrato nell'immagine seguente:

![Uso del feed delle modifiche di Azure Cosmos DB per agevolare le analisi in tempo reale e gli scenari di calcolo guidati dagli eventi](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Il supporto del feed di modifiche viene fornito per tutti i modelli di dati e contenitori in Azure Cosmos DB. Il feed di modifiche tuttavia viene letto usando il client SQL e serializza gli elementi in formato JSON. A causa della formattazione JSON, nei client MongoDB i documenti in formato JSON non corrisponderanno al feed di modifiche in formato JSON.

Nel video seguente Andrew Liu, Program Manager di Azure Cosmos DB, illustra il funzionamento del feed di modifiche in Azure Cosmos DB.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Funzionamento del feed di modifiche

Il supporto del feed di modifiche in Azure Cosmos DB è in ascolto di eventuali modifiche in una raccolta di Azure Cosmos DB. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Le modifiche sono persistenti, possono essere elaborate in modo asincrono e incrementale e l'output può essere distribuito a uno o più consumer per l'elaborazione parallela. 

È possibile leggere il feed di modifiche in tre modi diversi, come verrà illustrato più avanti in questo articolo:

*   [Uso di Funzioni di Azure](#azure-functions)
*   [Uso dell'SDK di Azure Cosmos DB](#sql-sdk)
*   [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](#change-feed-processor)

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
* È possibile usare ChangeFeedOptions.StartTime per fornire un punto di partenza iniziale, ad esempio per trovare il token di continuazione corrispondente a un'ora specificata. Se si specifica ContinuationToken, questo avrà priorità rispetto ai valori di StartTime e StartFromBeginning. La precisione di ChangeFeedOptions.StartTime è circa di 5 secondi. 

## <a name="use-cases-and-scenarios"></a>Casi d'uso e scenari

Il feed di modifiche consente di elaborare con efficienza set di dati di grandi dimensioni con volumi elevati di scritture e rappresenta un'alternativa alla query di un intero set di dati per identificare le modifiche. 

Ad esempio, con un feed di modifiche è possibile eseguire in modo efficiente le operazioni seguenti:

* Aggiornare una cache, un indice di ricerca o un data warehouse con i dati archiviati in Azure Cosmos DB.
* Implementare la suddivisione in livelli e l'archiviazione a livello di applicazione, in altre parole archiviare i dati più usati ("hot data") in Azure Cosmos DB e trasferire quelli meno usati ("cold data") in [Archivio BLOB di Azure](../storage/common/storage-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Effettuare migrazioni senza alcun tempo di inattività a un altro account Azure Cosmos DB con uno schema di partizionamento differente.
* Implementare [pipeline lambda in Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) con Azure Cosmos DB. Azure Cosmos DB offre una soluzione di database scalabile che può gestire sia l'inserimento che le query e implementare architetture lambda con costo totale di proprietà ridotto. 
* Ricevere e archiviare i dati di eventi da dispositivi, sensori, infrastrutture e applicazioni, per poi elaborarli in tempo reale con [analisi di flusso di Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) o [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

L'immagine seguente illustra come le pipeline lambda che eseguono sia inserimenti che query con Azure Cosmos DB possono usare il supporto del feed di modifiche: 

![Pipeline lambda basate su Azure Cosmos DB per l'inserimento e le query](./media/change-feed/lambda.png)

Nelle app Web e per dispositivi mobili [senza server](http://azure.com/serverless) è anche possibile tenere traccia di eventi come le modifiche al profilo, alle preferenze o alle località dei clienti per attivare determinate azioni come l'invio di notifiche push ai dispositivi tramite [Funzioni di Azure](#azure-functions). Se si usa Azure Cosmos DB per creare un gioco, è ad esempio possibile usare il feed delle modifiche per implementare classifiche in tempo reale in base ai punteggi delle partite completate.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Uso di Funzioni di Azure 

Se si usa Funzioni di Azure, il modo più semplice per connettersi a un feed di modifiche di Azure Cosmos DB consiste nell'aggiungere un trigger di Azure Cosmos DB all'app Funzioni di Azure. Quando si crea un trigger di Azure Cosmos DB in un'app Funzioni di Azure, si seleziona la raccolta di Azure Cosmos DB a cui connettersi e la funzione viene attivata quando viene apportata una modifica alla raccolta. 

I trigger possono essere creati nel portale di Funzioni di Azure, nel portale di Azure Cosmos DB o a livello di codice. Per altre informazioni, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Uso dell'SDK

[SQL SDK](sql-api-sdk-dotnet.md) per Azure Cosmos DB offre tutta la potenza necessaria per leggere e gestire un feed di modifiche, con tutte le responsabilità che ne derivano. Se è necessario gestire checkpoint e numeri di sequenza di documenti e avere un controllo granulare sulle chiavi di partizione, usare l'SDK può essere l'approccio ideale.

Questa sezione illustra come usare SQL SDK per lavorare con un feed di modifiche.

1. Iniziare leggendo le risorse seguenti da appconfig. Le istruzioni per recuperare l'endpoint e la chiave di autorizzazione sono disponibili in [Aggiornare la stringa di connessione](create-sql-api-dotnet.md#update-your-connection-string).

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

> [!NOTE]
> Invece di `ChangeFeedOptions.PartitionKeyRangeId`, è possibile usare `ChangeFeedOptions.PartitionKey` per specificare una singola chiave di partizione per cui ottenere un feed di modifiche. Ad esempio, `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Se si hanno più lettori, è possibile usare **ChangeFeedOptions** per distribuire il carico delle letture in thread diversi o in client diversi.

Con queste poche righe di codice è possibile iniziare a leggere il feed di modifiche. È possibile ottenere il codice completo usato in questo articolo dal [repository GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

Nel codice del passaggio 4 **ResponseContinuation** nell'ultima riga ha l'ultimo numero di sequenza logico (LSN) del documento, che verrà usato la volta successiva in cui si leggeranno nuovi documenti dopo questo numero di sequenza. Usando il valore **StartTime** di **ChangeFeedOption** è possibile ampliare il campo di ricerca per ottenere i documenti. Se quindi **ResponseContinuation** è null, ma **StartTime** risale indietro nel tempo, si otterranno tutti i documenti modificati dopo **StartTime**. Se invece per **ResponseContinuation** viene specificato un valore, il sistema restituirà tutti i documenti dopo tale LSN.

La matrice di checkpoint quindi conserva solo l'ultimo numero di sequenza per ogni partizione, ma se non si vogliono gestire le partizioni, i checkpoint, l'ultimo numero di sequenza, l'ora di inizio e così via, l'opzione più semplice consiste nell'usare la libreria del processore dei feed di modifiche.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed di modifiche 

La [libreria del processore dei feed di modifiche di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) consente di distribuire facilmente l'elaborazione degli eventi in più consumer. Questa libreria semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo.

Il vantaggio principale della libreria del processore dei feed di modifiche è che non è necessario gestire ogni partizione e token di continuazione né eseguire il poll manuale di ogni raccolta.

La libreria del processore dei feed di modifiche semplifica la lettura delle modifiche nelle partizioni e in più thread che operano in parallelo.  Gestisce automaticamente la lettura delle modifiche nelle partizioni usando un meccanismo di lease. Come si può osservare nell'immagine seguente, se si avviano due client che usano la libreria del processore dei feed di modifiche, il lavoro viene diviso tra di essi. Se si aumenta ancora il numero di client, il lavoro viene ulteriormente diviso tra di essi.

![Elaborazione distribuita del feed delle modifiche di Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Il client a sinistra è stato avviato per primo e ha iniziato a monitorare tutte le partizioni, quindi è stato avviato il secondo client e infine il primo ha assegnato alcuni lease al secondo client. Come si può notare, questo è il modo migliore per distribuire il lavoro tra computer e client diversi.

Si noti che, se si hanno due funzioni di Azure senza server che monitorano la stessa raccolta e usano lo stesso lease, le due funzioni possono ottenere documenti diversi a seconda di come la libreria del processore decide di elaborare le partizioni.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Informazioni sulla libreria del processore dei feed di modifiche

L'implementazione della libreria del processore dei feed di modifiche prevede quattro componenti principali, ovvero la raccolta monitorata, la raccolta di lease, l'host del processore e i consumer. 

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

### <a name="working-with-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed di modifiche

Prima di installare il pacchetto NuGet del processore dei feed di modifiche, installare quanto segue: 

* Microsoft.Azure.DocumentDB, versione più recente.
* Newtonsoft.Json, versione più recente

Installare quindi il [pacchetto Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e includerlo come riferimento.

Per implementare la libreria del processore dei feed di modifiche, è necessario seguire questa procedura:

1. Implementare un oggetto **DocumentFeedObserver**, che implementa **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implementare un oggetto **DocumentFeedObserverFactory**, che implementa **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Definire *CancellationTokenSource* e *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. Compilare **ChangeFeedProcessorBuilder** dopo avere definito gli oggetti pertinenti 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>È possibile leggere il feed di modifiche tramite Java?

La libreria Java per leggere il feed di modifiche è disponibile nel [repository Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Tuttavia, attualmente la libreria Java è alcune versioni indietro rispetto alla libreria .NET. A breve le due librerie saranno sincronizzate.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Si possono usare _etag, _lsn o _ts per la gestione interna e come in risposta?

_etag è un formato interno da cui è sconsigliabile dipendere, pertanto evitare di analizzarlo perché può cambiare in qualsiasi momento.
_ts è un timestamp di creazione o modifica. Si può usare _ts per confronti cronologici.
_lsn è un ID batch che viene aggiunto solo per i feed di modifiche e che rappresenta l'ID della transazione dall'archivio. Molti documenti potrebbero avere lo stesso _lsn.
Un'altra cosa da segnalare: l'ETag di FeedResponse è diverso dal valore _etag visualizzato nel documento. _etag è un identificatore interno utilizzato per il controllo della concorrenza e indica la versione del documento mentre ETag viene utilizzato per la sequenziazione del feed.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>La lettura del feed di modifiche aggiunge costi supplementari?

L'addebito riguarda le UR utilizzate, ad esempio, lo spostamento dei dati da e verso le raccolte di Azure Cosmos DB utilizzano sempre le UR. Agli utenti verranno addebitate le UR utilizzate dalla raccolta di lease.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Più Funzioni di Azure possono leggere il feed di modifiche di una sola raccolta?

Sì. S', più Funzioni di Azure possono leggere il feed di modifiche di una sola raccolta. Tuttavia, per ogni istanza di Funzioni di Azure dovrà essere stato definito un leaseCollectionPrefix separato.

### <a name="should-the-lease-collection-be-partitioned"></a>La raccolta di lease deve essere partizionata?

No, la raccolta di lease può essere fissa. Le raccolte di lease partizionate non sono necessarie e attualmente neanche supportate.

### <a name="can-i-read-change-feed-from-spark"></a>È possibile leggere il feed di modifiche tramite Spark?

Sì, Vedere [Velocizzare l'analisi di Big Data in tempo reale con il connettore Spark per Azure Cosmos DB](spark-connector.md). È disponibile uno [screencast](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) che mostra come elaborare il feed di modifiche come flusso strutturato.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Poniamo che si stia elaborando un feed di modifiche utilizzando Funzioni di Azure, ad esempio su un batch di 10 documenti, e che si verifichi un errore nel documento 7. In tal caso, gli ultimi tre documenti non verrebbero elaborati. In che modo è possibile riprendere l'elaborazione a partire dal documento non riuscito, cioè dal documento 7, nel feed successivo?

La soluzione consigliata per gestire l'errore consiste nel racchiudere il codice in un blocco Try-Catch. Questa procedura consiste nel rilevare l'errore, inserire il documento in questione in una coda di documenti non recapitabili e quindi definire la logica per gestire i documenti che hanno generato l'errore. Se il batch comprende 200 documenti e l'errore riguarda un solo documento, questo metodo consente di evitare di invalidare l'intero batch.

In caso di errore, non si dovrà riportare il punto di controllo all'inizio altrimenti i documenti che hanno generato l'errore verranno continuamente recuperati dal feed di modifiche. Tenere presente che il feed di modifiche mantiene l'ultimo snapshot finale dei documenti, quindi, per questo motivo, si potrebbe perdere lo snapshot precedente del documento. Il feed di modifiche mantiene solo l'ultima versione del documento e nel frattempo altri processi possono intervenire a modificare il documento.

Man mano che si continua a correggere il codice, la coda dei documenti non recapitabili si svuoterà.
Funzioni di Azure viene chiamato automaticamente dal sistema del feed di modifiche e il punto di controllo viene gestito internamente dalla funzione di Azure. Se si desidera eseguire il rollback del punto di controllo e controllarne tutti gli aspetti, considerare di utilizzare l'SDK processore del feed di modifiche.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Azure Cosmos DB con Funzioni di Azure, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](serverless-computing-database.md).

Per altre informazioni sull'uso della libreria del processore dei feed di modifiche, usare le risorse seguenti:

* [Pagina delle informazioni](sql-api-sdk-dotnet-changefeed.md) 
* [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Codice di esempio che illustra i passaggi precedenti da 1 a 6](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Altri esempi in GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Per altre informazioni sull'uso del feed di modifiche tramite l'SDK, usare le risorse seguenti:

* [Pagina delle informazioni sull'SDK](sql-api-sdk-dotnet.md)
