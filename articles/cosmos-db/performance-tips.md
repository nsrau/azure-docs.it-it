---
title: Suggerimenti sulle prestazioni di DB Cosmos Azure per .NET | Documenti Microsoft
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni del database Azure Cosmos DB
keywords: Come migliorare le prestazioni del database
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mimig
ms.openlocfilehash: 84a1913bd218d512f7f2818291f59d98628a7272
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e .NET

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche di architettura principali o scrivere codici complessi per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una [chiamata al metodo SDK](set-throughput.md#set-throughput-sdk). Tuttavia, poiché Azure Cosmos DB è possibile accedere tramite chiamate di rete sono ottimizzazioni sul lato client è possibile apportare a ottenere prestazioni ottimali, quando si utilizza il [SQL .NET SDK](documentdb-sdk-dotnet.md).

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete
<a id="direct-connection"></a>

1. **Criteri di connessione: usare la modalità di connessione diretta**

    La modalità con cui un client si connette al database di Azure Cosmos ha implicazioni molto importanti sulle prestazioni, specie in termini di latenza client-side osservata. Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client, ovvero la *modalità* di connessione e il [protocollo di *connessione*](#connection-protocol).  Le due modalità disponibili sono:

   1. Modalità gateway (predefinita)
   2. Modalità diretta

      La modalità gateway è supportata in tutte le piattaforme SDK ed è l'impostazione predefinita configurata.  Se l'applicazione è in esecuzione in una rete aziendale con limitazioni rigide del firewall, la modalità gateway è la scelta migliore, perché usa la porta HTTPS standard e un singolo endpoint. Il compromesso di prestazioni, tuttavia, è che la modalità di Gateway implica un hop di rete aggiuntiva ogni volta i dati vengono letti o scritti nel database di Azure Cosmos. La modalità diretta offre quindi prestazioni migliori grazie al numero minore di hop di rete.
<a id="use-tcp"></a>
2. **Criteri di connessione: usare il protocollo TCP**

    Quando si usa la modalità diretta, sono disponibili due opzioni del protocollo:

   * TCP
   * HTTPS

     DB Cosmos Azure offre un modello di programmazione di REST semplice e aprire tramite HTTPS. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Sia il protocollo TCP diretto che il protocollo HTTPS usano SSL per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

     Quando si utilizza TCP in modalità di Gateway, la porta TCP 443 è la porta di Azure Cosmos DB 10255 è la porta di MongoDB API. Quando si utilizza TCP in modalità diretta, oltre alle porte Gateway, è necessario verificare che la porta compreso tra 10000 e 20000 è aperto, poiché Azure Cosmos DB utilizza le porte TCP dinamiche. Se queste porte non sono aperte e si tenta di usare TCP, si riceverà un errore 503 (Servizio non disponibile).

     La modalità di connessione viene configurata durante la creazione dell'istanza di DocumentClient con il parametro ConnectionPolicy. Se si usa la modalità diretta, è possibile configurare il protocollo entro il parametro ConnectionPolicy.

    ```C#
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Poiché TCP è supportato solo in modalità diretta, se si usa la modalità gateway viene usato sempre il protocollo HTTPS per comunicare con il gateway e il valore del protocollo in ConnectionPolicy viene ignorato.

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/connection-policy.png)

3. **Chiamare OpenAsync per evitare la latenza di avvio alla prima richiesta**

    Per impostazione predefinita, la prima richiesta ha una latenza più elevata perché deve recuperare la tabella di routing degli indirizzi. Per evitare questa latenza di avvio alla prima richiesta, è necessario chiamare OpenAsync() una volta durante l'inizializzazione, come indicato di seguito.

        await client.OpenAsync();
   <a id="same-region"></a>
4. **Collocare i client nella stessa area di Azure per ottenere prestazioni migliori**

    Quando possibile, inserire tutte le applicazioni che chiamano DB Cosmos Azure nella stessa area del database di Azure Cosmos DB. Per un confronto approssimativo, le chiamate al database di Azure Cosmos nella stessa area completata entro 1-2 ms, ma la latenza tra l'occidentale e costa orientale degli Stati Uniti è > 50 ms. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. La latenza più bassa possibile scopo è necessario garantire che l'applicazione chiamante si trova nella stessa area di Azure come endpoint Azure Cosmos DB provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **Aumentare il numero di thread/attività**

    Dato che le chiamate ad Azure Cosmos DB vengono eseguite sulla rete, può essere necessario modificare il grado di parallelismo delle richieste in modo che i tempi di attesa dell'applicazione client tra le richieste siano molto ridotti. Ad esempio, se si sta utilizzando. Del NET [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), creare nell'ordine 100s delle attività di lettura o scrittura al database di Azure Cosmos.

## <a name="sdk-usage"></a>Uso dell'SDK
1. **Installare l'SDK più recente**

    Azure Cosmos DB SDK sono costantemente aggiornate per garantire prestazioni ottimali. Vedere il [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) pagine per determinare il SDK più recente, esaminare i miglioramenti.
2. **Utilizzare un client di Azure Cosmos DB singleton per la durata dell'applicazione**

    Istanza di Efach DocumentClient è thread-safe ed esegue la gestione delle connessioni efficiente e l'indirizzo di memorizzazione nella cache quando si opera in modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte di DocumentClient, è consigliabile usare una singola istanza di DocumentClient per ogni AppDomain per la durata dell'applicazione.

   <a id="max-connection"></a>
3. **Aumentare il valore di System.Net MaxConnections per host se si usa la modalità Gateway**.

    DB Cosmos Azure, le richieste vengono eseguite su HTTPS/REST quando si utilizza la modalità Gateway e sono soggetti al limite di connessione predefinito per ogni nome host o indirizzo IP. Si potrebbe essere necessario impostare il MaxConnections su un valore più alto (100-1000) in modo che la libreria client può utilizzare più connessioni simultanee al database di Azure Cosmos. In .NET SDK 1.8.0 e versioni successive il valore predefinito per [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50 e per modificare il valore è possibile impostare [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore più elevato.   
4. **Ottimizzazione delle query parallele per le raccolte partizionate**

     SQL .NET SDK versione alla 1.9.0 e versioni successive di query parallele di supporto, che consentono di eseguire query in una raccolta partizionata in parallelo (vedere [utilizzo con il SDK di](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) e le relative [esempi di codice](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) per altre informazioni). Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali. Mettono a disposizione due parametri che gli utenti possono ottimizzare in funzione dei requisiti, ovvero (a) MaxDegreeOfParallelism per definire il numero massimo di partizioni sulle quali è possibile eseguire query in parallelo e (b) MaxBufferedItemCount per definire il numero di risultati di prelettura.

    (a) ***Ottimizzazione di MaxDegreeOfParallelism\:*** la query parallela funziona eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. L'impostazione di MaxDegreeOfParallelism sul numero di partizioni offre quindi la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di MaxDegreeOfParallelism su un numero elevato; il sistema sceglie il numero minimo (numero di partizioni, input specificato dall'utente) come valore di MaxDegreeOfParallelism.

    È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    (b) ***Ottimizzazione di MaxBufferedItemCount\:*** la query parallela è progettata per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. La prelettura consente il miglioramento complessivo della latenza di una query. MaxBufferedItemCount è il parametro che consente di limitare il numero di risultati di prelettura. L'impostazione di MaxBufferedItemCount sul numero previsto di risultati restituiti (o un numero più alto) consente alla query di ottenere il massimo vantaggio dalla prelettura.

    La prelettura funziona allo stesso modo indipendentemente la MaxDegreeOfParallelism ed è un singolo buffer per i dati da tutte le partizioni.  
5. **Attivare GC sul lato server**

    In alcuni casi, può essere utile ridurre la frequenza di Garbage Collection. In .NET impostare [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) su true.
6. **Implementare il backoff in corrispondenza di intervalli RetryAfter**

    Durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff sulla limitazione per l'intervallo tra tentativi specificato dal server. Rispettando il backoff si garantiscono tempi di attesa minimi tra i tentativi. Supporto di criteri di ripetizione è incluso nella versione 1.8.0 e di sopra dell'istruzione SQL [.NET](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), versione alla 1.9.0 e versioni successive del [Node.js](sql-api-sdk-node.md) e [Python](sql-api-sdk-python.md), e tutte le versioni supportate del [.NET Core](sql-api-sdk-dotnet-core.md) SDK. Per altre informazioni, vedere [Superamento dei limiti della velocità effettiva riservata](request-units.md#RequestRateTooLarge) e [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
7. **Aumentare il carico di lavoro client**

    Se si sta eseguendo il test a livelli di velocità effettiva elevati (> 50.000 UR/sec), l'applicazione client può diventare un collo di bottiglia a causa della limitazione di utilizzo della CPU o della rete. Se si raggiunge questo punto, è possibile continuare a eseguire il push ulteriormente l'account di Azure Cosmos DB tramite la scalabilità alle applicazioni client in più server.
8. **Memorizzare nella cache gli URI dei documenti per una minore latenza di lettura**

    Memorizzare nella cache gli URI dei documenti quando possibile per ottenere prestazioni di lettura ottimali.
   <a id="tune-page-size"></a>
9. **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando l'esecuzione di un blocco di lettura di documenti tramite lettura feed funzionalità (ad esempio, ReadDocumentFeedAsync) o durante l'emissione di una query SQL, i risultati vengono restituiti in modo segmentato, se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Per ridurre il numero di round trip necessari per recuperare tutti i risultati in rete, è possibile aumentare la dimensione di pagina utilizzando [x-ms-max-item-count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) intestazione della richiesta a un massimo di 1000. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni di pagina a 10 in modo da ridurre la velocità effettiva usata per le letture e le query.

    È inoltre possibile impostare le dimensioni di pagina utilizzando Azure Cosmos DB SDK disponibili.  Ad esempio: 

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Aumentare il numero di thread/attività**

    Vedere [Aumentare il numero di thread/attività](#increase-threads) nella sezione Rete.

11. **Usare processi host a 64 bit**

    il SDK SQL funziona in un processo host a 32 bit quando si utilizza SQL .NET SDK versione 1.11.4 e versioni successive. Tuttavia, se si usano query tra partizioni, è consigliabile usare l'elaborazione dell'host a 64 bit per migliorare le prestazioni. I tipi seguenti di applicazioni dispongono di un processo host a 32 bit per impostazione predefinita; per passare a un processo a 64 bit, seguire questi passaggi in base al tipo di applicazione:

    - Per le applicazioni eseguibili è possibile, a tale scopo, deselezionare l'opzione **Preferisci 32 bit** nella scheda **Compila** della finestra **Proprietà progetto**.

    - Per progetti di test basati su VSTest questa operazione può essere eseguita selezionando **Test**->**Impostazioni test**->**Default Processor Architecture as X64** (Imposta architettura processore X64 come predefinita) dall'opzione **Visual Studio Test**.

    - Per le applicazioni Web ASP.NET distribuite in locale questa operazione può essere eseguita selezionando **Utilizzare la versione a 64 bit di IIS Express per progetti e siti Web** in **Strumenti**->**Opzioni**->**Progetti e soluzioni**->**Progetti Web**.

    - Per le applicazioni Web ASP.NET distribuite in Azure questa operazione può essere eseguita selezionando **Platform as 64-bit** (Piattaforma 64 bit) in **Impostazioni applicazione** nel Portale di Azure.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
1. **Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di Cosmos DB consentono anche di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati.  Ad esempio, il codice seguente illustra come escludere un'intera sezione dei documenti, detta anche sottoalbero, dall'indicizzazione usando il carattere jolly "*".

    ```C#
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

1. **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    DB Cosmos Azure offre un'ampia gamma di operazioni di database, incluse le query relazionale e gerarchiche con funzioni definite dall'utente, stored procedure e trigger: funzionamento nei documenti all'interno di una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning di velocità effettiva in base al numero di [unità richieste](request-units.md) impostare per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive. 

    La complessità di una query influisce sulla quantità di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (creare, aggiornare o eliminare), controllare il [x-ms-richiesta addebito](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) intestazione (o la proprietà RequestCharge equivalente in ResourceResponse<T> o FeedResponse<T> nel. NET SDK) per misurare il numero di unità di richiesta utilizzato da queste operazioni.

    ```C#
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
    ```             

    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning, ad esempio 2000 UR/secondo. Se, ad esempio, la query precedente restituisce 1000 documenti da 1 KB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare le richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server modalità preemptive terminerà la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituire il [x-ms-nuovo tentativo-dopo-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) intestazione che indica la quantità di tempo, espresso in millisecondi, che l'utente deve attendere prima di ripetere la richiesta.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se più client operano collettivamente in modo costante al di sopra della frequenza delle richieste, il numero di ripetizioni dei tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando RetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza.  La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).
3. **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi
Per un'applicazione di esempio consentono di valutare Azure Cosmos DB per scenari ad alte prestazioni in alcuni computer client, vedere [prestazioni e scalabilità test con Azure Cosmos DB](performance-testing.md).

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e scalabilità in Azure Cosmos DB](partition-data.md).
