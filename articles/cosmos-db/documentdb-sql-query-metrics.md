---
title: Metriche di query SQL per l'API DocumentDB di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come instrumentare ed eseguire il debug delle prestazioni delle query SQL per le richieste di Azure Cosmos DB.
keywords: sintassi sql, query sql, linguaggio di query json, concetti relativi ai database e query sql, funzioni di aggregazione
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: c6c929c568cf7246c2c2e414723a38429727df36
ms.contentlocale: it-it
ms.lasthandoff: 08/17/2017

---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ottimizzazione delle prestazioni delle query con Azure Cosmos DB
Azure Cosmos DB fornisce un'[API SQL per le query sui dati](documentdb-sql-query.md), senza che siano necessari schemi o indici secondari. In questo articolo vengono fornite le seguenti informazioni per gli sviluppatori:

* Informazioni generali sull'esecuzione delle query SQL di Azure Cosmos DB
* Dettagli sulle intestazioni di richiesta e risposta delle query e le opzioni SDK client
* Suggerimenti e procedure consigliate per le prestazioni delle query
* Esempi di come usare le statistiche di esecuzione di SQL per eseguire il debug delle prestazioni delle query

## <a name="about-sql-query-execution"></a>Informazioni sull'esecuzione di query SQL

In Azure Cosmos DB i dati vengono archiviati in contenitori, che possono raggiungere [dimensioni di archiviazione o velocità effettive delle richieste](partition-data.md) illimitate. Azure Cosmos DB esegue automaticamente il ridimensionamento dei dati tra le partizioni fisiche per gestire la crescita dei dati o l'aumento della velocità effettiva con provisioning. È possibile eseguire query SQL su qualsiasi contenitore usando l'API REST o uno dei [SDK di DocumentDB](documentdb-sdk-dotnet.md) supportati.

Una breve panoramica del partizionamento: si definisce una chiave di partizione come "city", che determina il modo in cui vengono suddivisi i dati tra le partizioni fisiche. I dati che appartengono a una singola chiave di partizione (ad esempio, "city" == "Seattle") vengono archiviati in una partizione fisica, ma in genere una singola partizione fisica contiene più chiavi di partizione. Quando una partizione raggiunge la dimensione di archiviazione, il servizio suddivide automaticamente la partizione in due nuove partizioni e divide la chiave di partizione in modo uniforme tra queste partizioni. Poiché le partizioni sono temporanee, le API usano un'astrazione "intervallo di chiavi di partizione", che indica gli intervalli degli hash delle chiavi di partizione. 

Quando si esegue una query in Azure Cosmos DB, l'SDK esegue i passaggi logici seguenti:

* La query SQL viene analizzata per determinare il piano di esecuzione della query. 
* Se la query include un filtro con la chiave di partizione, ad esempio `SELECT * FROM c WHERE c.city = "Seattle"`, viene indirizzata a una singola partizione. Se la query non contiene un filtro per la chiave di partizione, viene eseguita in tutte le partizioni e i risultati vengono uniti sul lato client.
* La query viene eseguita in ogni partizione in serie o in parallelo, in base alla configurazione client. All'interno di ogni partizione, la query può richiedere uno o più round trip, a seconda della complessità della query, delle dimensioni di pagina configurate e della velocità effettiva con provisioning della raccolta. Ogni esecuzione restituisce il numero di [unità richiesta](request-units.md) usate dall'esecuzione della query e, facoltativamente, le statistiche sull'esecuzione della query. 
* L'SDK riepiloga i risultati della query tra le partizioni. Ad esempio, se la query implica una clausola ORDER BY tra le partizioni, i risultati dalle singole partizioni vengono uniti e ordinati per restituire i risultati in base all'ordinamento globale. Se la query è un'aggregazione come `COUNT`, i conteggi relativi alle singole partizioni vengono sommati per produrre il conteggio globale.

L'SDK offre varie opzioni per l'esecuzione di query. Ad esempio, in .NET queste opzioni sono disponibili nella classe `FeedOptions`. La tabella seguente illustra queste opzioni e il relativo impatto sul tempo di esecuzione delle query. 

| Opzione | Descrizione |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Deve essere impostato su true per qualsiasi query che richiede l'esecuzione in più di una partizione. Si tratta di un flag esplicito che consente di trovare un compromesso adeguato per le prestazioni in fase di sviluppo. |
| `EnableScanInQuery` | Deve essere impostato su true se è stata rifiutata esplicitamente l'indicizzazione, ma si vuole comunque eseguire la query tramite un'analisi. Applicabile solo se l'indicizzazione per il percorso di filtro richiesto è disabilitata. | 
| `MaxItemCount` | Numero massimo di elementi da restituire per ogni round trip al server. Impostando -1, è possibile lasciare che sia il server a gestire il numero di elementi. In alternativa, è possibile ridurre questo valore per recuperare solo un numero limitato di elementi per ogni round trip. 
| `MaxBufferedItemCount` | Questa è un'opzione sul lato client e viene usata per limitare l'uso della memoria durante l'esecuzione di clausole ORDER BY tra partizioni. Un valore più alto consente di ridurre la latenza di ordinamento tra partizioni. |
| `MaxDegreeOfParallelism` | Ottiene o imposta il numero di operazioni simultanee eseguite sul lato client durante l'esecuzione di query in parallelo nel servizio database di Azure DocumentDB. Un valore di proprietà positivo limita il numero di operazioni simultanee al valore impostato. Se è impostato un valore minore di 0, il sistema decide automaticamente il numero di operazioni simultanee da eseguire. |
| `PopulateQueryMetrics` | Consente la registrazione dettagliata delle statistiche sul tempo impiegato nelle varie fasi di esecuzione della query, come il tempo di compilazione, il tempo del ciclo di indice e il tempo di caricamento dei documenti. È possibile condividere l'output delle statistiche sulle query con il supporto tecnico di Azure per la diagnostica dei problemi di prestazioni delle query. |
| `RequestContinuation` | È possibile riprendere l'esecuzione delle query passando il token di continuazione opaco restituito da qualsiasi query. Il token di continuazione incapsula tutti gli stati necessari per l'esecuzione delle query. |
| `ResponseContinuationTokenLimitInKb` | È possibile limitare la dimensione massima del token di continuazione restituito dal server. Potrebbe essere necessario eseguire questa operazione se l'host applicazione applica limiti per le dimensioni delle intestazioni delle risposte. L'impostazione di questa opzione può determinare un aumento della durata complessiva e delle unità di richieste usate per la query.  |

Supponiamo ad esempio che venga richiesta una query con la chiave di partizione su una raccolta con `/city` come chiave di partizione e con una velocità effettiva con provisioning di 100.000 unità di richieste al secondo. Questa query può essere richiesta usando `CreateDocumentQuery<T>` in .NET in modo simile al seguente:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Il frammento SDK illustrato sopra corrisponde alla richiesta dell'API REST seguente:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Ogni pagina di esecuzione della query corrisponde a un `POST` dell'API REST con l'intestazione `Accept: application/query+json` e la query SQL nel corpo. Ogni query effettua uno o più round trip al server con il token `x-ms-continuation` restituito tra il client e server per riprendere l'esecuzione. Le opzioni di configurazione in FeedOptions vengono passate al server sotto forma di intestazioni di richiesta. Ad esempio, `MaxItemCount` corrisponde a `x-ms-max-item-count`. 

La richiesta restituisce la risposta seguente (troncata per una migliore leggibilità):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Di seguito sono elencate le principali intestazioni di risposta restituite dalla query:

| Opzione | Descrizione |
| ------ | ----------- |
| `x-ms-item-count` | Il numero di elementi restituiti nella risposta. Questo dipende dal valore `x-ms-max-item-count` specificato, dal numero di elementi che possono essere contenuti nella dimensione massima del payload della risposta, dalla velocità effettiva con provisioning e dal tempo di esecuzione della query. |  
| `x-ms-continuation:` | Il token di continuazione per riprendere l'esecuzione della query, se sono disponibili altri risultati. | 
| `x-ms-documentdb-query-metrics` | Le statistiche della query per l'esecuzione. Si tratta di una stringa delimitata contenente le statistiche relative al tempo impiegato nelle varie fasi di esecuzione della query. Viene restituita se `x-ms-documentdb-populatequerymetrics` è impostato su `True`. | 
| `x-ms-request-charge` | Il numero di [unità richiesta](request-units.md) usate dalla query. | 

Per informazioni dettagliate sulle intestazioni e le opzioni delle richieste API REST, vedere [Querying resources using the DocumentDB REST API](https://docs.microsoft.com/rest/api/documentdb/querying-documentdb-resources-using-the-rest-api) (Esecuzione di query su risorse con l'API REST di DocumentDB).

## <a name="best-practices-for-query-performance"></a>Procedure consigliate per le prestazioni delle query
Di seguito sono indicati i fattori più comuni che influiscono sulle prestazioni delle query di Azure Cosmos DB. In questo articolo verrà esaminato in modo approfondito ognuno di questi argomenti.

| Fattore | Suggerimento | 
| ------ | -----| 
| Velocità effettiva con provisioning | Misurare le unità di richieste per ogni query e assicurarsi di disporre della velocità effettiva con provisioning richiesta per le query. | 
| Partizionamento e chiavi di partizione | Favorire le query con il valore della chiave di partizione nella clausola del filtro per una latenza bassa. |
| Opzioni per SDK e query | Seguire le procedure consigliate dell'SDK come la connettività diretta e ottimizzare le opzioni di esecuzione delle query sul lato client. |
| Latenza di rete | Tenere conto del sovraccarico di rete nella misurazione e usare API multihosting per eseguire la lettura dall'area più vicina. |
| Criterio di indicizzazione | Assicurarsi di disporre dei percorsi/criteri di indicizzazione necessari per la query. |
| Metriche di esecuzione delle query | Analizzare le metriche di esecuzione delle query per identificare le potenziali riscritture di forme di dati e query.  |

### <a name="provisioned-throughput"></a>Velocità effettiva con provisioning
In Cosmos DB è possibile creare contenitori di dati, ognuno con una velocità effettiva riservata espressa in unità richiesta (UR) al secondo. Una lettura di un documento di 1 KB è 1 UR e ogni operazione (incluse le query) è normalizzata secondo un numero fisso di UR in base alla relativa complessità. Se ad esempio è previsto un provisioning di 1000 UR/sec per il contenitore e si dispone di una query come `SELECT * FROM c WHERE c.city = 'Seattle'` che usa 5 UR, è possibile eseguire (1000 UR/sec) / (5 UR/query) = 200 query di questo tipo al secondo. 

Se si inviano più di 200 query al secondo, il servizio inizia a limitare le velocità delle richieste in ingresso sopra il valore di 200 query al secondo. L'SDK gestisce automaticamente questo caso eseguendo un nuovo tentativo/backoff, pertanto è possibile osservare una latenza superiore per queste query. L'aumento della velocità effettiva con provisioning al valore richiesto migliora la velocità effettiva e la latenza delle query. 

Per altre informazioni sulle unità richiesta, vedere [Unità richiesta](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partizionamento e chiavi di partizione
Con Azure Cosmos DB, in genere le query vengono eseguite nell'ordine seguente, dalla più veloce/più efficiente alla più lenta/meno efficiente. 

* GET su una singola chiave di partizione e chiave di elemento
* Query con una clausola di filtro su una singola chiave di partizione
* Query senza una clausola di filtro di uguaglianza o basato su intervallo su qualsiasi proprietà
* Query senza filtri

Le query che devono consultare tutte le partizioni comportano una latenza più elevata e possono usare più UR. Poiché ogni partizione dispone dell'indicizzazione automatica per tutte le proprietà, in questo caso la query può essere fornita in modo efficiente dall'indice. È possibile eseguire più velocemente le query che interessano diverse partizioni usando le opzioni di parallelismo.

Per altre informazioni sul partizionamento e sulle chiavi di partizione, vedere [Partizionamento in Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Opzioni per SDK e query
Per informazioni su come ottenere le migliori prestazioni sul lato client da Azure Cosmos DB, vedere [Suggerimenti sulle prestazioni](performance-tips.md) e [Test delle prestazioni](performance-testing.md). Sono inclusi l'uso degli SDK più recenti, la configurazione di impostazioni specifiche della piattaforma come il numero predefinito di connessioni, la frequenza della Garbage Collection e l'uso delle opzioni di connettività leggera come Direct/TCP. 


#### <a name="max-item-count"></a>Numero massimo di elementi
Per le query, il valore di `MaxItemCount` può avere un impatto significativo sulla durata delle query end-to-end. Ogni round trip al server restituirà un numero di elementi mai maggiore di quello specificato in `MaxItemCount` (100 elementi per impostazione predefinita). L'impostazione di un valore maggiore (-1 è il valore massimo e consigliato) migliora la durata complessiva delle query limitando il numero di round trip tra server e client, in particolare per le query con set di risultati di grandi dimensioni.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Massimo grado di parallelismo
Per le query, ottimizzare `MaxDegreeOfParallelism` per identificare le configurazioni migliori per l'applicazione, in particolare se si eseguono query tra partizioni (senza un filtro per il valore della chiave di partizione). `MaxDegreeOfParallelism` controlla il numero massimo di attività in parallelo, ovvero il numero massimo di partizioni accessibili in parallelo. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Presupponendo che
* D = Numero massimo predefinito di attività in parallelo (= numero totale di processori nel computer client)
* P = Numero massimo di attività in parallelo specificato dall'utente
* N = Numero di partizioni cui è necessario accedere per rispondere a una query

Di seguito sono indicate le implicazioni sul comportamento delle query in parallelo per diversi valori di P.
* (P == 0) => Modalità seriale
* (P == 1) => Massimo un'attività
* (P > 1) => Attività in parallelo minime (P, N) 
* (P < 1) => Attività in parallelo minime (N, D)

Per le note sulla versione degli SDK e altre informazioni sulle classi e i metodi implementati, vedere [SDK di DocumentDB](documentdb-sdk-dotnet.md)

### <a name="network-latency"></a>Latenza di rete
Per configurare la distribuzione globale e connettersi all'area più vicina, vedere [Distribuzione globale di Azure Cosmos DB](tutorial-global-distribution-documentdb.md). La latenza di rete ha un impatto significativo sulle prestazioni delle query quando è necessario eseguire più round trip o recuperare un set di risultati di grandi dimensioni dalla query. 

La sezione sulle metriche di esecuzione delle query descrive come recuperare la durata di esecuzione delle query del server ( `totalExecutionTimeInMs`), in modo da poter differenziare tra il tempo impiegato dall'esecuzione delle query e quello impiegato per il trasferimento di rete.

### <a name="indexing-policy"></a>Criterio di indicizzazione
Per informazioni su percorsi, tipi e modalità di indicizzazione e sul relativo impatto sull'esecuzione delle query, vedere [Configurazione dei criteri di indicizzazione](indexing-policies.md). Per impostazione predefinita, i criteri di indicizzazione usano l'indicizzazione hash per le stringhe, che è efficace per le query di uguaglianza, ma non per le query di intervallo o orderby. Se sono necessarie query di intervallo per le stringhe, è consigliabile specificare il tipo di indice di intervallo per tutte le stringhe. 

## <a name="query-execution-metrics"></a>Metriche di esecuzione delle query
È possibile ottenere metriche dettagliate sull'esecuzione delle query passando l'intestazione facoltativa `x-ms-documentdb-populatequerymetrics` (`FeedOptions.PopulateQueryMetrics` in .NET SDK). Il valore restituito in `x-ms-documentdb-query-metrics` contiene le seguenti coppie chiave-valore, pensate per risoluzione dei problemi più avanzati di esecuzione delle query. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metrica | Unità | Descrizione | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | millisecondi | Tempo di esecuzione della query | 
| `queryCompileTimeInMs` | millisecondi | Tempo di compilazione della query  | 
| `queryLogicalPlanBuildTimeInMs` | millisecondi | Tempo per la creazione del piano della query logica | 
| `queryPhysicalPlanBuildTimeInMs` | millisecondi | Tempo per la creazione del piano della query fisica | 
| `queryOptimizationTimeInMs` | millisecondi | Tempo impiegato per l'ottimizzazione della query | 
| `VMExecutionTimeInMs` | millisecondi | Tempo impiegato per l'esecuzione della query | 
| `indexLookupTimeInMs` | millisecondi | Tempo impiegato nel livello di indice fisico | 
| `documentLoadTimeInMs` | millisecondi | Tempo impiegato per il caricamento di documenti  | 
| `systemFunctionExecuteTimeInMs` | millisecondi | Tempo totale impiegato per l'esecuzione di funzioni di sistema (predefinite) in millisecondi  | 
| `userFunctionExecuteTimeInMs` | millisecondi | Tempo totale impiegato per l'esecuzione di funzioni definite dall'utente in millisecondi | 
| `retrievedDocumentCount` | millisecondi | Numero totale di documenti recuperati  | 
| `retrievedDocumentSize` | byte | Dimensione totale dei documenti recuperati in byte  | 
| `outputDocumentCount` | count | Numero di documenti di output | 
| `writeOutputTimeInMs` | millisecondi | Tempo di esecuzione della query in millisecondi | 
| `indexUtilizationRatio` | rapporto (<=1) | Rapporto del numero di documenti corrispondenti al filtro rispetto al numero di documenti caricati  | 

L'SDK client può eseguire internamente più operazioni di query per servire la query in ogni partizione. Il client effettua più chiamate per ogni partizione se i risultati totali superano `x-ms-max-item-count`, se la query supera la velocità effettiva con provisioning per la partizione, se il payload della query raggiunge la dimensione massima per ogni pagina oppure se la query raggiunge il limite di timeout allocato dal sistema. Ogni esecuzione parziale della query restituisce un `x-ms-documentdb-query-metrics` per la pagina. 

Di seguito sono riportate alcune query di esempio, con informazioni su come interpretare alcune delle metriche restituite dall'esecuzione delle query: 

| Query | Metrica di esempio | Descrizione | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Il numero di documenti recuperati è 100+1 per la corrispondenza alla clausola TOP. Il tempo della query viene impiegato per lo più in `WriteOutputTime` e `DocumentLoadTime` poiché si tratta di un'analisi. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount è ora superiore (500+1 per la corrispondenza alla clausola TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Vengono impiegati circa 0,9 ms in IndexLookupTime per la ricerca della chiave, perché viene eseguita una ricerca nell'indice `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Un tempo leggermente superiore (1,7 ms) viene impiegato per IndexLookupTime nel caso di un'analisi dell'intervallo, perché viene eseguita una ricerca nell'indice `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Il tempo trascorso in `DocumentLoadTime` è lo stesso delle query precedenti, ma con un valore `WriteOutputTime` inferiore perché viene eseguita la proiezione di una sola proprietà. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Vengono impiegati circa 213 ms in `UserDefinedFunctionExecutionTime` per l'esecuzione della funzione definita dall'utente su ogni valore di `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Vengono impiegati circa 0,6 ms in `IndexLookupTime` su `/Name/?`. La maggior parte del tempo di esecuzione della query (circa 7 ms) è in `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | La query viene eseguita come un'analisi perché usa `LOWER` e vengono restituiti 500 su 2491 documenti recuperati. |


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle parole chiave e gli operatori di query SQL supportati, vedere [Query SQL](documentdb-sql-query.md). 
* Per informazioni sulle unità richiesta, vedere [Unità richiesta](request-units.md).
* Per informazioni sui criteri di indicizzazione, vedere [Criteri di indicizzazione](indexing-policies.md) 



