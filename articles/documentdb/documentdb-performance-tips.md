<properties 
	pageTitle="Suggerimenti sulle prestazioni per DocumentDB | Microsoft Azure" 
	description="Informazioni sulle opzioni di configurazione del client per migliorare le prestazioni del database di Azure DocumentDB"
	keywords="Come migliorare le prestazioni del database"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>  

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016" 
	ms.author="mimig"/>  

# Suggerimenti sulle prestazioni per DocumentDB

Azure DocumentDB è un database distribuito rapido e flessibile, perfettamente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per ridimensionare il database con DocumentDB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una [chiamata al metodo SDK](documentdb-performance-levels.md#changing-performance-levels-using-the-net-sdk). Poiché si accede a DocumentDB tramite chiamate di rete, è possibile apportare ottimizzazioni lato client per ottenere prestazioni ottimali.

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## Rete
<a id="direct-connection"></a>

1. **Criteri di connessione: usare la modalità di connessione diretta**
    
    La modalità di connessione del client ad Azure DocumentDB influisce significativamente sulle prestazioni, in particolare a livello di latenza lato client osservata. Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client, ovvero la *modalità* di connessione e il [protocollo di *connessione*](#connection-protocol). Le due modalità disponibili sono:

    1. Modalità gateway (predefinita)
    2. Modalità diretta

    Poiché DocumentDB è un sistema di archiviazione distribuita, le risorse di DocumentDB come le raccolte vengono partizionate in più computer e ogni partizione viene replicata per assicurare la disponibilità elevata. La traduzione da indirizzo logico a fisico viene mantenuta in una tabella di routing disponibile anche internamente come risorsa.

    Nella modalità gateway i computer del gateway di DocumentDB eseguono il routing, consentendo di ottenere codice client semplice e compatto. Un'applicazione client emette richieste per i computer del gateway di DocumentDB, che converte l'URI logico della richiesta nell'indirizzo fisico del nodo back-end e quindi inoltra la richiesta in modo appropriato. Nella modalità diretta, invece, i client devono mantenere e aggiornare periodicamente una copia di questa tabella di routing e quindi devono connettersi direttamente ai nodi back-end di DocumentDB.

    La modalità gateway è supportata in tutte le piattaforme SDK ed è l'impostazione predefinita configurata. Se l'applicazione è in esecuzione in una rete aziendale con limitazioni rigide del firewall, la modalità gateway è la scelta migliore, perché usa la porta HTTPS standard e un singolo endpoint. A livello di prestazioni, tuttavia, la modalità gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in DocumentDB. La modalità diretta offre quindi prestazioni migliori grazie al numero minore di hop di rete.

2. **Criteri di connessione: usare il protocollo TCP**

    Quando si usa la modalità diretta, sono disponibili due opzioni del protocollo:

    - TCP
    - HTTPS

    DocumentDB offre un modello di programmazione RESTful su HTTPS semplice e aperto. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Sia il protocollo TCP diretto che il protocollo HTTPS usano SSL per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

    La modalità di connessione viene configurata durante la creazione dell'istanza di DocumentClient con il parametro ConnectionPolicy. Se si usa la modalità diretta, è possibile configurare il protocollo entro il parametro ConnectionPolicy.

        var serviceEndpoint = new Uri("https://contoso.documents.net");
        var authKey = new "your authKey from Azure Mngt Portal";
        DocumentClient client = new DocumentClient(serviceEndpoint, authKey, 
        new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        });

    Poiché TCP è supportato solo in modalità diretta, se si usa la modalità gateway viene usato sempre il protocollo HTTPS per comunicare con il gateway e il valore del protocollo in ConnectionPolicy viene ignorato.

    ![Illustrazione dei criteri di connessione di DocumentDB](./media/documentdb-performance-tips/azure-documentdb-connection-policy.png)

3. **Chiamare OpenAsync per evitare la latenza di avvio alla prima richiesta**

    Per impostazione predefinita, la prima richiesta avrà una latenza più elevata perché deve recuperare la tabella di routing degli indirizzi. Per evitare questa latenza di avvio alla prima richiesta, è necessario chiamare OpenAsync() una volta durante l'inizializzazione, come indicato di seguito.

        await client.OpenAsync();
<a id="same-region"></a>
4. **Collocare i client nella stessa area di Azure per ottenere prestazioni migliori**

    Quando possibile, posizionare eventuali applicazioni che chiamano DocumentDB nella stessa area del database di DocumentDB. Per un confronto approssimativo, le chiamate a DocumentDB eseguite nella stessa area vengono completate entro 1-2 ms, ma la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 ms. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di DocumentDB con provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di DocumentDB](./media/documentdb-performance-tips/azure-documentdb-same-region.png) <a id="increase-threads"></a>
5. **Aumentare il numero di thread/attività**

    Poiché le chiamate a DocumentDB vengono eseguite sulla rete, può essere necessario modificare il grado di parallelismo delle richieste in modo che i tempi di attesa dell'applicazione client tra le richieste siano molto ridotti. Se ad esempio si usa la [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx) di .NET, creare centinaia di attività di lettura o scrittura in DocumentDB.

## Uso dell'SDK

1. **Installare l'SDK più recente**

    Vengono apportati continuamente miglioramenti ai DocumentDB SDK per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere [DocumentDB SDK](documentdb-sdk-dotnet.md).

2. **Usare un client DocumentDB singleton per la durata dell'applicazione**
  
    Si noti che ogni istanza di DocumentClient è thread-safe ed esegue la gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi quando viene usata la modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte di DocumentClient, è consigliabile usare una singola istanza di DocumentClient per ogni AppDomain per la durata dell'applicazione. <a id="max-connection"></a>
3. **Aumentare il valore di System.Net MaxConnections per ogni host**

    Le richieste di DocumentDB vengono eseguite su HTTPS/REST per impostazione predefinita e sono soggette ai limiti di connessione predefiniti per ogni nome host o indirizzo IP. Può essere necessario impostare MaxConnections su un valore più alto (100-1000) in modo che la libreria client possa usare più connessioni DocumentDB contemporaneamente. In .NET SDK 1.8.0 e versioni successive il valore predefinito per [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50 e per modificare il valore è possibile impostare [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/it-IT/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore più elevato.

4. **Ottimizzazione delle query parallele per le raccolte partizionate**

     DocumentDB .NET SDK 1.9.0 e versioni successive supportano le query parallele, che consentono di eseguire query in una raccolta partizionata in parallelo (vedere [Uso degli SDK](documentdb-partition-data.md#working-with-the-sdks) e i relativi [esempi di codice](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) per maggiori dettagli). Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali. Mettono a disposizione due parametri che gli utenti possono ottimizzare in funzione dei requisiti, ovvero (a) MaxDegreeOfParallelism per definire il numero massimo di partizioni sulle quali è possibile eseguire query in parallelo e (b) MaxBufferedItemCount per definire il numero di risultati di prelettura.
    
    (a) ***Ottimizzazione di MaxDegreeOfParallelism:*** la query parallela funziona eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. L'impostazione di MaxDegreeOfParallelism sul numero di partizioni offre quindi la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di MaxDegreeOfParallelism su un numero elevato; il sistema sceglierà il numero minimo (numero di partizioni, input specificato dall'utente) come valore di MaxDegreeOfParallelism.
    
    È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.
    
    (b) ***Ottimizzazione di MaxBufferedItemCount:*** la query parallela è progettata per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. La prelettura consente il miglioramento complessivo della latenza di una query. MaxBufferedItemCount è il parametro che consente di limitare la quantità di risultati di prelettura. L'impostazione di MaxBufferedItemCount sul numero previsto di risultati restituiti (o un numero più alto) consente alla query di ottenere il massimo vantaggio dalla prelettura.
    
    Si noti che la prelettura funziona allo stesso modo indipendentemente dall'impostazione di MaxDegreeOfParallelism e che è presente un solo buffer per i dati di tutte le partizioni.

5. **Attivare GC sul lato server**
    
    In alcuni casi, può essere utile ridurre la frequenza di Garbage Collection. In .NET impostare [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) su true.

6. **Implementare il backoff in corrispondenza di intervalli RetryAfter**
 
    Durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff sulla limitazione per l'intervallo tra tentativi specificato dal server. Rispettando il backoff si garantiscono tempi di attesa minimi tra i tentativi. Il supporto per i criteri relativi alla ripetizione dei tentativi è incluso nella versione 1.8.0 e versioni successive di DocumentDB [.NET](documentdb-sdk-dotnet.md) e [Java](documentdb-sdk-java.md) e nella versione 1.9.0 e versioni successive di [Node.js](documentdb-sdk-nodejs.md) e [Python](documentdb-sdk-python.md). Per altre informazioni, vedere [Superamento dei limiti della velocità effettiva riservata](documentdb-request-units.md#exceeding-reserved-throughput-limits) e [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).

7. **Aumentare il carico di lavoro client**

    Se si sta eseguendo il test a livelli di velocità effettiva elevati (> 50.000 UR/sec), l'applicazione client può diventare un collo di bottiglia a causa della limitazione di utilizzo della CPU o della rete. In questo caso, è possibile continuare a eseguire il push dell'account di DocumentDB tramite l'aumento delle istanze delle applicazioni client su più server.

8. **Memorizzare nella cache gli URI dei documenti per una minore latenza di lettura**

    Memorizzare nella cache gli URI dei documenti quando possibile per ottenere prestazioni di lettura ottimali. <a id="tune-page-size"></a>
9. **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura, ovvero ReadDocumentFeedAsync, oppure quando si emette una query SQL di DocumentDB, i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni di pagina usando l'intestazione di richiesta x-ms-max-item-count fino a 1000. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni di pagina a 10 in modo da ridurre la velocità effettiva usata per le letture e le query.

    È anche possibile impostare le dimensioni di pagina usando i DocumentDB SDK disponibili. ad esempio:
    
        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });

10. **Aumentare il numero di thread/attività**

	Vedere [Aumentare il numero di thread/attività](increase-threads.md) nella sezione Rete.

## Criterio di indicizzazione

1. **Usare l'indicizzazione differita per frequenze di inserimento più veloci nei momenti di massima attività**

    DocumentDB consente di specificare a livello di raccolta un criterio di indicizzazione, che permette di scegliere se i documenti di una raccolta devono essere indicizzati automaticamente o meno. È anche possibile scegliere tra aggiornamenti sincroni (coerenti) e asincroni (differiti). Per impostazione predefinita, l'indice viene aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. La modalità sincrona consente alle query di rispettare lo stesso [livello di coerenza](documentdb-consistency-levels.md) delle letture di documenti, senza che l'indice debba recuperare alcun ritardo.
    
    L'indicizzazione differita è consigliabile per gli scenari in cui i dati vengono scritti in burst e si vuole ammortizzare il lavoro necessario per indicizzare il contenuto dell'indice in un periodo di tempo più lungo. L'indicizzazione differita consente di usare in modo efficiente la velocità effettiva con provisioning e di gestire le richieste di scrittura nei momenti di massima attività con latenza minima. Con l'indicizzazione differita attivata, i risultati delle query saranno comunque coerenti indipendentemente dal livello di coerenza configurato per l'account DocumentDB.

    La modalità di indicizzazione coerente (IndexingPolicy.IndexingMode impostato su Consistent) comporta gli addebiti più elevati per unità richiesta per ogni scrittura, mentre la modalità di indicizzazione differita (IndexingPolicy.IndexingMode impostato su Lazy) e l'assenza di indicizzazione (IndexingPolicy.Automatic impostato su False) non comportano alcun costo di indicizzazione al momento della scrittura.

2. **Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di DocumentDB consentono anche di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Il codice seguente, ad esempio, illustra come escludere un'intera sezione dei documenti, definita anche sottoalbero, dall'indicizzazione usando il carattere jolly "*".

        var collection = new DocumentCollection { Id = "excludedPathCollection" };
        collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
        collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
        collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);

    Per altre informazioni, vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

## Velocità effettiva
<a id="measure-rus"></a>

1. **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    DocumentDB offre un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Il provisioning delle [unità richiesta](documentdb-request-units.md) viene eseguito per ogni account di database in base al numero di unità di capacità acquistato. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano il livello di unità richiesta con provisioning previsto per il relativo account sono limitate fino al ritorno del livello sotto il valore riservato per l'account. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile acquistare unità di capacità aggiuntive.

    La complessità di una query influisce sulla quantità di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualunque operazione (create, update o delete), esaminare l'intestazione x-ms-request-charge (o la proprietà RequestCharge equivalente in ResourceResponse<T> oppure FeedResponse<T> in .NET SDK) per determinare il numero di unità richiesta usate da queste operazioni.

        // Measure the performance (request units) of writes
        ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
        Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
        // Measure the performance (request units) of queries
        IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
        while (queryable.HasMoreResults)
             {
                  FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
                  Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
             }
        
    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning, ad esempio 2000 UR/secondo. Se, ad esempio, la query precedente restituisce 1000 documenti da 1 KB, il costo dell'operazione sarà 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare le richieste successive. Per altre informazioni, vedere [Unità richiesta](documentdb-request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).

2. **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione x-ms-retry-after-ms, che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.
 
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se più client operano collettivamente in modo costante al di sopra della frequenza delle richieste, il numero di ripetizioni dei tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando RetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](documentdb-request-units.md).
   
3. **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni.

## Livelli di coerenza

1. **Usare livelli di coerenza meno rigorosi per ottenere latenze di lettura migliori**

    Un altro fattore importante da prendere in considerazione durante l'ottimizzazione delle prestazioni di un'applicazione di DocumentDB è il livello di coerenza. La scelta del livello di coerenza comporta implicazioni per le operazioni di lettura e di scrittura. È possibile configurare il livello di coerenza predefinito sull'account del database. Il livello di coerenza scelto verrà quindi applicato a tutte le raccolte in tutti i database nell'account DocumentDB. Per quanto riguarda le operazioni di scrittura, l'impatto della modifica del livello di coerenza viene osservato come latenza delle richieste. Se si usano livelli di coerenza più rigorosi, le latenze di scrittura aumenteranno. D'altra parte, l'impatto del livello di coerenza sulle operazioni di lettura viene osservato in termini di velocità effettiva. I livelli di coerenza meno rigorosi consentono al cliente di ottenere una velocità di lettura superiore.

    Per impostazione predefinita, tutte le letture e le query eseguite sulle risorse definite dall'utente usano il livello di coerenza predefinito specificato nell'account di database. È comunque possibile ridurre il livello di coerenza di una specifica richiesta di lettura/query specificando l'intestazione di richiesta x-ms-consistency-level. Per altre informazioni, vedere [Livelli di coerenza in DocumentDB](documentdb-consistency-levels.md).

## Passaggi successivi

Per un'applicazione di esempio usata per valutare DocumentDB per scenari a prestazioni elevate su un numero ridotto di computer client, vedere [Test delle prestazioni e della scalabilità con Azure DocumentDB](documentdb-performance-testing.md).

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere [Partizionamento e scalabilità in Azure DocumentDB](documentdb-partition-data.md).

<!---HONumber=AcomDC_0928_2016-->