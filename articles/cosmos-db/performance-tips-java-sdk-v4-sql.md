---
title: Suggerimenti sulle prestazioni per Azure Cosmos DB Java SDK v4
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni del database Azure Cosmos DB per Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 49827b7387edc1e914bbd58c63df2db74f4ed17b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091277"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> I suggerimenti sulle prestazioni riportati in questo articolo riguardano esclusivamente Azure Cosmos DB Java SDK v4. Per altre informazioni, vedere le [note sulla versione](sql-api-sdk-java-v4.md) di Azure Cosmos DB Java SDK v4, il [repository Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) e la [guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4. Se si usa una versione precedente, vedere l'articolo [Eseguire la migrazione ad Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento alla versione 4.
>

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una chiamata al metodo SDK. Tuttavia, dato che si accede ad Azure Cosmos DB tramite chiamate di rete, è possibile introdurre ottimizzazioni sul lato client per ottenere massime prestazioni durante l'uso di Azure Cosmos DB Java SDK v4.

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete

* **Modalità di connessione: usare la modalità diretta**
<a id="direct-connection"></a>
    
    La modalità di connessione predefinita di Java SDK è diretta. È possibile configurare la modalità di connessione nel generatore client usando i metodi *directMode ()* o *gatewayMode ()* , come illustrato di seguito. Per configurare una delle modalità con le impostazioni predefinite, chiamare uno dei metodi senza argomenti. In caso contrario, passare un'istanza della classe delle impostazioni di configurazione come argomento ( *DirectConnectionConfig* per *directMode ()* ,  *GatewayConnectionConfig* per *gatewayMode ()* . Per ulteriori informazioni sulle diverse opzioni di connettività, vedere l'articolo relativo alle [modalità di connettività](sql-sdk-connection-modes.md) .
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Asincrona](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API sincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    Il metodo *directMode ()* ha un override aggiuntivo, per il motivo seguente. Le operazioni del piano di controllo, ad esempio database e contenitore CRUD utilizzano *sempre* la modalità gateway; Quando l'utente ha configurato la modalità diretta per le operazioni del piano dati, le operazioni del piano di controllo usano le impostazioni predefinite della modalità gateway. Questa operazione è adatta alla maggior parte degli utenti. Tuttavia, gli utenti che desiderano la modalità diretta per le operazioni del piano dati e tunability i parametri della modalità gateway del piano di controllo possono usare il seguente override di *directMode ()* :

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Asincrona](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API Sync di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **Collocare i client nella stessa area di Azure per ottenere migliori prestazioni** <a id="same-region"></a>

    Quando possibile, posizionare eventuali applicazioni che chiamano Azure Cosmos DB nella stessa area del database Azure Cosmos. Per un confronto approssimativo, le chiamate ad Azure Cosmos DB eseguite nella stessa area vengono completate entro 1-2 millisecondi, mentre la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 millisecondi. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Illustrazione dei criteri di connessione di Azure Cosmos DB" border="false":::

    Un'app che interagisce con un account Azure Cosmos DB per più aree deve configurare [località preferite](tutorial-global-distribution-sql-api.md#preferred-locations) per garantire che le richieste vengano inviate a un'area collocata.

* **Abilitare la rete accelerata nella macchina virtuale di Azure per una latenza più bassa**

Per ottimizzare le prestazioni, è consigliabile seguire le istruzioni per abilitare la rete accelerata nella macchina virtuale di Azure con [Windows (fare clic per istruzioni)](../virtual-network/create-vm-accelerated-networking-powershell.md) o [Linux (fare clic per istruzioni)](../virtual-network/create-vm-accelerated-networking-cli.md).

Senza rete accelerata, le operazioni di IO che transitano tra la macchina virtuale di Azure e le altre risorse di Azure potrebbero essere instradate inutilmente attraverso un host e un commutatore virtuale posizionato tra la macchina virtuale e la relativa scheda di rete. La presenza dell'host e del commutatore virtuale inline nel percorso dei dati non solo aumenta la latenza e l'instabilità nel canale di comunicazione, ma sottrae anche cicli della CPU alla macchina virtuale. Grazie alla rete accelerata, la macchina virtuale si interfaccia direttamente con la scheda di interfaccia di rete senza intermediari. Tutti i dettagli dei criteri di rete gestiti dall'host e dal commutatore virtuale vengono ora gestiti nell'hardware della scheda di interfaccia di rete. L'host e il commutatore virtuale vengono ignorati. Quando si abilita la rete accelerata, è in genere possibile prevedere una latenza più bassa e una velocità effettiva più elevata, oltre a una maggiore latenza *coerente* e a un minore utilizzo della CPU.

Limitazioni: la rete accelerata deve essere supportata nel sistema operativo della macchina virtuale e può essere abilitata solo quando la macchina virtuale viene arrestata e deallocata. La macchina virtuale non può essere distribuita con Azure Resource Manager.

Per altri dettagli, vedere le istruzioni per [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) e [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso dell'SDK
* **Installare l'SDK più recente**

    Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-async-java.md).

* **Usare un client Azure Cosmos DB singleton per la durata dell'applicazione**

    Ogni istanza del client Azure Cosmos DB è thread-safe ed esegue la gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte del client Azure Cosmos DB, è consigliabile usare una singola istanza del client Azure Cosmos DB per ogni AppDomain per l'intera durata dell'applicazione.

   <a id="max-connection"></a>

* **Usare il livello di coerenza minimo richiesto per l'applicazione**

    Quando si crea un *CosmosClient* , la coerenza predefinita usata, se non impostata in modo esplicito, è *Session* . Se la coerenza *Session* non è richiesta dalla logica dell'applicazione, impostare *Consistency* su *Eventual* . Nota: è consigliabile impostare almeno la coerenza *Session* nelle applicazioni che usano il processore del feed di modifiche di Azure Cosmos DB.

* **Usare l'API Async per la massima velocità effettiva di cui viene effettuato il provisioning**

    Azure Cosmos DB Java SDK v4 include due API, Sync e Async. In linea generale, l'API Async implementa la funzionalità dell'SDK, mentre l'API Sync è un thin wrapper che blocca le chiamate all'API Async. Questo approccio è in contrasto con le versioni precedenti dell'SDK, ovvero Azure Cosmos DB Async Java SDK v2, che era solo asincrono, e Azure Cosmos DB Sync Java SDK v2, che era solo sincrono e aveva un'implementazione completamente separata. 
    
    La scelta dell'API viene determinata durante l'inizializzazione del client. *CosmosAsyncClient* supporta l'API Async, mentre *CosmosClient* supporta l'API Sync. 
    
    L'API Async implementa l'I/O non bloccante ed è la scelta ottimale se si ha come obiettivo di massimizzare la velocità effettiva quando vengono inviate richieste ad Azure Cosmos DB. 
    
    L'uso dell'API Sync può essere la scelta ottimale se si vuole o se è necessaria un'API che si blocchi in risposta a ogni richiesta o se il funzionamento sincrono costituisce il paradigma dominante nell'applicazione. Può ad esempio essere opportuno usare l'API Sync quando si salvano in modo permanente i dati di Azure Cosmos DB in un'applicazione di microservizi, purché la velocità effettiva non sia fondamentale.  
    
    Basta tenere presente che la velocità effettiva dell'API Sync peggiora con tempi crescenti di risposta alla richiesta, mentre l'API Async può saturare l'intera capacità di larghezza di banda dell'hardware. 
    
    La collocazione geografica può offrire una velocità effettiva più elevata e coerente quando si usa l'API Sync (vedere [Collocare i client nella stessa area di Azure per ottenere migliori prestazioni](#collocate-clients)), ma non si prevede ancora di superare la velocità effettiva ottenibile dall'API Async.

    Alcuni utenti potrebbero anche non avere familiarità con [Project Reactor](https://projectreactor.io/), il framework dei flussi reattivi usato per implementare l'API Async di Azure Cosmos DB Java SDK v4. Se questo rappresenta un problema, è consigliabile leggere la [guida introduttiva ai modelli di Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) e quindi esaminare l'[introduzione alla programmazione reattiva](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) per acquisire familiarità con questi concetti. Se si è già usato Azure Cosmos DB con un'interfaccia asincrona e l'SDK usato era Azure Cosmos DB Async Java SDK v2, è possibile che si abbia già familiarità con [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava) ma non si sappia esattamente cosa è cambiato in Project Reactor. In tal caso, consultare l'[articolo sul confronto dei framework Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) per informazioni al riguardo.

    I frammenti di codice seguenti mostrano come inizializzare il client di Azure Cosmos DB rispettivamente per il funzionamento dell'API Async o dell'API Sync:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Asincrona](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API Sync di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **Ottimizzazione di ConnectionPolicy**

    Per impostazione predefinita, quando si usa Azure Cosmos DB Java SDK v4, le richieste Cosmos DB in modalità diretta vengono effettuate tramite TCP. La modalità diretta interna usa un'architettura speciale per gestire in modo dinamico le risorse di rete e ottenere prestazioni ottimali.

    In Azure Cosmos DB Java SDK v4 la modalità diretta è la scelta ottimale per migliorare le prestazioni del database con la maggior parte dei carichi di lavoro. 

    * ***Panoramica della modalità diretta** _

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Illustrazione dei criteri di connessione di Azure Cosmos DB" border="false":::

        L'architettura sul lato client usata in modalità diretta consente l'utilizzo di rete prevedibile e l'accesso in multiplex alle repliche di Azure Cosmos DB. Il diagramma precedente mostra in che modo la modalità diretta instrada le richieste dei client alle repliche nel back-end di Cosmos DB. L'architettura della modalità diretta alloca fino a 10 _ *canali* * sul lato client per replica di database. Un canale è una connessione TCP preceduta da un buffer di richieste, con una profondità di 30 richieste. I canali appartenenti a una replica vengono allocati dinamicamente in base alle esigenze dall' **endpoint di servizio** della replica. Quando l'utente invia una richiesta in modalità diretta, il **TransportClient** instrada la richiesta all'endpoint di servizio appropriato in base alla chiave di partizione. La **coda delle richieste** memorizza le richieste nel buffer prima dell'endpoint di servizio.

    * ***Opzioni di configurazione per la modalità diretta** _

        Se si desidera un comportamento della modalità diretta non predefinito, creare un'istanza di _DirectConnectionConfig * e personalizzarne le proprietà, quindi passare l'istanza della proprietà personalizzata al metodo *directMode ()* nel generatore client Azure Cosmos DB.

        Queste impostazioni di configurazione controllano il comportamento dell'architettura della modalità diretta sottostante descritta sopra.

        Come primo passaggio, usare le seguenti impostazioni di configurazione consigliate. Queste opzioni di *DirectConnectionConfig* sono impostazioni di configurazione avanzate che possono influenzare le prestazioni dell'SDK in modi imprevisti. si consiglia agli utenti di evitarne la modifica, a meno che non si ritengano molto utili per comprendere i compromessi ed è assolutamente necessario. In caso di problemi relativi a questo particolare argomento, contattare il [team di Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com).

        | Opzione di configurazione       | Predefinito   |
        | :------------------:       | :-----:   |
        | idleConnectionTimeout      | "PT0"     |
        | maxConnectionsPerEndpoint  | "130"     |
        | connectTimeout             | "PT5S"    |
        | idleEndpointTimeout        | PT1H    |
        | maxRequestsPerConnection   | 30      |

* **Ottimizzazione delle query parallele per le raccolte partizionate**

    Azure Cosmos DB Java SDK v4 supporta le query parallele, che consentono l'esecuzione di query su una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) relativi all'uso di Azure Cosmos DB Java SDK v4. Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali.

    * ***Ottimizzazione di \: setMaxDegreeOfParallelism** _
    
        Le query parallele funzionano eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. Usare pertanto setMaxDegreeOfParallelism per impostare il numero di partizioni con la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di setMaxDegreeOfParallelism su un numero elevato. Il sistema sceglie il numero minimo (numero di partizioni, input specificato dall'utente) come livello di parallelismo massimo.

        È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    _ * **Ottimizzazione setmaxbuffereditemcount sul \:** _
    
        Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. The pre-fetching helps in overall latency improvement of a query. setMaxBufferedItemCount limits the number of pre-fetched results. Setting setMaxBufferedItemCount to the expected number of results returned (or a higher number) enables the query to receive maximum benefit from pre-fetching.

        Pre-fetching works the same way irrespective of the MaxDegreeOfParallelism, and there is a single buffer for the data from all partitions.

_ **Scalabilità orizzontale del carico di lavoro del client**

    If you are testing at high throughput levels, the client application may become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

    A good rule of thumb is not to exceed >50% CPU utilization on any given server, to keep latency low.

   <a id="tune-page-size"></a>

* **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura (ad esempio, *readItems* ) oppure quando si esegue una query SQL ( *queryItems* ), i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Si supponga che l'applicazione esegua una query su Azure Cosmos DB e che richieda il set completo di risultati della query per completare l'attività. Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare la dimensione di pagina usando il campo dell'intestazione della richiesta [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). 

    * Per le query su partizione singola, l'impostazione del valore del campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) su -1 (nessun limite per la dimensione di pagina) ha l'effetto di aumentare al massimo la latenza riducendo al minimo il numero di pagine di risposta della query. In questo modo, il set di risultati completo verrà restituito in una singola pagina oppure, se la query richiede più tempo rispetto all'intervallo di timeout, il set di risultati completo verrà restituito nel minor numero di pagine possibile. Questa operazione consente di ridurre il tempo di round trip della richiesta.
    
    * Per le query su più partizioni, l'impostazione del campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) su -1 e la rimozione del limite della dimensione di pagina rischiano di sovraccaricare l'SDK con dimensioni di pagina non gestibili. Nel caso di più partizioni, è consigliabile aumentare la dimensione di pagina fino a un valore elevato ma comunque finito, ad esempio 1000, per ridurre la latenza. 
    
    In alcune applicazioni, potrebbe non essere necessario restituire il set completo dei risultati della query. Nei casi in cui si devono visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre la dimensione di pagina a 10 in modo da limitare la velocità effettiva utilizzata per le letture e le query.

    È anche possibile impostare l'argomento relativo alla dimensione di pagina preferita del metodo *byPage* invece di modificare direttamente il campo dell'intestazione REST. Tenere presente che [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) o l'argomento della dimensione di pagina preferita di *byPage* imposta solo un limite massimo per la dimensione di pagina, non un requisito assoluto. Pertanto, per diversi motivi, è possibile che le pagine di Azure Cosmos DB restituite risultino di dimensione inferiore rispetto a quella preferita. 

* **Usare l'utilità di pianificazione appropriata (evitare l'acquisizione di thread Netty di I/O EventLoop)**

    La funzionalità asincrona di Azure Cosmos DB Java SDK si basa sull'I/O non bloccante [Netty](https://netty.io/). L'SDK usa un numero fisso di thread Netty di I/O EventLoop (corrispondente al numero di core della CPU del computer) per l'esecuzione di operazioni di I/O. La sequenza Flux restituita dall'API genera il risultato in uno dei thread Netty di I/O EventLoop condiviso. Per questo motivo è importante non bloccare i thread Netty di I/O EventLoop condivisi. L'esecuzione di operazioni con utilizzo intensivo della CPU oppure di operazioni di blocco sul thread Netty di I/O EventLoop può causare deadlock o ridurre in modo significativo la velocità effettiva dell'SDK.

    Ad esempio il codice seguente esegue un'operazione con utilizzo intensivo della CPU sul thread Netty di I/O EventLoop:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>API Async di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    Dopo aver ricevuto il risultato, se si vogliono eseguire operazioni con utilizzo intensivo della CPU sul risultato è consigliabile evitare di procedere su thread Netty di I/O EventLoop. È invece possibile fornire un'utilità di pianificazione personalizzata per fornire il proprio thread per l'esecuzione del lavoro, come illustrato di seguito (richiede `import reactor.core.scheduler.Schedulers` ).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API Async di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    In base al tipo di lavoro è necessario usare l'utilità di pianificazione Reactor esistente appropriata. Per altre informazioni, vedere qui [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html).

    Per altre informazioni su Azure Cosmos DB Java SDK v4, vedere la [directory Cosmos DB del mono repository di Azure SDK per Java su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Ottimizzare le impostazioni di registrazione nell'applicazione**

    Per diversi motivi, può essere utile o necessario aggiungere la registrazione in un thread che genera una velocità effettiva elevata della richiesta. Se l'obiettivo è quello di saturare completamente, con le richieste generate da questo thread, la velocità effettiva di un contenitore di cui viene effettuato il provisioning, le ottimizzazioni della registrazione possono migliorare significativamente le prestazioni.

    * ***Configurare un logger asincrono** _

        La latenza di un logger sincrono implica necessariamente il calcolo della latenza complessiva del thread che genera richieste. È consigliabile un logger asincrono, ad esempio [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0), per separare l'overhead di registrazione dai thread dell'applicazione a prestazioni elevate.

    _ * **Disabilitare la registrazione di Netty** _

        Netty library logging is chatty and needs to be turned off (suppressing sign in the configuration may not be enough) to avoid additional CPU costs. If you are not in debugging mode, disable netty's logging altogether. So if you are using log4j to remove the additional CPU costs incurred by ``org.apache.log4j.Category.callAppenders()`` from netty add the following line to your codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 _ **Limite risorse file aperti del sistema operativo**
 
    Alcuni sistemi Linux (ad esempio Red Hat) prevedono un limite massimo per il numero di file aperti e quindi per il numero totale di connessioni. Eseguire il comando seguente per visualizzare i limiti correnti:

    ```bash
    ulimit -a
    ```

    Il numero di file aperti (nofile) deve essere sufficientemente grande da disporre di spazio sufficiente per le dimensioni del pool di connessioni configurate e per altri file aperti dal sistema operativo. Può essere modificato per consentire dimensioni del pool di connessioni più grandi.

    Aprire il file limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Aggiungere/modificare le righe seguenti:

    ```
    * - nofile 100000
    ```

* **Specificare la chiave della partizione nelle scritture di punti**

    Per migliorare le prestazioni delle scritture di punti, specificare la chiave della partizione dell'elemento nella chiamata all'API per le scritture di punti, come mostrato di seguito:

    # <a name="async"></a>[Async](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API Sync di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    anziché fornire solo l'istanza dell'elemento, come mostrato di seguito:

    # <a name="async"></a>[Async](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API Sync di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    Quest'ultima è supportata, ma aggiungerà latenza all'applicazione. L'SDK deve analizzare l'elemento ed estrarre la chiave della partizione.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
* **Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di Azure Cosmos DB consentono di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (setIncludedPaths e setExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Il codice seguente, ad esempio, illustra come includere ed escludere intere sezioni dei documenti (noti anche come sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

* **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    Azure Cosmos DB offre un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostato per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive.

    La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (create, update o delete), esaminare l'intestazione [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) per determinare il numero di unità richiesta usate da queste operazioni. È anche possibile esaminare la proprietà RequestCharge equivalente in ResourceResponse\<T> o FeedResponse\<T>.

    # <a name="async"></a>[Asincrona](#tab/api-async)

    API asincrona Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Sincronizzazione](#tab/api-sync)

    API Sync di Java SDK v4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning. Se, ad esempio, sono presenti 2000 UR/secondo e se la query precedente restituisce 1000 documenti da 1 kB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers), che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se più client operano cumulativamente in modo costante al di sopra della frequenza delle richieste, il numero di ripetizioni dei tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genererà un'eccezione *CosmosClientException* con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando setRetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione *CosmosClientException* con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).

* **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni. In teoria, l'applicazione e i flussi di lavoro devono essere progettati in modo che la dimensione dell'elemento sia pari a ~1 kB o di un ordine di grandezza simile. Per le applicazioni sensibili alla latenza è consigliabile evitare elementi di grandi dimensioni. I documenti di più MB rallenteranno l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partitioning-overview.md).
