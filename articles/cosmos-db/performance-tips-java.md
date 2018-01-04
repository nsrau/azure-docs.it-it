---
title: Suggerimenti sulle prestazioni di DB Cosmos Azure per Java | Documenti Microsoft
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni del database Azure Cosmos DB
keywords: Come migliorare le prestazioni del database
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: ca16a7fe424e9c50ce87b150442dd18ff0d6ce91
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

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Suggerimenti sulle prestazioni per database di Azure Cosmos e Java
Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche di architettura principali o scrivere codici complessi per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API o una [chiamata al metodo SDK](set-throughput.md#set-throughput-java). Tuttavia, poiché Azure Cosmos DB è possibile accedere tramite chiamate di rete sono ottimizzazioni sul lato client è possibile apportare a ottenere prestazioni ottimali, quando si utilizza il [SDK per Java SQL](documentdb-sdk-java.md).

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete
<a id="direct-connection"></a>

1. **Modalità di connessione: utilizzare DirectHttps**

    La modalità con cui un client si connette al database di Azure Cosmos ha implicazioni molto importanti sulle prestazioni, specie in termini di latenza client-side osservata. Impostazione disponibile per la configurazione del client di una configurazione chiave [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) : [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  I due ConnectionModes disponibili sono:

   1. [Gateway (impostazione predefinita)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    Modalità di gateway è supportata in tutte le piattaforme SDK ed è il valore configurato predefinito.  Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni del firewall strict, Gateway è la scelta migliore, poiché Usa la porta HTTPS standard e un singolo endpoint. Il compromesso di prestazioni, tuttavia, è che la modalità Gateway implica un hop di rete aggiuntiva ogni volta i dati vengono letti o scritti nel database di Azure Cosmos. Per questo motivo, la modalità DirectHttps offre prestazioni migliori a causa di un minor numero di hop di rete. 

    il SDK per Java usa HTTPS come protocollo di trasporto. HTTPS Usa SSL per l'autenticazione iniziale e la crittografia del traffico. Quando si utilizza il SDK per Java, deve essere aperta solo la porta HTTPS 443. 

    ConnectionMode viene configurato durante la costruzione dell'istanza di DocumentClient con il parametro ConnectionPolicy. 

    ```Java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
    }
        
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Collocare i client nella stessa area di Azure per ottenere prestazioni migliori**

    Quando possibile, inserire tutte le applicazioni che chiamano DB Cosmos Azure nella stessa area del database di Azure Cosmos DB. Per un confronto approssimativo, le chiamate al database di Azure Cosmos nella stessa area completata entro 1-2 ms, ma la latenza tra l'occidentale e costa orientale degli Stati Uniti è > 50 ms. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. La latenza più bassa possibile scopo è necessario garantire che l'applicazione chiamante si trova nella stessa area di Azure come endpoint Azure Cosmos DB provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Uso dell'SDK
1. **Installare l'SDK più recente**

    Azure Cosmos DB SDK sono costantemente aggiornate per garantire prestazioni ottimali. Vedere il [Azure Cosmos DB SDK](documentdb-sdk-java.md) pagine per determinare il SDK più recente, esaminare i miglioramenti.
2. **Utilizzare un client di Azure Cosmos DB singleton per la durata dell'applicazione**

    Ogni [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) istanza è thread-safe e che esegue la gestione di connessioni efficiente e indirizzo di memorizzazione nella cache quando si opera in modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte di DocumentClient, è consigliabile usare una singola istanza di DocumentClient per ogni AppDomain per la durata dell'applicazione.

   <a id="max-connection"></a>
3. **Aumentare MaxPoolSize per ogni host quando si utilizza la modalità Gateway**

    DB Cosmos Azure, le richieste vengono eseguite su HTTPS/REST quando si utilizza la modalità Gateway e sono soggetti al limite di connessione predefinito per ogni nome host o indirizzo IP. Si potrebbe essere necessario impostare il MaxPoolSize su un valore più alto (200-1000) in modo che la libreria client può utilizzare più connessioni simultanee al database di Azure Cosmos. In Java SDK, il valore predefinito per [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.gsetmaxpoolsize) è 100. Utilizzare [setMaxPoolSize]( https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) per modificare il valore.

4. **Ottimizzazione delle query parallele per le raccolte partizionate**

    Azure Cosmos DB SQL Java SDK versione alla 1.9.0 e versioni successive di query parallele di supporto, che consentono di eseguire query in una raccolta partizionata in parallelo (vedere [utilizzo con il SDK di](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) e le relative [esempi di codice](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) per altre informazioni). Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali.

    (a) ***ottimizzazione setMaxDegreeOfParallelism\:***  parallelo query eseguendo una query su più partizioni in parallelo. Tuttavia, da una raccolta partizionata singoli vengono recuperati in modo seriale rispetto alla query. In tal caso, utilizzare [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) per impostare il numero di partizioni che ha la possibilità di raggiungere più query ad alte prestazioni, massima fornite tutte le altre condizioni di sistema rimangono invariati. Se non si conosce il numero di partizioni, è possibile utilizzare setMaxDegreeOfParallelism per impostare un numero elevato e il sistema viene scelto il valore minimo (numero di partizioni, l'input dell'utente fornito) come massimo grado di parallelismo. 

    È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    (b) ***ottimizzazione setMaxBufferedItemCount\:***  query parallele è progettata per recuperare preventivamente risultati durante l'elaborazione batch corrente di risultati dal client. La prelettura consente il miglioramento complessivo della latenza di una query. setMaxBufferedItemCount limita il numero di risultati già recuperati. Impostando [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) per il numero previsto di risultati restituiti (o un numero più alto), in questo modo la query ricevere il massimo vantaggio dalla prelettura.

    La prelettura funziona allo stesso modo indipendentemente la MaxDegreeOfParallelism ed è un singolo buffer per i dati da tutte le partizioni.  

5. **Implementare backoff getRetryAfterInMilliseconds intervalli**

    Durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff sulla limitazione per l'intervallo tra tentativi specificato dal server. Rispettando il backoff si garantiscono tempi di attesa minimi tra i tentativi. Supporto di criteri di ripetizione è incluso nella versione 1.8.0 e di sopra del [SDK per Java](documentdb-sdk-java.md). Per ulteriori informazioni, vedere [Exceeding riservato i limiti di velocità effettiva](request-units.md#RequestRateTooLarge) e [getRetryAfterInMilliseconds](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Aumentare il carico di lavoro client**

    Se si sta testando a livelli di velocità effettiva elevata (> 50.000 UR/sec), l'applicazione client potrebbe diventare il collo di bottiglia a causa di computer coprendo all'utilizzo della CPU o rete. Se si raggiunge questo punto, è possibile continuare a eseguire il push ulteriormente l'account di Azure Cosmos DB tramite la scalabilità alle applicazioni client in più server.

7. **Utilizzare l'indirizzamento del nome di base**

    Utilizzare l'indirizzamento basato su nome, in cui i collegamenti hanno il formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, anziché SelfLinks (_self), che hanno il formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` per evitare di recuperare l'ID della risorsa di tutte le risorse utilizzate per costruire il collegamento. Inoltre, poiché queste risorse ottengano ricreate (possibilmente con stesso nome), la memorizzazione nella cache di questi potrebbe non aiutare.

   <a id="tune-page-size"></a>
8. **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

    Quando l'esecuzione di un blocco lettura di documenti tramite lettura feed funzionalità (ad esempio, [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) o quando l'esecuzione di una query SQL, i risultati vengono restituiti in modo segmentato, se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

    Per ridurre il numero di round trip necessari per recuperare tutti i risultati in rete, è possibile aumentare la dimensione di pagina utilizzando la [x-ms-max-item-count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) intestazione della richiesta a un massimo di 1000. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni di pagina a 10 in modo da ridurre la velocità effettiva usata per le letture e le query.

    È inoltre possibile impostare la dimensione di pagina utilizzando l'il [setPageSize metodo](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
1. **Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

    Criteri di indicizzazione del DB Cosmos Azure consentono di specificare che i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando l'indicizzazione di percorsi ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) e [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati.  Ad esempio, il codice seguente illustra come escludere un'intera sezione dei documenti, detta anche sottoalbero, dall'indicizzazione usando il carattere jolly "*".

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

1. **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    DB Cosmos Azure offre un'ampia gamma di operazioni di database, incluse le query relazionale e gerarchiche con funzioni definite dall'utente, stored procedure e trigger: funzionamento nei documenti all'interno di una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning di velocità effettiva in base al numero di [unità richieste](request-units.md) impostare per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive. 

    La complessità di una query ha un impatto quante unità di richiesta vengono utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (creare, aggiornare o eliminare), controllare il [x-ms-richiesta addebito](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) intestazione (o la proprietà RequestCharge equivalente in [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) o [FeedResponse<T> ](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_response) per misurare il numero di unità di richiesta utilizzato da queste operazioni.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    L'addebito richiesta restituito in questa intestazione è una frazione di velocità effettiva del provisioning. Ad esempio, se si dispone di 2000 UR/sec è eseguito il provisioning e se la query precedente restituisce documenti con 1000 1KB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare le richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server modalità preemptive terminerà la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituire il [x-ms-nuovo tentativo-dopo-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) intestazione che indica la quantità di tempo, espresso in millisecondi, che l'utente deve attendere prima di ripetere la richiesta.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se si dispone di più di un client in modo cumulativo operano in modo coerente di sopra la frequenza delle richieste, il numero di tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente; In questo caso, il client genera un [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) con stato 429 all'applicazione del codice. Il numero di tentativi predefinito può essere modificato utilizzando [setRetryOptions](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) sul [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) istanza. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza.  La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).
3. **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito di richiesta (il costo di elaborazione della richiesta) di un'operazione specificata è direttamente correlato alla dimensione del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla progettazione dell'applicazione per la scalabilità e prestazioni elevate, vedere [partizionamento e la scalabilità in Azure Cosmos DB](partition-data.md).
