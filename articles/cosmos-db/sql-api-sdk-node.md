---
title: 'Database Cosmos di Azure: API SQL Node.js, risorse & SDK'
description: Informazioni complete sull'SDK e sull'API Node.js di SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: de1c1b93b813f71b321da0625bc60e0762a859c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70207951"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Node.js SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed di modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Esecutore in blocco - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

|Risorsa  |Collegamento  |
|---------|---------|
|Download dell'SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentazione sull'API  |  [Documentazione di riferimento per JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Istruzioni per l'installazione dell'SDK  |  [Istruzioni per l'installazione](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuire all'SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Esempi | [Esempi di codice Node.js](sql-api-nodejs-samples.md)
| Esercitazione introduttiva | [Introduzione a JavaScript SDK](sql-api-nodejs-get-started.md)
| Esercitazione sull'app Web | [Creare un'applicazione Web Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
| Piattaforma attualmente supportata | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK versione 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK versione 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK versione 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK versione 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK versione 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK versione 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK versione 1.x.x

## <a name="release-notes"></a>Note sulla versione

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Impostare il valore predefinito ResponseContinuationTokenLimitInKB su 1kb. Per impostazione predefinita, stiamo limitando questo a 1kb per evitare intestazioni lunghe (Node.js ha un limite di dimensione dell'intestazione globale). Un utente può impostare questo campo per consentire intestazioni più lunghe, che consentono al back-end di ottimizzare l'esecuzione delle query.
* Rimuovere disableSSLVerification. Questa opzione contiene nuove alternative descritte in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Consenti a initialHeaders di impostare in modo esplicito l'intestazione della chiave di partizioneAllow initialHeaders to explicitly set partition key header
* Utilizzare package.json-files per impedire la pubblicazione di file estranei
* Correggere l'errore di ordinamento della mappa di routing nella versione precedente del nodo v8
* Corregge il bug quando l'utente fornisce opzioni di ripetizione dei tentativi parzialiFixes bug when user supplies partial retry options

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Impedisci a Webpack di risolvere i moduli chiamati con la richiesta

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Corregge un lungo bug in sospeso in cui le RU venivano sempre segnalate come 0 per le query di aggregazione

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 versione v3! 🎉 molte nuove funzionalità, correzioni di bug e alcune modifiche di rilievo. Obiettivi principali di questa versione:

* Implementare nuove funzionalità principali
  * Query DISTINCT
  * Query LIMIT/OFFSET
  * Richieste annullabili dall'utente
* Aggiornamento alla versione più recente dell'API REST Cosmos in cui tutti i contenitori hanno scalabilità illimitata
* Semplificare l'utilizzo di Cosmos dal browser
* Meglio allinearsi con le nuove linee guida di Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guida alla migrazione per le modifiche di rilievoMigration guide for breaking changes
##### <a name="improved-client-constructor-options"></a>Opzioni del costruttore client migliorateImproved client Constructor options

Le opzioni del costruttore sono state semplificate:Constructor options have been simplified:

* masterKey è stato rinominato chiave e spostato al livello superiore
* Le proprietà precedentemente in options.auth sono state spostate al livello superiore

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
Nella versione 2 erano disponibili molti modi diversi per scorrere o recuperare i risultati da una query. Abbiamo tentato di semplificare l'API v3 e rimuovere API simili o duplicate:

* Rimuovere iterator.next() e iterator.current(). Utilizzare fetchNext() per ottenere pagine di risultati.
* Rimuovere iterator.forEach(). Usare invece gli iteratori asincroni.
* iterator.executeNext() rinominata in iterator.fetchNext()
* iterator.toArray() rinominata in iterator.fetchAll()
* Le pagine sono ora oggetti Response appropriati anziché oggetti JS semplici
* contenitore const : client.database(dbId)container(containerId)

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
Il servizio Cosmos ora supporta le chiavi di partizione in tutti i contenitori, inclusi quelli creati in precedenza come contenitori fissi. L'SDK v3 viene aggiornato alla versione API più recente che implementa questa modifica, ma non è interrompe. Se non si fornisce una chiave di partizione per le operazioni, verrà usata per impostazione predefinita una chiave di sistema che funziona con tutti i contenitori e i documenti esistenti.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert rimosso per le stored procedure
In precedenza upsert era consentito per le raccolte non partizionate, ma con l'aggiornamento della versione dell'API, tutte le raccolte sono partizionate in modo che sia stato rimosso completamente.

##### <a name="item-reads-will-not-throw-on-404"></a>Letture dell'oggetto non getterà su 404
contenitore const : client.database(dbId)container(containerId)

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

##### <a name="default-multi-region-write"></a>Scrittura in più aree predefinite
L'SDK scriverà ora in più aree per impostazione predefinita se la configurazione Cosmos lo supporta. Questo era in precedenza il comportamento di consenso esplicito.

##### <a name="proper-error-objects"></a>Oggetti di errore appropriati
Le richieste non riuscite generano errore o sottoclassi di errore. In precedenza hanno gettato oggetti JS pianura.

#### <a name="new-features"></a>Nuove funzionalità
##### <a name="user-cancelable-requests"></a>Richieste annullabili dall'utente
Lo spostamento per recuperare internamente ci permette di utilizzare il browser AbortController API per supportare le operazioni annullabili dall'utente. Nel caso di operazioni in cui più richieste sono potenzialmente in corso (ad esempio query tra partizioni), tutte le richieste per l'operazione verranno annullate. Gli utenti moderni del browser avranno già AbortController. Gli utenti di Node.js dovranno utilizzare una libreria di polifill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Impostare la velocità effettiva come parte dell'operazione di creazione db/containerSet throughput as part of db/container create operation
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
La generazione di token di intestazione è stata suddivisa in una nuova libreria, @azure/cosmos-sign. Chiunque chiami direttamente l'API REST Cosmos può usarlo @azure/cosmosper firmare le intestazioni usando lo stesso codice che chiamiamo all'interno di .

##### <a name="uuid-for-generated-ids"></a>UUID for generated IDs
v2 aveva codice personalizzato per generare gli ID elemento. Siamo passati alla biblioteca della comunità ben nota e mantenuta uuid.

##### <a name="connection-strings"></a>Stringhe di connessione
È ora possibile passare una stringa di connessione copiata dal portale di Azure:It is now be pass a connection string copied from the Azure portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Esperienza browser migliorata
Mentre è stato possibile utilizzare il v2 SDK nel browser non era un'esperienza ideale. Era necessario ricaricare diverse librerie incorporate node.js e usare un bundler come Webpack o Parcel. L'SDK v3 rende l'esperienza out-of-the-to-the molto migliore per gli utenti del browser.

* Sostituisci i dati interni della richiesta con fetch (#245)
* Rimuovere l'utilizzo del buffer (#330)
* Rimuovere l'utilizzo incorporato del nodo a favore di pacchetti/API universali (#328)
* Passare al nodo-abort-controller (#294)

#### <a name="bug-fixes"></a>Correzioni di bug
* Correggere i test di lettura e riporto dell'offerta (#224)
* Correggere EnableEndpointDiscovery (#207)
* Correggere le RU mancanti nei risultati impaginati (#360)
* Espandere il tipo di parametro di query SQL (#346)
* Aggiungi ttl a ItemDefinition (#341)
* Correggere le metriche delle query CP (#311)
* Aggiungere activityId a FeedResponse (#293)
* Passare _ts tipo da una stringa all'altra (#252)(#295)
* Correggere l'aggregazione addebito richiesta (#289)
* Consenti chiavi di partizione stringa vuota (#277)Allow blank string partition keys (#277)
* Aggiungi stringa al tipo di query in conflitto (#237)
* Aggiungere uniqueKeyPolicy al contenitore (#234)

#### <a name="engineering-systems"></a>Sistemi di ingegneria
Non sempre i cambiamenti più visibili, ma aiutano il nostro team a spedire un codice migliore, più velocemente.

* Usare il rollup per le build di produzione (#104)
* Aggiornamento a Typescript 3.5 (#327)
* Convertire in riferimenti al progetto TS. Estrai cartella di prova (#270)
* Abilita noUnusedLocals e noUnusedParameters (#275)
* Pipeline di Azure YAML per compilazioni CI (#298)Azure Pipelines YAML for CI builds (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Nessuna modifica del codice. Consente di risolvere un problema a quando alcuni file aggiuntivi sono stati inclusi nel pacchetto 2.1.4.Fixes an issue where some extra files were included in 2.1.4 package.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Correggere il failover regionale all'interno dei criteri di ripetizionedei
* Correggere ChangeFeed haMoreResults proprietà
* Aggiornamenti delle dipendenze di sviluppo
* Aggiungere PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Passare _ts tipo da una stringa all'altra
* Correggere i test di indicizzazione predefiniti
* Backport uniqueKeyPolicy alla v2
* Correzioni di debug demo e demo

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Correzioni dell'offerta Backport dal ramo v3
* Correggere il bug nella firma del tipo executeNext()
* Correzioni per tipo di errore

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Costruire la ristrutturazione. Consente di estrarre la versione dell'SDK in fase di compilazione.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunto il supporto ChangeFeed (#196)
* Aggiunto tipo di dati MultiPolygon per l'indicizzazione (#191)
* Aggiungere la proprietà "key" al costruttore come alias per masterKey (#202)

#### <a name="fixes"></a>Correzioni
* Correggere il bug in cui next() restituiva un valore non corretto nell'iteratoreFix bug where next() was returning incorrect value on iterator

#### <a name="engineering-improvements"></a>Miglioramenti tecnici
* Aggiungere il test di integrazione per il consumo di tipope (#199)
* Abilitare l'installazione direttamente da GitHub (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Aggiunge l'interfaccia per il tipo di agente del nodo. Gli utenti di Typescript non devono più installare @types/node come dipendenza
* Le posizioni preferite vengono ora rispettate correttamente
* Miglioramenti apportati alla documentazione degli sviluppatori
* Varie correzioni di errori di battitura

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Risolve il problema di definizione dei tipi introdotto nella versione 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Rimuove la dipendenza `big-integer`
* Passa alle direttive di riferimento per il tipo AsyncIterable. Gli utenti di Typescript non devono più personalizzare la loro impostazione "lib".
* Correzione di errori di battitura

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Correzione dei collegamenti Leggimi

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Correzione dell'implementazione dell'interfaccia di ripetizione dei tentativi

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* Disponibilità generale della versione 2.0.0 di JavaScript SDK
* Aggiunta di supporto per le scritture in più aree.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 della versione 2.0.0 di JavaScript SDK per l'anteprima pubblica.
* Nuovo modello a oggetti con il modulo CosmosClient di livello superiore e metodi suddivisi tra le relative classi del database, del contenitore e dell'elemento. 
* Supporto per [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertito in TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* Correzione della documentazione di npm.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Aggiunta del supporto per nuovi tentativi predefiniti in caso di problemi di connessione.
* Aggiunta del supporto per la lettura del feed delle modifiche della raccolta.
* Risoluzione di un bug di coerenza della sessione che causava in modo intermittente un errore di sessione di lettura non disponibile.
* Aggiunta del supporto per le metriche di query.
* Modifica del numero massimo di connessioni dell'agente HTTP.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.
* Aggiunta del supporto per l'impostazione proxyUrl in ConnectionPolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Correzione secondaria per i sistemi di file con distinzione tra maiuscole e minuscole.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Aggiunge il supporto per la coerenza di sessione.
* Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Query indivisibili su più partizioni.
* Aggiunge il supporto per il collegamento a una risorsa con barra iniziale e finale (e relativi test).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   Correzione della documentazione di npm.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Correzione di un bug in executeStoredProcedure per cui i documenti coinvolti contenevano caratteri Unicode speciali (LS, PS).
* Correzione di un bug nella gestione di documenti con caratteri Unicode nella chiave di partizione.
* Correzione del supporto per la creazione di raccolte con i supporti di memorizzazione dei nomi. Problema GitHub n. 114.
* Correzione del supporto per il token di autorizzazione. Problema GitHub n. 178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Aggiunta del supporto per nuovi [livelli di coerenza](consistency-levels.md) denominati ConsistentPrefix.
* Aggiunta del supporto per UriFactory.
* Correzione di un bug del supporto Unicode. Problema GitHub n. 171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta dell'opzione per il controllo del grado di parallelismo per query nella partizione.
* Aggiunta dell'opzione per disabilitare la verifica SSL durante l'esecuzione sull'emulatore Azure Cosmos DB.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Correzione del bug del token di continuazione per la raccolta a partizione singola. Problema GitHub n. 107.
* Correzione del bug executeStoredProcedure nella gestione di 0 come parametro singolo. Problema GitHub n. 155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Corretta l'intestazione agente-utente per includere la versione di SDK.
* Pulizia del codice di minore entità.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Disabilitazione della verifica SSL durante l'uso dell'SDK per l'emulatore(nomehost=host).
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Aggiunta del supporto per le query in parallelo nelle raccolte partizionate.
* Aggiunta del supporto per le query TOP/ORDER BY nelle raccolte partizionate.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate Le richieste limitate ricevono un'eccezione di richiesta troppo grande, codice di errore 429. Per impostazione predefinita, Azure Cosmos DB riprova nove volte per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il retryAfter tempo nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Azure Cosmos DB attende al massimo 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* Cosmos DB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il numero di nuovi tentativi di limitazione e il tempo cumulativo di attesa della richiesta tra i tentativi.
* La classe RetryOptions è stata aggiunta, esponendo la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l'override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Aggiunta del supporto per gli account di database con più aree.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Correzione del bug RangePartitionResolver.resolveForRead, relativo alla mancata restituzione di collegamenti a causa di un concatenamento non valido dei risultati.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Correzione del problema di hashPartitionResolver resolveForRead(): se non veniva specificata una chiave di partizione, veniva generata un'eccezione, anziché l'elenco di tutti i collegamenti registrati.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Correzione del problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di Azure Cosmos DB. Usare un agente dedicato per tutte le richieste della libreria.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Correzione del problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestione corretta dei trattini negli ID dei file multimediali.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Correzione del problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avviso di perdita del listener EventEmitter.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Correzione del problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90): ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implementazione del supporto per il partizionamento orizzontale mediante l'aggiunta di resolver della partizione a intervalli e hash.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementazione di Upsert. Nuovi metodi upsertXXX in documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Saltata per riallineare il numero di versione con altri SDK.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Divisione del wrapper Q promise per il nuovo repository
* Aggiornamento del file del pacchetto del Registro di sistema npm

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementazione del routing basato su ID
* Correzione del problema [n. 49](https://github.com/Azure/azure-documentdb-node/issues/49) : conflitto tra la proprietà current e il metodo current().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Aggiunta del supporto per l'indice GeoSpatial
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere ?, /, ,  &#47;&#47;, caratteri o terminano con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementazione del criterio di indicizzazione V2.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Problema [n. 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implementazione delle configurazioni eslint e grunt nell'SDK core e promise.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) : il wrapper promise non include l'intestazione con errore

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Implementazione della possibilità di eseguire query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts
* Aggiornamento della documentazione relativa alle API
* Problema [n. 41](https://github.com/Azure/azure-documentdb-node/issues/41) : errore client.createDocumentAsync.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* SDK con disponibilità generale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni **1.x** dell'API SDK del client Node per SQL verranno ritirate il **30 agosto 2020.** Ciò influisce solo sull'SDK del nodo lato client e non sugli script sul lato server (stored procedure, trigger e funzioni definite dall'utente).
> 
>
<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 luglio 2019 |--- |
| [3.0.4](#3.0.4) |giovedì 22 luglio 2019 |--- |
| [3.0.3](#3.0.3) |17 luglio 2019 |--- |
| [3.0.2](#3.0.2) |09 luglio 2019 |--- |
| [3.0.0](#3.0.0) |giovedì 28 giugno 2019 |--- |
| [2.1.5](#2.1.5) |giovedì 20 marzo 2019 |--- |
| [2.1.4](#2.1.4) |martedì 15 marzo 2019 |--- |
| [2.1.3](#2.1.3) |lunedì 8 marzo 2019 |--- |
| [2.1.2](#2.1.2) |28 gennaio 2019 |--- |
| [2.1.1](#2.1.1) |5 dicembre 2018 |--- |
| [2.1.0](#2.1.0) |4 dicembre 2018 |--- |
| [2.0.5](#2.0.5) |giovedì 7 novembre 2018 |--- |
| [2.0.4](#2.0.4) |giovedì 30 ottobre 2018 |--- |
| [2.0.3](#2.0.3) |giovedì 30 ottobre 2018 |--- |
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

