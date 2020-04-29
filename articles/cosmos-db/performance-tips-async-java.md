---
title: Suggerimenti sulle prestazioni di Azure Cosmos DB per Async Java
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos database
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: b892b1f4ff73679ab425d0e97f5361e0f3712252
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549194"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e Async Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una chiamata al metodo SDK. Dato che si accede ad Azure Cosmos DB tramite chiamate di rete, è tuttavia possibile introdurre ottimizzazioni sul lato client per ottenere prestazioni ottimali quando si usa [SQL Async Java SDK](sql-api-sdk-async-java.md).

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete

* **Modalità di connessione: usare la modalità diretta**
<a id="direct-connection"></a>
    
    Il modo in cui un client si connette a Azure Cosmos DB ha implicazioni importanti sulle prestazioni, soprattutto in termini di latenza lato client. *ConnectionMode* è un'impostazione di configurazione chiave disponibile per la configurazione del client *ConnectionPolicy*. Per Async Java SDK, i due ConnectionMode disponibili sono:  
      
    * [Gateway (impostazione predefinita)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Connessione diretta](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    La modalità Gateway è supportata in tutte le piattaforme SDK ed è l'opzione configurata per impostazione predefinita. Se le applicazioni vengono eseguite all'interno di una rete aziendale con restrizioni rigide del firewall, la modalità Gateway è la scelta migliore perché usa la porta HTTPS standard e un singolo endpoint. A livello di prestazioni, tuttavia, la modalità Gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. Per questo motivo, la modalità diretta offre prestazioni migliori a causa del minor numero di hop di rete.

    *ConnectionMode* viene configurato durante la costruzione dell'istanza *DocumentClient* con il parametro *ConnectionPolicy* .
    
    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **Colloca i client nella stessa area di Azure per le prestazioni**<a id="same-region"></a>

    Quando possibile, inserire tutte le applicazioni che chiamano Azure Cosmos DB nella stessa area del database di Azure Cosmos. Per un confronto approssimativo, le chiamate ad Azure Cosmos DB eseguite nella stessa area vengono completate entro 1-2 millisecondi, mentre la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 millisecondi. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Uso dell'SDK
* **Installare l'SDK più recente**

    Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-async-java.md).

* **Usare un singleton Azure Cosmos DB client per la durata dell'applicazione**

    Ogni istanza di AsyncDocumentClient è thread-safe ed esegue la gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte di AsyncDocumentClient, è consigliabile usare una singola istanza di AsyncDocumentClient per ogni AppDomain per la durata dell'applicazione.

   <a id="max-connection"></a>

* **Ottimizzazione di ConnectionPolicy**

    Per impostazione predefinita, la modalità diretta Cosmos DB richieste vengono effettuate su TCP quando si usa async Java SDK. Internamente l'SDK usa un'architettura speciale in modalità diretta per gestire in modo dinamico le risorse di rete e ottenere prestazioni ottimali.

    In Async Java SDK, la modalità diretta è la scelta migliore per migliorare le prestazioni del database con la maggior parte dei carichi di lavoro. 

    * ***Panoramica della modalità diretta***

        ![Illustrazione dell'architettura della modalità diretta](./media/performance-tips-async-java/rntbdtransportclient.png)

        L'architettura lato client utilizzata in modalità diretta consente l'utilizzo di rete prevedibile e l'accesso in multiplex alle repliche Azure Cosmos DB. Il diagramma precedente illustra il modo in cui la modalità diretta instrada le richieste dei client alle repliche nel back-end Cosmos DB. L'architettura della modalità diretta alloca fino a 10 **canali** sul lato client per ogni replica di database. Un canale è una connessione TCP preceduta da un buffer di richiesta, ovvero 30 richieste approfondite. I canali appartenenti a una replica vengono allocati dinamicamente in base alle esigenze dell' **endpoint di servizio**della replica. Quando l'utente invia una richiesta in modalità diretta, il **TransportClient** instrada la richiesta all'endpoint di servizio appropriato in base alla chiave di partizione. La **coda di richieste** memorizza nel buffer le richieste prima dell'endpoint del servizio.

    * ***Opzioni di configurazione di ConnectionPolicy per la modalità diretta***

        Come primo passaggio, utilizzare le seguenti impostazioni di configurazione consigliate. Se si verificano problemi relativi a questo particolare argomento, contattare il [team di Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

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

    * ***Suggerimenti per la programmazione per la modalità diretta***

        Esaminare l'articolo sulla risoluzione dei problemi di Azure Cosmos DB [Async Java SDK](troubleshoot-java-async-sdk.md) come base di riferimento per la risoluzione di eventuali problemi asincroni di Java SDK.

        Alcuni suggerimenti importanti per la programmazione quando si usa la modalità diretta:

        + **Usare il multithreading nell'applicazione per un trasferimento dei dati TCP efficiente** : dopo aver eseguito una richiesta, l'applicazione deve sottoscrivere la ricezione dei dati in un altro thread. In caso contrario, viene forzata l'operazione "half-duplex" imprevista e le richieste successive vengono bloccate in attesa della risposta della richiesta precedente.

        + **Eseguire carichi di lavoro a elevato utilizzo di calcolo in un thread dedicato** : per motivi simili al suggerimento precedente, le operazioni quali l'elaborazione dei dati complessi vengono posizionate in modo ottimale in un thread separato. Una richiesta che estrae i dati da un altro archivio dati, ad esempio se il thread USA contemporaneamente Azure Cosmos DB e gli archivi dati Spark, può riscontrare un aumento della latenza ed è consigliabile generare un thread aggiuntivo che attenda una risposta dall'altro archivio dati.

            + L'i/o di rete sottostante in Async Java SDK è gestito da Netty. vedere questi [Suggerimenti per evitare i modelli di codifica che bloccano i thread di io Netty](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).

        + **Modellazione dei dati** : il contratto di Azure Cosmos DB presuppone che le dimensioni del documento siano inferiori a 1 KB. Per ottimizzare il modello di dati e la programmazione in modo da ottimizzare le dimensioni del documento, è in genere possibile ridurre la latenza. Se è necessario l'archiviazione e il recupero di documenti di dimensioni maggiori di 1 KB, l'approccio consigliato consiste nel collegare i documenti ai dati nell'archiviazione BLOB di Azure.


* **Ottimizzazione delle query parallele per le raccolte partizionate**

    Azure Cosmos DB SQL Async Java SDK supporta le query parallele, che consentono di eseguire query su una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) correlati all'utilizzo con gli SDK. Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali.

    * ***Ottimizzazione di setMaxDegreeOfParallelism\:***
    
        Le query parallele funzionano eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. Usare pertanto setMaxDegreeOfParallelism per impostare il numero di partizioni con la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di setMaxDegreeOfParallelism su un numero elevato. Il sistema sceglie il numero minimo (numero di partizioni, input specificato dall'utente) come livello di parallelismo massimo.

        È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    * ***Ottimizzazione di Setmaxbuffereditemcount sul\:***
    
        La query parallela è progettata per recuperare in anticipo i risultati mentre il batch di risultati corrente viene elaborato dal client. La prelettura consente il miglioramento complessivo della latenza di una query. setMaxBufferedItemCount consente di limitare il numero di risultati di prelettura. L'impostazione di setMaxBufferedItemCount sul numero previsto di risultati restituiti (o un numero più alto) consente alla query di ottenere il massimo vantaggio dalla prelettura.

        La prelettura funziona allo stesso modo indipendentemente dall'impostazione di MaxDegreeOfParallelism e dalla presenza di un solo buffer per i dati di tutte le partizioni.

* **Implementare il backoff in base agli intervalli definiti dal parametro getRetryAfterInMilliseconds**

    Durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff per l'intervallo tra tentativi specificato dal server. Rispettando il backoff si garantiscono tempi di attesa minimi tra i tentativi.

* **Aumentare il carico di lavoro client**

    Se si sta eseguendo il test a livelli di velocità effettiva elevati (> 50.000 UR/sec), l'applicazione client può diventare un collo di bottiglia a causa della limitazione di uso della CPU o della rete. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.

* **Usare l'indirizzamento basato sul nome**

    Usare l'indirizzamento basato sul nome, in cui il formato dei collegamenti è `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, invece dei selflink (\_self), il cui formato è `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, per evitare di recuperare l'ID di tutte le risorse usate per costruire il collegamento. Inoltre, poiché queste risorse vengono ricreate (possibilmente con stesso nome), la relativa memorizzazione nella cache potrebbe essere controproducente.

   <a id="tune-page-size"></a>

* **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura, ad esempio readDocuments, oppure quando si esegue una query SQL, i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni di pagina usando l'intestazione di richiesta [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fino a 1000. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni di pagina a 10 in modo da ridurre la velocità effettiva usata per le letture e le query.

    È anche possibile impostare le dimensioni di pagina usando il metodo setMaxItemCount.

* **Usare l'utilità di pianificazione appropriata (evitare l'acquisizione di thread Netty di I/O EventLoop)**

    Async Java SDK usa [Netty](https://netty.io/) per le operazioni di I/O non di blocco. L'SDK usa un numero fisso di thread Netty di I/O EventLoop (corrispondente al numero di core della CPU del computer) per l'esecuzione di operazioni di I/O. La sequenza Observable restituita dall'API genera il risultato in uno dei thread Netty di I/O EventLoop condiviso. Per questo motivo è importante non bloccare i thread Netty di I/O EventLoop condivisi. L'esecuzione di operazioni con utilizzo intensivo della CPU oppure di operazioni di blocco sul thread Netty di I/O EventLoop può causare deadlock o ridurre in modo significativo la velocità effettiva dell'SDK.

    Ad esempio il codice seguente esegue un'operazione con utilizzo intensivo della CPU sul thread Netty di I/O EventLoop:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Dopo aver ricevuto il risultato, se si vogliono eseguire operazioni con utilizzo intensivo della CPU sul risultato è consigliabile evitare di procedere su thread Netty di I/O EventLoop. In alternativa, è possibile specificare la propria utilità di pianificazione per fornire il proprio thread per eseguire il lavoro.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    In base al tipo di lavoro è necessario usare l'utilità di pianificazione RxJava esistente appropriata. Leggere qui [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Per altre informazioni, vedere la [pagina di GitHub](https://github.com/Azure/azure-cosmosdb-java) per Async Java SDK.

* **Disabilitare la registrazione di Netty**

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

* **Usare l'implementazione di TLS/SSL nativa per Netty**

    Netty può usare direttamente OpenSSL per lo stack di implementazione TLS per ottenere prestazioni migliori. In assenza di questa configurazione Netty verrà eseguito il fallback all'implementazione TLS predefinita di Java.

    In Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    e aggiungere la dipendenza seguente alle dipendenze Maven del progetto:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Per le altre piattaforme (Red Hat, Windows, Mac e così via), fare riferimento a queste istruzioni https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
* **Escludi i percorsi inutilizzati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di Azure Cosmos DB consentono di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (setIncludedPaths e setExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Il codice seguente, ad esempio, Mostra come escludere un'intera sezione dei documenti (noto anche come sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

* **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    Azure Cosmos DB offre un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostato per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive.

    La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (create, update o delete), esaminare l'intestazione [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) per determinare il numero di unità richiesta usate da queste operazioni. È anche possibile esaminare la proprietà RequestCharge equivalente in ResourceResponse\<t> o FeedResponse\<t>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning. Se, ad esempio, sono presenti 2000 UR/secondo e se la query precedente restituisce 1000 documenti da 1 kB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers), che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se più client operano collettivamente in modo costante al di sopra della frequenza delle richieste, il numero di ripetizioni dei tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando setRetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).

* **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
