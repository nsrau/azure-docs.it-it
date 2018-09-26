---
title: 'Azure Cosmos DB: risorse, SDK e API Node.js di SQL | Microsoft Docs'
description: Informazioni complete sull'SDK e sull'API Node.js di SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Node.js SDK.
services: cosmos-db
author: deborahc
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6983b49135b5a8adbef1d0cfc5a407cb7b0c7ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960550"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Node.js SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|Risorsa  |Collegamento  |
|---------|---------|
|Download dell'SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentazione sull'API  |  [Documentazione di riferimento per JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Istruzioni per l'installazione dell'SDK  |  [Istruzioni di installazione](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuire all'SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Esempi | [Esempi di codice Node.js](sql-api-nodejs-samples.md)
| Esercitazione introduttiva | [Introduzione a JavaScript SDK](sql-api-nodejs-get-started.md)
| Esercitazione sull'app Web | [Creare un'applicazione Web Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
| Piattaforma attualmente supportata | [Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - richiesto per SDK versione 2.0.0 e successive.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Note sulla versione

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
* Corretto hashParitionResolver resolveForRead(): quando la mancata indicazione di una chiave di partizione generava un'eccezione, invece di restituire un elenco di tutti i collegamenti registrati.

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
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, &#47;&#47; o terminare con uno spazio.
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

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato viene rifiutata dal servizio.

<br/>

| Version | Data di rilascio | Data di ritiro |
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

