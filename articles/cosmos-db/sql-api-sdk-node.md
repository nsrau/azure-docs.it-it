---
title: 'Azure Cosmos DB: API Node.js di SQL, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API Node.js di SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 27b548d03e6b05179da744e636a5c887e6b01ad5
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624682"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK per l'API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor in blocco-Java](sql-api-sdk-bulk-executor-java.md)

|Risorsa  |Collegamento  |
|---------|---------|
|Download dell'SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentazione dell'API  |  [Documentazione di riferimento per JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Istruzioni per l'installazione dell'SDK  |  [Istruzioni di installazione](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuire all'SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Esempi | [Esempi di codice Node.js](sql-api-nodejs-samples.md)
| Esercitazione introduttiva | [Introduzione a JavaScript SDK](sql-api-nodejs-get-started.md)
| Esercitazione sull'app Web | [Creare un'applicazione Web Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
| Piattaforma attualmente supportata | [Node. js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK versione 3. x. x<br/>[Node. js V10. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK versione 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK versione 3. x. x<br/>[Node. js V6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK versione 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK versione 1. x. x<br/> [Node. js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK versione 1. x. x<br/> [Node. js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK versione 1. x. x

## <a name="release-notes"></a>Note sulla versione

### <a name="3.1.0"/>3.1.0</a>
* Impostare il valore predefinito di ResponseContinuationTokenLimitInKB su 1 KB. Per impostazione predefinita, questa operazione viene limitata a 1 KB per evitare intestazioni lunghe (il limite delle dimensioni dell'intestazione globale è node. js). Un utente può impostare questo campo in modo da consentire intestazioni più lunghe, che possono aiutare il back-end a ottimizzare l'esecuzione delle query.
* Rimuovere disableSSLVerification. Questa opzione include nuove alternative descritte in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="3.0.4"/>3.0.4</a>
* Consenti a initialHeaders di impostare in modo esplicito l'intestazione della chiave di partizione
* Usare i file Package. JSON # per impedire la pubblicazione di file estranei
* Correzione dell'errore di ordinamento della mappa di routing nella versione precedente del nodo + V8
* Corregge un bug quando l'utente fornisce opzioni di ripetizione dei tentativi parziali

### <a name="3.0.3"/>3.0.3</a>
* Impedisci a Webpack di risolvere i moduli chiamati con require

### <a name="3.0.2"/>3.0.2</a>
* Corregge un bug in attesa lungo dove le UR venivano sempre segnalate come 0 per le query di aggregazione

### <a name="3.0.0"/>3.0.0</a>

🎉 versione V3 🎉 numerose nuove funzionalità, correzioni di bug e alcune modifiche di rilievo. Obiettivi principali di questa versione:

* Implementare nuove funzionalità principali
  * Query DISTINte
  * Query LIMIT/OFFSET
  * Richieste annullabili dall'utente
* Eseguire l'aggiornamento alla versione più recente dell'API REST Cosmos in cui tutti i contenitori hanno scalabilità illimitata
* Semplificare l'uso di Cosmos dal browser
* Allinea meglio alle nuove linee guida di Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guida alla migrazione per le modifiche di rilievo
##### <a name="improved-client-constructor-options"></a>Miglioramento delle opzioni del costruttore client

Sono state semplificate le opzioni del costruttore:

* la chiave è stata rinominata e spostata nel livello superiore
* Le proprietà in precedenza in options. auth sono state spostate nel livello superiore

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
Nella versione V2 erano disponibili molti modi per eseguire un'iterazione o recuperare i risultati di una query. Si è provato a semplificare l'API V3 e a rimuovere API simili o duplicate:

* Rimuovere iterator. Next () e Iterator. Current (). Utilizzare fetchNext () per ottenere pagine di risultati.
* Rimuovere iterator. forEach (). Usare invece gli iteratori asincroni.
* iterator. executeNext () rinominato in Iterator. fetchNext ()
* iterator. ToArray () rinominato in Iterator. fetchAll ()
* Le pagine sono ora oggetti di risposta appropriati anziché oggetti JS semplici
* const container = client. database (dbId). container (containerId)

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
Il servizio Cosmos supporta ora le chiavi di partizione in tutti i contenitori, inclusi quelli creati in precedenza come contenitori fissi. L'SDK V3 viene aggiornato alla versione più recente dell'API che implementa questa modifica, ma non si interrompe. Se non si specifica una chiave di partizione per le operazioni, per impostazione predefinita viene usata una chiave di sistema che funziona con tutti i contenitori e i documenti esistenti.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert rimossi per le stored procedure
In precedenza Upsert era consentito per le raccolte non partizionate, ma con l'aggiornamento della versione dell'API, tutte le raccolte vengono partizionate in modo da essere rimosse completamente.

##### <a name="item-reads-will-not-throw-on-404"></a>Le letture di elementi non vengono generate il 404
const container = client. database (dbId). container (containerId)

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

##### <a name="default-multi-region-write"></a>Scrittura predefinita in più aree
Per impostazione predefinita, l'SDK scriverà in più aree se la configurazione di Cosmos la supporta. Questo comportamento è stato precedentemente esplicito.

##### <a name="proper-error-objects"></a>Oggetti errore appropriati
Le richieste non riuscite ora generano errori o sottoclassi di errore appropriati. In precedenza hanno generato oggetti JS semplici.

#### <a name="new-features"></a>Nuove funzionalità
##### <a name="user-cancelable-requests"></a>Richieste annullabili dall'utente
Il passaggio a fetch interna consente di usare l'API AbortController del browser per supportare le operazioni annullabili dall'utente. Nel caso di operazioni in cui sono potenzialmente in corso più richieste (ad esempio query tra partizioni), tutte le richieste per l'operazione verranno annullate. Gli utenti del browser moderni avranno già AbortController. Gli utenti node. js dovranno usare una libreria di riempimento

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Imposta la velocità effettiva come parte dell'operazione di creazione del contenitore/database
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
La generazione del token di intestazione è stata suddivisa in @azure/cosmos-signuna nuova libreria. Chiunque chiami direttamente l'API REST di Cosmos può usarlo per firmare le intestazioni usando lo stesso codice chiamato @azure/cosmosall'interno.

##### <a name="uuid-for-generated-ids"></a>UUID per gli ID generati
V2 dispone di codice personalizzato per generare ID elemento. È stato passato l'UUID della libreria community ben nota e gestita.

##### <a name="connection-strings"></a>Stringhe di connessione
È ora possibile passare una stringa di connessione copiata dal portale di Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Esperienza browser migliorata
Sebbene sia possibile utilizzare l'SDK v2 nel browser, non è stata un'esperienza ideale. È necessario riempire diverse librerie predefinite di node. js e usare un bundler come Webpack o pacco. L'SDK V3 rende l'esperienza predefinita molto migliore per gli utenti del browser.

* Sostituisci elementi interni richiesta con recupero (#245)
* Rimuovi utilizzo del buffer (#330)
* Rimuovere l'utilizzo incorporato del nodo a favore di pacchetti/API universali (#328)
* Passa a node-Abort-controller (#294)

#### <a name="bug-fixes"></a>Correzioni di bug
* Correzione dell'offerta leggere e ripristinare i test dell'offerta (#224)
* Correzione di EnableEndpointDiscovery (#207)
* Correggi le UR mancanti nei risultati impaginati (#360)
* Espandi tipo di parametro query SQL (#346)
* Aggiungere la durata (TTL) a ItemDefinition (#341)
* Correggere le metriche delle query CP (#311)
* Aggiungere activityId a FeedResponse (#293)
* Passa il tipo di _ts da String a Number (#252) (#295)
* Correzione aggregazione addebito richieste (#289)
* Consenti chiavi di partizione stringa vuote (#277)
* Aggiungi stringa al tipo di query dei conflitti (#237)
* Aggiungere uniqueKeyPolicy al contenitore (#234)

#### <a name="engineering-systems"></a>Sistemi di progettazione
Non sempre le modifiche più visibili, ma consentono al team di fornire codice migliore, più velocemente.

* Usare rollup per le compilazioni di produzione (#104)
* Aggiornamento a typescript 3,5 (#327)
* Converti in riferimenti al progetto di Servizi terminal. Estrai cartella test (#270)
* Abilitare noUnusedLocals e noUnusedParameters (#275)
* Azure Pipelines YAML per le compilazioni CI (#298)

### <a name="2.1.5"/>2.1.5</a>
* Non vengono apportate modifiche al codice. Corregge un problema per cui alcuni file aggiuntivi sono stati inclusi nel pacchetto 2.1.4.

### <a name="2.1.4"/>2.1.4</a>
* Correzione del failover a livello di area entro i criteri di ripetizione
* Correzione della proprietà hasMoreResults di offre
* Aggiornamenti delle dipendenze di sviluppo
* Aggiungere PolicheckExclusions. txt

### <a name="2.1.3"/>2.1.3</a>
* Passa il tipo di _ts da stringa a numero
* Correggi i test di indicizzazione predefiniti
* Backporting uniqueKeyPolicy alla versione V2
* Correzioni di debug demo e demo

### <a name="2.1.2"/>2.1.2</a>
* Correzioni dell'offerta backporting dal ramo V3
* Correzione di un bug nella firma del tipo executeNext ()
* Correzioni di errori di digitazione

### <a name="2.1.1"/>2.1.1</a>
* Ristrutturazione della compilazione. Consente di estrarre la versione dell'SDK in fase di compilazione.

### <a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunta del supporto offre (#196)
* Aggiunto DataType MultiPolygon per l'indicizzazione (#191)
* Aggiungere la proprietà "Key" al costruttore come alias per il #202

#### <a name="fixes"></a>Correzioni
* Correzione di un bug in cui Next () restituisce un valore errato nell'iteratore

#### <a name="engineering-improvements"></a>Miglioramenti della progettazione
* Aggiungi test di integrazione per l'utilizzo di typescript (#199)
* Abilitare l'installazione direttamente da GitHub (#194)

### <a name="2.0.5"/>2.0.5</a>
* Aggiunge l'interfaccia per il tipo di agente del nodo. Gli utenti di Typescript non devono più installare @types/node come dipendenza
* Le posizioni preferite vengono ora rispettate correttamente
* Miglioramenti apportati alla documentazione degli sviluppatori
* Varie correzioni di errori di battitura

### <a name="2.0.4"/>2.0.4</a>
* Risolve il problema di definizione dei tipi introdotto nella versione 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Rimuove la dipendenza `big-integer`
* Passa alle direttive di riferimento per il tipo AsyncIterable. Gli utenti di Typescript non devono più personalizzare la loro impostazione "lib".
* Correzione di errori di battitura

### <a name="2.0.2"/>2.0.2</a>
* Correzione dei collegamenti Leggimi

### <a name="2.0.1"/>2.0.1</a>
* Correzione dell'implementazione dell'interfaccia di ripetizione dei tentativi

### <a name="2.0.0"/>2.0.0</a>
* Disponibilità generale della versione 2.0.0 di JavaScript SDK
* Aggiunta di supporto per le scritture in più aree.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 della versione 2.0.0 di JavaScript SDK per l'anteprima pubblica.
* Nuovo modello a oggetti con il modulo CosmosClient di livello superiore e metodi suddivisi tra le relative classi del database, del contenitore e dell'elemento. 
* Supporto per [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertito in TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* Correzione della documentazione di npm.

### <a name="1.14.3"/>1.14.3</a>
* Aggiunta del supporto per nuovi tentativi predefiniti in caso di problemi di connessione.
* Aggiunta del supporto per la lettura del feed delle modifiche della raccolta.
* Risoluzione di un bug di coerenza della sessione che causava in modo intermittente un errore di sessione di lettura non disponibile.
* Aggiunta del supporto per le metriche di query.
* Modifica del numero massimo di connessioni dell'agente HTTP.

### <a name="1.14.2"/>1.14.2</a>
* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.
* Aggiunta del supporto per l'impostazione proxyUrl in ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Correzione secondaria per i sistemi di file con distinzione tra maiuscole e minuscole.

### <a name="1.14.0"/>1.14.0</a>
* Aggiunge il supporto per la coerenza di sessione.
* Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Query indivisibili su più partizioni.
* Aggiunge il supporto per il collegamento a una risorsa con barra iniziale e finale (e relativi test).

### <a name="1.12.2"/>1.12.2</a>
*   Correzione della documentazione di npm.

### <a name="1.12.1"/>1.12.1</a>
* Correzione di un bug in executeStoredProcedure per cui i documenti coinvolti contenevano caratteri Unicode speciali (LS, PS).
* Correzione di un bug nella gestione di documenti con caratteri Unicode nella chiave di partizione.
* Correzione del supporto per la creazione di raccolte con i supporti di memorizzazione dei nomi. Problema GitHub n. 114.
* Correzione del supporto per il token di autorizzazione. Problema GitHub n. 178.

### <a name="1.12.0"/>1.12.0</a>
* Aggiunta del supporto per nuovi [livelli di coerenza](consistency-levels.md) denominati ConsistentPrefix.
* Aggiunta del supporto per UriFactory.
* Correzione di un bug del supporto Unicode. Problema GitHub n. 171.

### <a name="1.11.0"/>1.11.0</a>
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta dell'opzione per il controllo del grado di parallelismo per query nella partizione.
* Aggiunta dell'opzione per disabilitare la verifica SSL durante l'esecuzione sull'emulatore Azure Cosmos DB.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Correzione del bug del token di continuazione per la raccolta a partizione singola. Problema GitHub n. 107.
* Correzione del bug executeStoredProcedure nella gestione di 0 come parametro singolo. Problema GitHub n. 155.

### <a name="1.10.2"/>1.10.2</a>
* Corretta l'intestazione agente-utente per includere la versione di SDK.
* Pulizia del codice di minore entità.

### <a name="1.10.1"/>1.10.1</a>
* Disabilitazione della verifica SSL durante l'uso dell'SDK per l'emulatore(nomehost=host).
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.

### <a name="1.10.0"/>1.10.0</a>
* Aggiunta del supporto per le query in parallelo nelle raccolte partizionate.
* Aggiunta del supporto per le query TOP/ORDER BY nelle raccolte partizionate.

### <a name="1.9.0"/>1.9.0</a>
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, Cosmos DB esegue nove tentativi per ogni richiesta con codice di errore 429, rispettando l'intervallo di tempo di retryAfter specificato nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Azure Cosmos DB attende al massimo 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
* Cosmos DB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il numero di nuovi tentativi di limitazione e il tempo cumulativo di attesa della richiesta tra i tentativi.
* La classe RetryOptions è stata aggiunta, esponendo la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l'override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="1.8.0"/>1.8.0</a>
* Aggiunta del supporto per gli account di database con più aree.

### <a name="1.7.0"/>1.7.0</a>
* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="1.6.0"/>1.6.0</a>
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Correzione del bug RangePartitionResolver.resolveForRead, relativo alla mancata restituzione di collegamenti a causa di un concatenamento non valido dei risultati.

### <a name="1.5.5"/>1.5.5</a>
* Corretto hashPartitionResolver resolveForRead(): quando la mancata indicazione di una chiave di partizione generava un'eccezione, invece di restituire un elenco di tutti i collegamenti registrati.

### <a name="1.5.4"/>1.5.4</a>
* Correzione del problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di Azure Cosmos DB. Usare un agente dedicato per tutte le richieste della libreria.

### <a name="1.5.3"/>1.5.3</a>
* Correzione del problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestione corretta dei trattini negli ID dei file multimediali.

### <a name="1.5.2"/>1.5.2</a>
* Correzione del problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avviso di perdita del listener EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Correzione del problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90): ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

### <a name="1.5.0"/>1.5.0</a>
* Implementazione del supporto per il partizionamento orizzontale mediante l'aggiunta di resolver della partizione a intervalli e hash.

### <a name="1.4.0"/>1.4.0</a>
* Implementazione di Upsert. Nuovi metodi upsertXXX in documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Saltata per riallineare il numero di versione con altri SDK.

### <a name="1.2.2"/>1.2.2</a>
* Divisione del wrapper Q promise per il nuovo repository
* Aggiornamento del file del pacchetto del Registro di sistema npm

### <a name="1.2.1"/>1.2.1</a>
* Implementazione del routing basato su ID
* Correzione del problema [n. 49](https://github.com/Azure/azure-documentdb-node/issues/49) : conflitto tra la proprietà current e il metodo current().

### <a name="1.2.0"/>1.2.0</a>
* Aggiunta del supporto per l'indice GeoSpatial
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri? &#47; &#47;,/, #,, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementazione del criterio di indicizzazione V2.

### <a name="1.0.3"/>1.0.3</a>
* Problema [n. 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implementazione delle configurazioni eslint e grunt nell'SDK core e promise.

### <a name="1.0.2"/>1.0.2</a>
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) : il wrapper promise non include l'intestazione con errore

### <a name="1.0.1"/>1.0.1</a>
* Implementazione della possibilità di eseguire query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts
* Aggiornamento della documentazione relativa alle API
* Problema [n. 41](https://github.com/Azure/azure-documentdb-node/issues/41) : errore client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* SDK con disponibilità generale.

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 agosto 2018 |--- |
| [1.14.4](#1.14.4) |3 maggio 2018 |--- |
| [1.14.3](#1.14.3) |3 maggio 2018 |--- |
| [1.14.2](#1.14.2) |21 dicembre 2017 |--- |
| [1.14.1](#1.14.1) |10 novembre 2017 |--- |
| [1.14.0](#1.14.0) |09 novembre 2017 |--- |
| [1.13.0](#1.13.0) |11 ottobre 2017 |--- |
| [1.12.2](#1.12.2) |10 agosto 2017 |--- |
| [1.12.1](#1.12.1) |10 agosto 2017 |--- |
| [1.12.0](#1.12.0) |10 maggio 2017 |--- |
| [1.11.0](#1.11.0) |16 marzo 2017 |--- |
| [1.10.2](#1.10.2) |27 gennaio 2017 |--- |
| [1.10.1](#1.10.1) |22 dicembre 2016 |--- |
| [1.10.0](#1.10.0) |03 ottobre 2016 |--- |
| [1.9.0](#1.9.0) |07 luglio 2016 |--- |
| [1.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1.7.0](#1.7.0) |26 aprile 2016 |--- |
| [1.6.0](#1.6.0) |29 marzo 2016 |--- |
| [1.5.6](#1.5.6) |08 marzo 2016 |--- |
| [1.5.5](#1.5.5) |02 febbraio 2016 |--- |
| [1.5.4](#1.5.4) |01 febbraio 2016 |--- |
| [1.5.2](#1.5.2) |26 gennaio 2016 |--- |
| [1.5.2](#1.5.2) |22 gennaio 2016 |--- |
| [1.5.1](#1.5.1) |4 gennaio 2016 |--- |
| [1.5.0](#1.5.0) |31 dicembre 2015 |--- |
| [1.4.0](#1.4.0) |06 ottobre 2015 |--- |
| [1.3.0](#1.3.0) |06 ottobre 2015 |--- |
| [1.2.2](#1.2.2) |10 settembre 2015 |--- |
| [1.2.1](#1.2.1) |15 agosto 2015 |--- |
| [1.2.0](#1.2.0) |05 agosto 2015 |--- |
| [1.1.0](#1.1.0) |09 luglio 2015 |--- |
| [1.0.3](#1.0.3) |04 giugno 2015 |--- |
| [1.0.2](#1.0.2) |23 maggio 2015 |--- |
| [1.0.1](#1.0.1) |15 maggio 2015 |--- |
| [1.0.0](#1.0.0) |08 aprile 2015 |--- |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

