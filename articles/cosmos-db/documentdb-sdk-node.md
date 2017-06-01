---
title: API Node.js, risorse e SDK per Azure DocumentDB | Microsoft Docs
description: Tutte le informazioni sull&quot;SDK e sull&quot;API di Node.js, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB Node.js SDK.
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/24/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 260405d1f6b5064af3d533ecef722f0d56b7ef36
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="documentdb-nodejs-sdk-release-notes-and-resources"></a>DocumentDB Node.js SDK: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Download dell'SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento delle API di Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Istruzioni per l'installazione dell'SDK**</td><td>[Istruzioni di installazione](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice Node.js](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Esercitazione introduttiva**</td><td>[Introduzione all'SDK di Node.js](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Creare un'applicazione Web Node.js con DocumentDB](documentdb-nodejs-application.md)</td></tr>

<tr><td>**Piattaforma attualmente supportata**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="1.12.0"/>1.12.0</a>
* Aggiunta del supporto per la funzionalità [unità di richiesta al minuto (RU/m)](../cosmos-db/request-units-per-minute.md).
* Aggiunta del supporto per nuovi [livelli di coerenza](consistency-levels.md) denominati ConsistentPrefix.
* Aggiunta del supporto per UriFactory.
* Risolto un bug di supporto unicode. Problema GitHub #171

### <a name="1.11.0"/>1.11.0</a>
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta dell'opzione per il controllo del grado di parallelismo per query nella partizione.
* Aggiunta dell'opzione per la disabilitazione della verifica SSL durante l'esecuzione nell'emulatore DocumentDB.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Correzione del bug del token di continuazione per raccolta a partizione singola (github #107).
* Correzione del bug executeStoredProcedure nella gestione di 0 come parametro singolo (github #155).

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
* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, DocumentDB esegue nove tentativi per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il tempo di RetryAfter nell'intestazione della risposta. Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi. Ora, DocumentDB attende al massimo 30 secondi per ciascuna richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.
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
* Correzione del problema [n. 100](https://github.com/Azure/azure-documentdb-node/issues/100) relativo all'agente HTTPS dedicato: evitare di modificare l'agente globale per gli scopi di DocumentDB. Usare un agente dedicato per tutte le richieste della libreria.

### <a name="1.5.3"/>1.5.3</a>
* Correzione del problema [n. 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestione corretta dei trattini negli ID dei file multimediali.

### <a name="1.5.2"/>1.5.2</a>
* Correzione del problema [n. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avviso di perdita del listener EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Correzione del problema [n. 92](https://github.com/Azure/azure-documentdb-node/issues/90) : ridenominazione della cartella Hash in hash per i sistemi con distinzione tra maiuscole e minuscole.

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
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
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

## <a name="faq"></a>domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).


