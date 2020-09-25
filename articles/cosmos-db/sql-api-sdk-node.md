---
title: 'Azure Cosmos DB: API Node.js di SQL, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API Node.js di SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 8334f8bbbdb6cad210839fc724c46834f559bf91
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324368"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK per l'API SQL: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring data V2](sql-api-sdk-java-spring-v2.md)
> * [Spring data V3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

|Risorsa  |Collegamento  |
|---------|---------|
|Download dell'SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentazione sull'API  |  [Documentazione di riferimento per JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Istruzioni per l'installazione dell'SDK  |  [Istruzioni di installazione](https://github.com/Azure/azure-sdk-for-js)
|Contribuire all'SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Esempi | [Esempi di codice Node.js](sql-api-nodejs-samples.md)
| Esercitazione introduttiva | [Introduzione a JavaScript SDK](sql-api-nodejs-get-started.md)
| Esercitazione sull'app Web | [Creare un'applicazione Web Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
| Piattaforma attualmente supportata | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK versione 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK versione 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK versione 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK versione 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/) - SDK versione 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/) - SDK versione 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) - SDK versione 1.x.x

## <a name="release-notes"></a>Note sulla versione

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* ResponseContinuationTokenLimitInKB impostato sul valore predefinito 1 KB. Per impostazione predefinita, questo valore viene limitato a 1 KB per evitare intestazioni troppo lunghe (Node.js ha un limite globale di dimensioni dell'intestazione). Un utente può impostare questo campo in modo da consentire intestazioni più lunghe, che possono aiutare il back-end a ottimizzare l'esecuzione delle query.
* Parametro disableSSLVerification rimosso. Questa opzione include nuove alternative descritte in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Consentire a initialHeaders di impostare in modo esplicito l'intestazione della chiave di partizione
* Usare package.json#files per impedire la pubblicazione di file estranei
* Correggere l'errore di ordinamento della mappa di routing nella versione precedente di node+v8
* È stato corretto il bug che si verificava quando l'utente specificava opzioni di ripetizione dei tentativi parziali

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Impedisci a Webpack di risolvere i moduli chiamati con require

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* È stato risolto un bug sospeso da tempo in cui le unità riservate risultavano sempre 0 per le query di aggregazione

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 Versione 3! 🎉 Molte nuove funzionalità, correzioni di bug e alcune modifiche di rilievo. Obiettivi principali di questa versione:

* Implementare nuove funzionalità importanti
  * Query DISTINCT
  * Query LIMIT/OFFSET
  * Richieste annullabili dall'utente
* Eseguire l'aggiornamento alla versione più recente dell'API REST Cosmos, in cui tutti i contenitori hanno scalabilità illimitata
* Semplificare l'uso di Cosmos dal browser
* Conformarsi meglio alle nuove linee guida di Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guida alla migrazione per modifiche di rilievo
##### <a name="improved-client-constructor-options"></a>Ottimizzazione delle opzioni del costruttore client

Sono state semplificate le opzioni del costruttore:

* masterKey è stata rinominata key e spostata nel primo livello
* Le proprietà che in precedenza si trovavano in options.auth sono state spostate nel primo livello

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>API QueryIterator semplificata
Nella versione 2 erano disponibili molti modi diversi per scorrere o recuperare i risultati di una query. Si è provato a semplificare l'API v3 e a rimuovere API simili o duplicate:

* Sono stati rimossi iterator.next() e iterator.current(). È stato usato fetchNext() per ottenere pagine di risultati.
* È stato rimosso iterator.forEach(). Vengono usati invece iteratori asincroni.
* iterator.executeNext() è stato rinominato in iterator.fetchNext()
* iterator.toArray() è stato rinominato in iterator.fetchAll()
* Le pagine sono ora oggetti Response appropriati anziché oggetti JS semplici
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>I contenitori fissi sono ora partizionati
Il servizio Cosmos supporta ora chiavi di partizione in tutti i contenitori, inclusi quelli precedentemente creati come contenitori fissi. L'SDK v3 è stato aggiornato alla versione più recente dell'API che implementa questa modifica senza determinare un'interruzione. Se non si specifica una chiave di partizione per le operazioni, viene automaticamente usata una chiave di sistema compatibile con tutti i contenitori e i documenti esistenti.

##### <a name="upsert-removed-for-stored-procedures"></a>È stato rimosso l'upsert per le stored procedure
In precedenza, era possibile usare l'upsert per le raccolte non partizionate, ma con l'aggiornamento della versione dell'API, tutte le raccolte sono ora partizionate e, di conseguenza, l'upsert è stato completamente rimosso.

##### <a name="item-reads-will-not-throw-on-404"></a>La lettura di elementi non genera più l'eccezione 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Scritture in più aree predefinita
Per impostazione predefinita, l'SDK scriverà ora in più aree, se supportato dalla configurazione di Cosmos. In precedenza, per questo comportamento era necessario il consenso esplicito.

##### <a name="proper-error-objects"></a>Oggetti errore appropriati
Le richieste non riuscite generano ora errori o sottoclassi di errore appropriati. In precedenza, generavano oggetti JS semplici.

#### <a name="new-features"></a>Nuove funzionalità
##### <a name="user-cancelable-requests"></a>Richieste annullabili dall'utente
Con il passaggio al fetch interno, è possibile usare l'API AbortController del browser per supportare le operazioni annullabili dall'utente. Nel caso di un'operazione in cui sono potenzialmente in corso più richieste (ad esempio, query tra più partizioni), tutte le richieste per l'operazione verranno annullate. Gli utenti dei browser moderni hanno già a disposizione AbortController. Gli utenti di Node.js dovranno invece usare una libreria polyfill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Impostazione della velocità effettiva inclusa nell'operazione di creazione del database/contenitore
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
La generazione del token di intestazione è stata suddivisa in una nuova libreria, @azure/cosmos-sign. Chiunque chiami direttamente l'API REST di Cosmos può usarla per firmare le intestazioni usando lo stesso codice chiamato in @azure/cosmos.

##### <a name="uuid-for-generated-ids"></a>UUID per ID generati
Nella versione 2 era disponibile codice personalizzato per la generazione di ID elemento. È stata eseguita la transizione all'UUID della libreria della community, già noto e correttamente gestito.

##### <a name="connection-strings"></a>Stringhe di connessione
È ora possibile passare una stringa di connessione copiata dal portale di Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Esperienza browser migliorata
Sebbene fosse possibile usare l'SDK v2 nel browser, non era un'esperienza ottimale. È necessario compilare più librerie node.js predefinite e usare un bundler come Webpack o pacchetto. Con l'SDK V3 l'esperienza d'uso del browser risulta considerevolmente migliorata.

* Sono stati sostituiti alcuni elementi interni request con fetch (n. 245)
* È stato rimosso l'utilizzo del buffer (n. 330)
* È stato eliminato l'utilizzo predefinito del nodo a favore di pacchetti/API universali (#328)
* Si è passati a node-abort-controller (n. 294)

#### <a name="bug-fixes"></a>Correzioni di bug
* È stata corretta la lettura di offerte e sono stati ripristinati i test delle offerte (#224)
* È stato corretto EnableEndpointDiscovery (n. 207)
* Sono state corrette le unità riservate nei risultati impaginati (n. 360)
* È stato esteso il tipo di parametro delle query SQL (n. 346)
* È stata aggiunta la durata TTL a ItemDefinition (n. 341)
* Sono state corrette le metriche delle query CP (n. 311)
* È stato aggiunto activityId a FeedResponse (n. 293)
* Il tipo_ts è stato convertito da stringa a numero (n. 252)(n. 295)
* È stata corretta l'aggregazione di addebiti delle richieste (n. 289)
* Sono state consentite chiavi di partizione di stringhe vuote (n. 277)
* È stata aggiunta una stringa al tipo di query dei conflitti (n. 237)
* È stato aggiunto uniqueKeyPolicy al contenitore (n. 234)

#### <a name="engineering-systems"></a>Sistemi di ingegneria
Non sempre riguardano le modifiche più visibili, ma consentono al personale di fornire più rapidamente codice migliore.

* È stato usato il rollup per le build di produzione (n. 104)
* È stato eseguito l'aggiornamento a Typescript 3.5 (n. 327)
* È stata eseguita la conversione a riferimenti di progetti TS. È stata estratta la cartella di test (n. 270)
* Sono stati abilitati noUnusedLocals e noUnusedParameters (n. 275)
* File YAML di Azure Pipelines per build CI (n. 298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Nessuna modifica del codice. È stato corretto un problema per cui nel pacchetto 2.1.4 erano stati inclusi alcuni file in eccesso.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* È stato corretto il processo di failover a livello di area nell'ambito dei criteri di ripetizione
* È stata corretta la proprietà ChangeFeed hasMoreResults
* Sono state aggiornate le dipendenze di sviluppo
* È stato aggiunto PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Il tipo_ts è stato convertito da stringa a numero
* Sono stati corretti i test di indicizzazione predefiniti
* È stato eseguito il backport di uniqueKeyPolicy alla versione 2
* Sono state apportate correzioni a livello di demo e debug delle demo

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* È stato eseguito il backport delle correzioni delle offerte dal ramo V3
* È stato corretto un bug nella firma di tipo executeNext()
* Sono stati corretti errori di battitura

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* È stata eseguita la ristrutturazione della build. È stato consentito il pull della versione dell'SDK in fase di compilazione.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nuove funzionalità
* È stato aggiunto il supporto per ChangeFeed (#196)
* È stato aggiunto il datatype MultiPolygon per l'indicizzazione (n. 191)
* È stata aggiunta la proprietà "key" al costruttore come alias per masterKey (#202)

#### <a name="fixes"></a>Correzioni
* È stato corretto il bug per cui next() restituiva un valore errato nell'iteratore

#### <a name="engineering-improvements"></a>Miglioramenti di progettazione
* È stato aggiunto un test di integrazione per l'utilizzo di typescript (n. 199)
* È stata abilitata l'installazione diretta da GitHub (n. 194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Aggiunge l'interfaccia per il tipo di agente del nodo. Gli utenti di Typescript non devono più installare @types/node come dipendenza
* Le posizioni preferite vengono ora rispettate correttamente
* Miglioramenti apportati alla documentazione degli sviluppatori
* Varie correzioni di errori di battitura

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Risolve il problema di definizione dei tipi introdotto nella versione 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Rimuove la dipendenza `big-integer`
* Passa alle direttive di riferimento per il tipo AsyncIterable. Gli utenti di Typescript non devono più personalizzare la loro impostazione "lib".
* Correzione di errori di battitura

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Correzione dei collegamenti Leggimi

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Correzione dell'implementazione dell'interfaccia di ripetizione dei tentativi

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Disponibilità generale della versione 2.0.0 di JavaScript SDK
* Aggiunta di supporto per le scritture in più aree.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 della versione 2.0.0 di JavaScript SDK per l'anteprima pubblica.
* Nuovo modello a oggetti con il modulo CosmosClient di livello superiore e metodi suddivisi tra le relative classi del database, del contenitore e dell'elemento. 
* Supporto per [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertito in TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* Correzione della documentazione di npm.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Aggiunta del supporto per nuovi tentativi predefiniti in caso di problemi di connessione.
* Aggiunta del supporto per la lettura del feed delle modifiche della raccolta.
* Risoluzione di un bug di coerenza della sessione che causava in modo intermittente un errore di sessione di lettura non disponibile.
* Aggiunta del supporto per le metriche di query.
* Modifica del numero massimo di connessioni dell'agente HTTP.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.
* Aggiunta del supporto per l'impostazione proxyUrl in ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Correzione secondaria per i sistemi di file con distinzione tra maiuscole e minuscole.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Aggiunge il supporto per la coerenza di sessione.
* Questa versione dell'SDK richiede la versione più recente dell'[emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Query indivisibili su più partizioni.
* Aggiunge il supporto per il collegamento a una risorsa con barra iniziale e finale (e relativi test).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    Correzione della documentazione di npm.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Correzione di un bug in executeStoredProcedure per cui i documenti coinvolti contenevano caratteri Unicode speciali (LS, PS).
* Correzione di un bug nella gestione di documenti con caratteri Unicode nella chiave di partizione.
* Correzione del supporto per la creazione di raccolte con i supporti di memorizzazione dei nomi. Problema GitHub n. 114.
* Correzione del supporto per il token di autorizzazione. Problema GitHub n. 178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Aggiunta del supporto per nuovi [livelli di coerenza](consistency-levels.md) denominati ConsistentPrefix.
* Aggiunta del supporto per UriFactory.
* Correzione di un bug del supporto Unicode. Problema GitHub n. 171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta dell'opzione per il controllo del grado di parallelismo per query nella partizione.
* Aggiunta dell'opzione per disabilitare la verifica TLS durante l'esecuzione sull'emulatore Azure Cosmos DB.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Correzione del bug del token di continuazione per la raccolta a partizione singola. Problema GitHub n. 107.
* Correzione del bug executeStoredProcedure nella gestione di 0 come parametro singolo. Problema GitHub n. 155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Corretta l'intestazione agente-utente per includere la versione di SDK.
* Pulizia del codice di minore entità.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Disabilitazione della verifica TLS durante l'uso dell'SDK per l'emulatore(nomehost=host).
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Aggiunta del supporto per le query in parallelo nelle raccolte partizionate.
* Aggiunta del supporto per le query TOP/ORDER BY nelle raccolte partizionate.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, Cosmos DB esegue nove tentativi per ogni richiesta con codice di errore 429, rispettando l'intervallo di tempo di retryAfter specificato nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Azure Cosmos DB attende al massimo 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* Cosmos DB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il numero di nuovi tentativi di limitazione e il tempo cumulativo di attesa della richiesta tra i tentativi.
* La classe RetryOptions è stata aggiunta, esponendo la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l'override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Aggiunta del supporto per gli account di database con più aree.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md).

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Correzione del bug RangePartitionResolver.resolveForRead, relativo alla mancata restituzione di collegamenti a causa di un concatenamento non valido dei risultati.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Corretto hashPartitionResolver resolveForRead(): quando la mancata indicazione di una chiave di partizione generava un'eccezione, invece di restituire un elenco di tutti i collegamenti registrati.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Correzione del problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di Azure Cosmos DB. Uso di un agente dedicato per tutte le richieste della libreria.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Correzione del problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestione corretta dei trattini negli ID dei file multimediali.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Correzione del problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avviso di perdita del listener EventEmitter.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Correzione del problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90): ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implementazione del supporto per il partizionamento orizzontale mediante l'aggiunta di resolver della partizione a intervalli e hash.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementazione di Upsert. Nuovi metodi upsertXXX in documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Saltata per riallineare il numero di versione con altri SDK.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Divisione del wrapper Q promise per il nuovo repository
* Aggiornamento del file del pacchetto del Registro di sistema npm

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementazione del routing basato su ID
* Correzione del problema [n. 49](https://github.com/Azure/azure-documentdb-node/issues/49) : conflitto tra la proprietà current e il metodo current().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Aggiunta del supporto per l'indice GeoSpatial
* Convalida della proprietà ID per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, &#47;&#47; o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementazione del criterio di indicizzazione V2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problema [n. 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implementazione delle configurazioni eslint e grunt nell'SDK core e promise.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) : il wrapper promise non include l'intestazione con errore

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Implementazione della possibilità di eseguire query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts
* Aggiornamento della documentazione relativa alle API
* Problema [n. 41](https://github.com/Azure/azure-documentdb-node/issues/41) : errore client.createDocumentAsync.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* SDK con disponibilità generale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata. Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 luglio 2019 |--- |
| [3.0.4](#3.0.4) |22 luglio 2019 |--- |
| [3.0.3](#3.0.3) |17 luglio 2019 |--- |
| [3.0.2](#3.0.2) |9 luglio 2019 |--- |
| [3.0.0](#3.0.0) |28 giugno 2019 |--- |
| [2.1.5](#2.1.5) |20 marzo 2019 |--- |
| [2.1.4](#2.1.4) |15 marzo 2019 |--- |
| [2.1.3](#2.1.3) |8 marzo 2019 |--- |
| [2.1.2](#2.1.2) |28 gennaio 2019 |--- |
| [2.1.1](#2.1.1) |5 dicembre 2018 |--- |
| [2.1.0](#2.1.0) |4 dicembre 2018 |--- |
| [2.0.5](#2.0.5) |7 novembre 2018 |--- |
| [2.0.4](#2.0.4) |30 ottobre 2018 |--- |
| [2.0.3](#2.0.3) |30 ottobre 2018 |--- |
| [2.0.2](#2.0.2) |10 ottobre 2018 |--- |
| [2.0.1](#2.0.1) |25 settembre 2018 |--- |
| [2.0.0](#2.0.0) |24 settembre 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 agosto 2018 |--- |
| [1.14.4](#1.14.4) |3 maggio 2018 |30 agosto 2020 |
| [1.14.3](#1.14.3) |3 maggio 2018 |30 agosto 2020 |
| [1.14.2](#1.14.2) |21 dicembre 2017 |30 agosto 2020 |
| [1.14.1](#1.14.1) |10 novembre 2017 |30 agosto 2020 |
| [1.14.0](#1.14.0) |09 novembre 2017 |30 agosto 2020 |
| [1.13.0](#1.13.0) |11 ottobre 2017 |30 agosto 2020 |
| [1.12.2](#1.12.2) |10 agosto 2017 |30 agosto 2020 |
| [1.12.1](#1.12.1) |10 agosto 2017 |30 agosto 2020 |
| [1.12.0](#1.12.0) |10 maggio 2017 |30 agosto 2020 |
| [1.11.0](#1.11.0) |16 marzo 2017 |30 agosto 2020 |
| [1.10.2](#1.10.2) |27 gennaio 2017 |30 agosto 2020 |
| [1.10.1](#1.10.1) |22 dicembre 2016 |30 agosto 2020 |
| [1.10.0](#1.10.0) |03 ottobre 2016 |30 agosto 2020 |
| [1.9.0](#1.9.0) |07 luglio 2016 |30 agosto 2020 |
| [1.8.0](#1.8.0) |14 giugno 2016 |30 agosto 2020 |
| [1.7.0](#1.7.0) |26 aprile 2016 |30 agosto 2020 |
| [1.6.0](#1.6.0) |29 marzo 2016 |30 agosto 2020 |
| [1.5.6](#1.5.6) |08 marzo 2016 |30 agosto 2020 |
| [1.5.5](#1.5.5) |02 febbraio 2016 |30 agosto 2020 |
| [1.5.4](#1.5.4) |01 febbraio 2016 |30 agosto 2020 |
| [1.5.2](#1.5.2) |26 gennaio 2016 |30 agosto 2020 |
| [1.5.2](#1.5.2) |22 gennaio 2016 |30 agosto 2020 |
| [1.5.1](#1.5.1) |4 gennaio 2016 |30 agosto 2020 |
| [1.5.0](#1.5.0) |31 dicembre 2015 |30 agosto 2020 |
| [1.4.0](#1.4.0) |06 ottobre 2015 |30 agosto 2020 |
| [1.3.0](#1.3.0) |06 ottobre 2015 |30 agosto 2020 |
| [1.2.2](#1.2.2) |10 settembre 2015 |30 agosto 2020 |
| [1.2.1](#1.2.1) |15 agosto 2015 |30 agosto 2020 |
| [1.2.0](#1.2.0) |05 agosto 2015 |30 agosto 2020 |
| [1.1.0](#1.1.0) |09 luglio 2015 |30 agosto 2020 |
| [1.0.3](#1.0.3) |04 giugno 2015 |30 agosto 2020 |
| [1.0.2](#1.0.2) |23 maggio 2015 |30 agosto 2020 |
| [1.0.1](#1.0.1) |15 maggio 2015 |30 agosto 2020 |
| [1.0.0](#1.0.0) |08 aprile 2015 |30 agosto 2020 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

