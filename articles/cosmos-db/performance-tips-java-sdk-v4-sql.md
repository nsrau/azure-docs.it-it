---
title: Suggerimenti sulle prestazioni per Azure Cosmos DB Java SDK v4
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos database per Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982531"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB Java SDK v4

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> I suggerimenti sulle prestazioni in questo articolo sono destinati solo a Azure Cosmos DB Java SDK v4. Per altre informazioni, vedere le note sulla versione di Azure Cosmos DB Java SDK v4, il [repository maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e la [Guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4. Se attualmente si usa una versione precedente alla V4, vedere la pagina relativa alla [migrazione a Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento a V4.
>

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una chiamata al metodo SDK. Tuttavia, poiché è possibile accedere a Azure Cosmos DB tramite chiamate di rete, è possibile apportare ottimizzazioni lato client per ottenere prestazioni ottimali quando si usa Azure Cosmos DB Java SDK v4.

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete

* **Modalità di connessione: usare la modalità diretta**
<a id="direct-connection"></a>
    
    Il modo in cui un client si connette a Azure Cosmos DB ha implicazioni importanti sulle prestazioni, soprattutto in termini di latenza lato client. *ConnectionMode* è un'impostazione di configurazione chiave disponibile per la configurazione del client *ConnectionPolicy*. Per Azure Cosmos DB Java SDK v4, i due *ConnectionMode*disponibili sono:  
      
    * [Gateway (impostazione predefinita)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Connessione diretta](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Questi *ConnectionMode*essenzialmente condizionano la route richiesta dal computer client alle partizioni nel back-end Azure Cosmos DB. In genere, la modalità diretta è l'opzione preferita per ottenere prestazioni ottimali, consentendo al client di aprire le connessioni TCP direttamente alle partizioni nel Azure Cosmos DB back-end e inviare le richieste *Direct*ly senza intermediario. Al contrario, in modalità Gateway, le richieste effettuate dal client vengono instradate a un cosiddetto server "gateway" nel Azure Cosmos DB front-end, che a sua volta fan le richieste alle partizioni appropriate nel back-end del Azure Cosmos DB. Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni rigide del firewall, la modalità Gateway è la scelta migliore perché usa la porta HTTPS standard e un singolo endpoint. Il compromesso in termini di prestazioni, tuttavia, è che la modalità Gateway prevede un hop di rete aggiuntivo (da client a gateway e da gateway a partizione) ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. Per questo motivo, la modalità diretta offre prestazioni migliori a causa del minor numero di hop di rete.

    *ConnectionMode* viene configurato durante la costruzione dell'istanza client di Azure Cosmos DB con il parametro *ConnectionPolicy* :
    
   #### <a name="async"></a>[Async](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronizza](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>API di sincronizzazione Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **Colloca i client nella stessa area di Azure per le prestazioni**<a id="same-region"></a>

    Quando possibile, inserire tutte le applicazioni che chiamano Azure Cosmos DB nella stessa area del database di Azure Cosmos. Per un confronto approssimativo, le chiamate ad Azure Cosmos DB eseguite nella stessa area vengono completate entro 1-2 millisecondi, mentre la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 millisecondi. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/same-region.png)

    Un'app che interagisce con un account Azure Cosmos DB per più aree deve configurare le [posizioni preferite]() per garantire che le richieste vengano inviate a un'area collocata.

* **Abilitare la rete accelerata nella VM di Azure per una latenza più bassa.**

Per ottimizzare le prestazioni, è consigliabile seguire le istruzioni per abilitare la rete accelerata nelle [finestre (fare clic per istruzioni)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) o [Linux (fare clic per le istruzioni)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

Senza rete accelerata, i/o che transitano tra la macchina virtuale di Azure e altre risorse di Azure possono essere instradati inutilmente tramite un host e un commutire virtuale situati tra la macchina virtuale e la relativa scheda di rete. Il fatto che l'host e il commutire virtuale inline nel percorso di DataPath non solo aumentino la latenza e l'instabilità nel canale di comunicazione, inoltre, ruba i cicli della CPU dalla macchina virtuale. Grazie alla rete accelerata, la macchina virtuale si interfaccia direttamente con la scheda di interfaccia di rete senza intermediari; tutti i dettagli dei criteri di rete gestiti dall'host e dal Commuter virtuale vengono ora gestiti nell'hardware della scheda di interfaccia di rete. l'host e il commutire virtuale sono ignorati. In genere è possibile prevedere una latenza più bassa e una velocità effettiva più elevata, nonché una latenza più *coerente* e un utilizzo ridotto della CPU quando si Abilita la rete accelerata.

Limitazioni: la rete accelerata deve essere supportata nel sistema operativo della macchina virtuale e può essere abilitata solo quando la macchina virtuale viene arrestata e deallocata. La macchina virtuale non può essere distribuita con Azure Resource Manager.

Per ulteriori informazioni, vedere le istruzioni per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) e [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

## <a name="sdk-usage"></a>Uso dell'SDK
* **Installare l'SDK più recente**

    Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-async-java.md).

* **Usare un singleton Azure Cosmos DB client per la durata dell'applicazione**

    Ogni istanza del client Azure Cosmos DB è thread-safe ed esegue una gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte del client di Azure Cosmos DB, è consigliabile usare una singola istanza del client di Azure Cosmos DB per AppDomain per la durata dell'applicazione.

   <a id="max-connection"></a>

* **Usare il livello di coerenza minimo richiesto per l'applicazione**

    Quando si crea un *CosmosClient*, la coerenza predefinita utilizzata se non impostata in modo esplicito è *Session*. Se la coerenza di *sessione* non è richiesta dalla logica dell'applicazione, impostare la *coerenza* su *finale*. Nota: è consigliabile usare almeno la coerenza di *sessione* nelle applicazioni che utilizzano il processore del feed di modifiche Azure Cosmos DB.

* **Usare l'API Async per la massima velocità effettiva con provisioning**

    Azure Cosmos DB Java SDK v4 combina due API, Sync e Async. Approssimativamente, l'API Async implementa la funzionalità SDK, mentre l'API di sincronizzazione è un thin wrapper che blocca le chiamate all'API asincrona. Questo approccio è a contrasto con la versione precedente di Azure Cosmos DB Async Java SDK v2, che era solo asincrona, e con il precedente Azure Cosmos DB Sync Java SDK v2, che era solo di sincronizzazione e aveva un'implementazione completamente separata. 
    
    La scelta dell'API viene determinata durante l'inizializzazione del client. un *CosmosAsyncClient* supporta l'API asincrona mentre un *COSMOSCLIENT* supporta l'API di sincronizzazione. 
    
    L'API Async implementa i/o non bloccanti ed è la scelta ottimale se l'obiettivo è quello di massimizzare la velocità effettiva quando si inviano richieste a Azure Cosmos DB. 
    
    L'uso dell'API di sincronizzazione può essere la scelta migliore se si vuole o si necessita di un'API che blocchi la risposta a ogni richiesta o se l'operazione sincrona è il paradigma dominante nell'applicazione. Ad esempio, è possibile che l'API di sincronizzazione quando si salvano in modo permanente i dati Azure Cosmos DB in un'applicazione di microservizi, la velocità effettiva fornita non è fondamentale.  
    
    È sufficiente tenere presente che la velocità effettiva dell'API di sincronizzazione peggiora con tempi di risposta di richiesta crescenti, mentre l'API asincrona può saturare le capacità di larghezza di banda completa dell'hardware. 
    
    La collocazione geografica può offrire una velocità effettiva più elevata e coerente quando si usa l'API di sincronizzazione (vedere [la posizione dei client nella stessa area di Azure per le prestazioni](#collocate-clients)) ma non è ancora previsto che superi la velocità effettiva ottenibile dall'API asincrona.

    Alcuni utenti potrebbero anche non avere familiarità con il [Reactor del progetto](https://projectreactor.io/), il Framework dei flussi reattivi usato per implementare Azure Cosmos DB API asincrona di Java SDK v4. Se si tratta di un problema, è consigliabile leggere la Guida introduttiva al [modello di reattore](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) , quindi esaminare questa [Introduzione alla programmazione reattiva](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) per familiarizzare. Se è già stato usato Azure Cosmos DB con un'interfaccia asincrona e l'SDK usato è stato Azure Cosmos DB Async Java SDK v2, è possibile che si abbia familiarità con[RxJava](https://github.com/ReactiveX/RxJava) di [reactivex](http://reactivex.io/)/, ma non si è certi di cosa sia cambiato nel reattore del progetto. In tal caso, consultare la [Guida Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) per acquisire familiarità con.

    I frammenti di codice seguenti illustrano come inizializzare l'operazione client di Azure Cosmos DB per l'API asincrona o l'API di sincronizzazione, rispettivamente:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronizza](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>API di sincronizzazione Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **Ottimizzazione di ConnectionPolicy**

    Per impostazione predefinita, la modalità diretta Cosmos DB richieste vengono effettuate su TCP quando si usa Azure Cosmos DB Java SDK v4. Internamente l'SDK usa un'architettura speciale in modalità diretta per gestire in modo dinamico le risorse di rete e ottenere prestazioni ottimali.

    In Azure Cosmos DB Java SDK v4 la modalità diretta è la scelta migliore per migliorare le prestazioni del database con la maggior parte dei carichi di lavoro. 

    * ***Panoramica della modalità diretta***

        ![Illustrazione dell'architettura della modalità diretta](./media/performance-tips-async-java/rntbdtransportclient.png)

        L'architettura lato client utilizzata in modalità diretta consente l'utilizzo di rete prevedibile e l'accesso in multiplex alle repliche Azure Cosmos DB. Il diagramma precedente illustra il modo in cui la modalità diretta instrada le richieste dei client alle repliche nel back-end Cosmos DB. L'architettura della modalità diretta alloca fino a 10 **canali** sul lato client per ogni replica di database. Un canale è una connessione TCP preceduta da un buffer di richiesta, ovvero 30 richieste approfondite. I canali appartenenti a una replica vengono allocati dinamicamente in base alle esigenze dell' **endpoint di servizio**della replica. Quando l'utente invia una richiesta in modalità diretta, il **TransportClient** instrada la richiesta all'endpoint di servizio appropriato in base alla chiave di partizione. La **coda di richieste** memorizza nel buffer le richieste prima dell'endpoint del servizio.

    * ***Opzioni di configurazione di ConnectionPolicy per la modalità diretta***

        Queste impostazioni di configurazione controllano il comportamento dell'architettura RNTBD che regola il comportamento dell'SDK in modalità diretta.
        
        Come primo passaggio, utilizzare le seguenti impostazioni di configurazione consigliate. Queste opzioni di *ConnectionPolicy* sono impostazioni di configurazione avanzate che possono influenzare le prestazioni dell'SDK in modi imprevisti. si consiglia agli utenti di evitarne la modifica, a meno che non si ritengano molto utili per comprendere i compromessi ed è assolutamente necessario. Se si verificano problemi relativi a questo particolare argomento, contattare il [team di Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

        Se si utilizza Azure Cosmos DB come database di riferimento (ovvero, il database viene utilizzato per molte operazioni di lettura dei punti e alcune operazioni di scrittura), potrebbe essere accettabile impostare *idleEndpointTimeout* su 0 (ovvero senza timeout).


        | Opzione di configurazione       | Predefinito    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | "PT1M"     |
        | idleChannelTimeout         | PT0S     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | PT5S     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution     | "PT 0,5 S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | PT15S    |

* **Ottimizzazione delle query parallele per le raccolte partizionate**

    Azure Cosmos DB Java SDK V4 supporta le query parallele, che consentono di eseguire query in una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) relativi all'uso di Azure Cosmos DB Java SDK v4. Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali.

    * ***Ottimizzazione di setMaxDegreeOfParallelism\:***
    
        Le query parallele funzionano eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. Usare pertanto setMaxDegreeOfParallelism per impostare il numero di partizioni con la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di setMaxDegreeOfParallelism su un numero elevato. Il sistema sceglie il numero minimo (numero di partizioni, input specificato dall'utente) come livello di parallelismo massimo.

        È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    * ***Ottimizzazione di Setmaxbuffereditemcount sul\:***
    
        La query parallela è progettata per recuperare in anticipo i risultati mentre il batch di risultati corrente viene elaborato dal client. La prelettura consente il miglioramento complessivo della latenza di una query. setMaxBufferedItemCount consente di limitare il numero di risultati di prelettura. L'impostazione di setMaxBufferedItemCount sul numero previsto di risultati restituiti (o un numero più alto) consente alla query di ottenere il massimo vantaggio dalla prelettura.

        La prelettura funziona allo stesso modo indipendentemente dall'impostazione di MaxDegreeOfParallelism e dalla presenza di un solo buffer per i dati di tutte le partizioni.

* **Aumentare il carico di lavoro client**

    Se si esegue il test a livelli di velocità effettiva elevati, è possibile che l'applicazione client diventi il collo di bottiglia a causa della riduzione dell'utilizzo della CPU o della rete da parte del computer. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.

    Una regola empirica efficace consiste nel non superare >50% di utilizzo della CPU in un determinato server, per ridurre la latenza.

   <a id="tune-page-size"></a>

* **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura (ad esempio, *readItems*) o quando si emette una query SQL (*queryItems*), i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Si supponga che l'applicazione verifichi una query per Azure Cosmos DB e si supponga che l'applicazione richieda il set completo di risultati della query per completare l'attività. Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni della pagina regolando il campo di intestazione della richiesta [x-ms-max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) . 

    * Per le query a partizione singola, la regolazione del valore del campo [x-ms-max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) su-1 (nessun limite per le dimensioni di pagina) ingrandisce la latenza riducendo al minimo il numero di pagine di risposta alle query: il set di risultati completo verrà restituito in una singola pagina o se la query richiede più tempo rispetto all'intervallo di timeout, il set di risultati completo verrà restituito nel numero minimo Questa operazione consente di risparmiare sui multipli del tempo di round trip della richiesta.
    
    * Per le query tra partizioni, l'impostazione del campo [x-ms-max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) su-1 e la rimozione del limite di dimensioni della pagina rischiano di sovraccaricare l'SDK con dimensioni di pagina non gestibili. Nel caso di una partizione incrociata è consigliabile aumentare le dimensioni della pagina fino a un valore elevato ma finito, ad esempio 1000, per ridurre la latenza. 
    
    In alcune applicazioni, potrebbe non essere necessario il set completo di risultati della query. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni della pagina a 10 per ridurre la velocità effettiva utilizzata per le letture e le query.

    È anche possibile impostare l'argomento delle dimensioni di pagina preferite del metodo *byPage* , invece di modificare direttamente il campo dell'intestazione Rest. Tenere presente che [x-ms-max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) o l'argomento di dimensioni di pagina preferito di *byPage* stanno solo impostando un limite superiore per le dimensioni della pagina, non un requisito assoluto. per diversi motivi, è possibile che vengano visualizzate Azure Cosmos DB pagine restituite di dimensioni inferiori rispetto a quelle della pagina preferita. 

* **Usare l'utilità di pianificazione appropriata (evitare l'acquisizione di thread Netty di I/O EventLoop)**

    La funzionalità asincrona di Azure Cosmos DB SDK Java è basata sull'i/o non di blocco [Netty](https://netty.io/) . L'SDK usa un numero fisso di thread Netty di I/O EventLoop (corrispondente al numero di core della CPU del computer) per l'esecuzione di operazioni di I/O. Il Flux restituito dall'API genera il risultato su uno dei thread netty del ciclo di eventi IO condivisi. Per questo motivo è importante non bloccare i thread Netty di I/O EventLoop condivisi. L'esecuzione di operazioni con utilizzo intensivo della CPU oppure di operazioni di blocco sul thread Netty di I/O EventLoop può causare deadlock o ridurre in modo significativo la velocità effettiva dell'SDK.

    Ad esempio il codice seguente esegue un'operazione con utilizzo intensivo della CPU sul thread Netty di I/O EventLoop:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Dopo aver ricevuto il risultato, se si vogliono eseguire operazioni con utilizzo intensivo della CPU sul risultato è consigliabile evitare di procedere su thread Netty di I/O EventLoop. È invece possibile fornire un'utilità di pianificazione personalizzata per fornire un thread personalizzato per l'esecuzione del lavoro, come illustrato di seguito.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    In base al tipo di lavoro, è necessario usare l'utilità di pianificazione del reattore esistente appropriata per il lavoro. Leggere qui [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html).

    Per ulteriori informazioni su Azure Cosmos DB Java SDK v4, consultare la [directory Cosmos DB di Azure SDK per monorepo Java su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Ottimizzare le impostazioni di registrazione nell'applicazione**

    Per diversi motivi, può essere utile o necessario aggiungere la registrazione in un thread che genera una velocità effettiva elevata della richiesta. Se l'obiettivo è quello di saturare completamente la velocità effettiva con provisioning di un contenitore con le richieste generate da questo thread, le ottimizzazioni della registrazione possono migliorare significativamente le prestazioni.

    * ***Configurare un logger asincrono***

        La latenza di un logger sincrono implica necessariamente il calcolo della latenza complessiva del thread che genera richieste. È consigliabile un logger asincrono, ad esempio [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) , per separare l'overhead di registrazione dai thread dell'applicazione a prestazioni elevate.

    * ***Disabilitare la registrazione di Netty***

        La registrazione della libreria Netty è loquace e deve essere disattivata (l'eliminazione della configurazione potrebbe non essere sufficiente) per evitare costi aggiuntivi della CPU. Se non si è in modalità di debug, disabilitare del tutto la registrazione di Netty. Pertanto, se si usa log4j per evitare i costi della CPU aggiuntivi causati da ``org.apache.log4j.Category.callAppenders()`` da Netty aggiungere la riga seguente alla codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **Limite delle risorse dei file aperti del sistema operativo**
 
    Alcuni sistemi Linux, ad esempio Red Hat, hanno un limite superiore per il numero di file aperti e quindi il numero totale di connessioni. Eseguire il comando seguente per visualizzare i limiti correnti:

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

* **Specificare la chiave di partizione nelle Scritture del punto**

    Per migliorare le prestazioni delle Scritture di punti, specificare la chiave di partizione dell'elemento nella chiamata all'API Point Write, come illustrato di seguito:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[Sincronizza](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>API di sincronizzazione Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    anziché fornire solo l'istanza dell'elemento, come illustrato di seguito:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[Sincronizza](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>API di sincronizzazione Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    syncContainer.createItem(item);
    ```

    ---

    Quest'ultimo è supportato, ma aggiungerà latenza all'applicazione; l'SDK deve analizzare l'elemento ed estrarre la chiave di partizione.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
* **Escludi i percorsi inutilizzati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di Azure Cosmos DB consentono di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (setIncludedPaths e setExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Il codice seguente, ad esempio, Mostra come escludere un'intera sezione dei documenti (noto anche come sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

* **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    Azure Cosmos DB offre un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostato per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive.

    La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (create, update o delete), esaminare l'intestazione [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) per determinare il numero di unità richiesta usate da queste operazioni. È anche possibile esaminare la proprietà RequestCharge equivalente in ResourceResponse\<t> o FeedResponse\<t>.

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[Sincronizza](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>API di sincronizzazione Java SDK v4 (Maven com. Azure:: Azure-Cosmos)    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning. Se, ad esempio, sono presenti 2000 UR/secondo e se la query precedente restituisce 1000 documenti da 1 kB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers), che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se si dispone di più di un client che funziona cumulativamente in modo costante al di sopra della frequenza delle richieste, il numero di tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. in questo caso, il client genera un *CosmosClientException* con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando setRetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, la *CosmosClientException* con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).

* **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni. Idealmente, progettare l'applicazione e i flussi di lavoro in modo che le dimensioni dell'elemento siano pari a ~ 1 KB, o ordine o grandezza simile. Per le applicazioni sensibili alla latenza è consigliabile evitare che i documenti con più MB rallentino l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
