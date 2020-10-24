---
title: 'Azure Cosmos DB: API Java SQL, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 546c8ab9df58b19a47e0d330e13e65d3357c9e7c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487365"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK Java di Azure Cosmos DB per API SQL: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Si tratta di Azure Cosmos DB Sync Java SDK v2 per API SQL, che supporta operazioni asincrone.

> [!IMPORTANT]  
> *Non* corrisponde alla versione più recente di Java SDK per Azure Cosmos DB. Provare a usare [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) per il progetto. Per eseguire l'aggiornamento, seguire le istruzioni della guida alla [migrazione a Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e la guida relativa al [confronto tra Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md). 
>

| |  |
|---|---|
|**Download dell'SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentazione sull'API**|[Documentazione di riferimento API Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuire all'SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Operazioni preliminari**|[Introduzione a SDK Java](./create-sql-api-java.md)|
|**Esercitazione sull'app Web**|[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-java-application.md)|
|**Runtime minimo supportato**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Note sulla versione

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Corregge il problema della cache della partizione primaria nella query DocumentCollection.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Aggiunta del supporto per la configurazione personalizzata dei tentativi 449.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* È stato corretto un problema di timeout del pool di connessioni.
* È stato corretto l'aggiornamento del token di autenticazione per tentativi interni.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* È stato aggiornato il tag dei criteri di replica corretto sul lato client in databaseAccount e sono state effettuate letture della configurazione di databaseAccount dalla cache.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Non vengono più ripetuti nuovi tentativi in caso di errore per un intervallo di chiavi di partizione non valido, se l'utente specifica pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Sono stati ottimizzati gli aggiornamenti della cache degli intervalli di chiavi di partizione.
* È stato corretto lo scenario per cui l'SDK non trattiene l'hint di suddivisione della partizione dal server e si verifica quindi un errore nell'aggiornamento delle cache di routing sul lato client.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Sono stati ottimizzati gli aggiornamenti della cache di raccolta.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* È stato aggiunto il supporto per recuperare il messaggio di eccezione interna generato dalla stringa di diagnostica della richiesta.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* È stata introdotta l'API della versione in PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* È stato aggiunto il supporto di un timeout separato per la modalità diretta.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Viene usato ora il messaggio di errore null dal servizio e viene generata un'eccezione client del documento.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* È stata migliorata la connessione socket ed è stato aggiunto il valore predefinito true per SoKeepAlive.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* È stato aggiunto il supporto per la stringa di diagnostica della richiesta.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* È stato corretto un bug in PartitionKey per hash V2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* È stato aggiunto il supporto per indici composti.
* È stato corretto un bug in Endpoint Manager globale in modo da forzare l'aggiornamento.
* È stato corretto un bug per gli upsert con condizioni preliminari in modalità diretta.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* È stato corretto un bug nella cache degli indirizzi gateway.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Aggiunto il supporto per la scrittura in più aree per la modalità diretta.
* Aggiunto il supporto per la gestione di IOExceptions generate come eccezioni ServiceUnavailable, da un proxy.
* Risolto un bug nei criteri di ripetizione dei tentativi di individuazione degli endpoint.
* Risolto un bug per assicurare che non vengano generate eccezioni del puntatore Null in BaseDatabaseAccountConfigurationProvider.
* È stato risolto un bug per garantire che QueryIterator non restituisca valori Null.
* Risolto un bug per garantire che sia consentito PartitionKey ampio

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Aggiunto il supporto per la scrittura in più aree per la modalità gateway.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Risolto un bug negli intervalli di chiavi di partizione di lettura per una query.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Risolto un bug nell'impostazione delle dimensioni dell'intestazione dei token di continuazione in modalità DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Aggiunta del supporto del failover dello streaming.
* Aggiunta del supporto per i metadati personalizzati.
* Logica di gestione delle sessioni migliorata.
* Correzione di un bug nella cache dell'intervallo di chiavi di partizione.
* Correzione di un bug NPE in modalità diretta.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Aggiunta del supporto per indice univoco.
* Aggiunta del supporto per limitare la dimensione del token di continuazione nelle opzioni di feed.
* Correzione di un bug nella serializzazione Json (timestamp).
* Correzione di un bug nella serializzazione Json (enumerazione).
* Dipendenza su com.fasterxml.jackson.core:jackson-databind.aggiornata a 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Funzionalità migliorata per il pool di connessioni per la modalità diretta.
* Funzionalità migliorata per il piano di query di prelettura per query di partizione non orderby.
* Funzionalità migliorata per la generazione UUID.
* Funzionalità migliorata per la logica di coerenza di sessione.
* Aggiunta di supporto per multipoligoni.
* Aggiunta del supporto per le statistiche dell'intervallo di chiavi di partizione per la raccolta.
* Correzione di un bug in modalità di supporto per più aree.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Miglioramento delle prestazioni di serializzazione Json.
* Questa versione dell'SDK richiede la versione più recente dell'[emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Modifiche interne per le librerie Friend di Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Risolto un problema durante la lettura di singoli intervalli di chiavi di partizione.
* Risolto un problema nell'analisi di ResourceID che interessa il database con nomi brevi.
* Risolto una problema causato dalla codifica di chiavi di partizione.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Correzioni di bug importanti per richiedere l'elaborazione durante le suddivisioni delle partizioni.
* Risolto un problema relativo ai livelli di coerenza Strong e BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.
* Risolto un bug nella raccolta in modalità di sessione di lettura.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Supporto abilitato per la raccolta partizionata con valore ridotto di 2.500 UR/s e riduzione con incrementi di 100 UR/s.
* Correzione di un bug nell'assembly nativo che può causare l'eccezione NullRef in alcune query.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Correzione di un bug nella configurazione del motore di query che può generare eccezioni per le query in modalità Gateway.
* Correzione di alcuni bug nel contenitore della sessione che possono generare un'eccezione "Risorsa proprietario non trovata" per le richieste immediatamente dopo la creazione della raccolta.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](sql-query-aggregates.md).
* Aggiunta del supporto per la modifica del feed.
* Aggiunta del supporto per informazioni sulla quota della raccolta tramite RequestOptions.setPopulateQuotaInfo.
* Aggiunta del supporto per la registrazione dello script della procedura archiviata tramite RequestOptions.setScriptLoggingEnabled.
* Risoluzione di un bug in cui una query in modalità DirectHttps talvolta non rispondeva più in presenza di errori di limitazione.
* Risoluzione di un bug in modalità di coerenza di sessione.
* Risoluzione di un bug che potrebbe causare l'eccezione NullReferenceException in HttpContext quando la frequenza delle richieste è elevata.
* Miglioramento delle prestazioni della modalità DirectHttps.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Aggiunta del supporto del proxy basato su istanza di client semplice con l'API ConnectionPolicy.setProxy().
* Aggiunta dell'API DocumentClient.close() per arrestare correttamente l'istanza di DocumentClient.
* Miglioramento delle prestazioni delle query nella modalità di connettività diretta, tramite derivazione del piano di query dall'assembly nativo invece che dal gateway.
* Impostare FAIL_ON_UNKNOWN_PROPERTIES = false in modo che gli utenti non debbano definire JsonIgnoreProperties in POJO.
* Refactoring della registrazione per usare SLF4J.
* Risoluzione di altri bug nel lettore di coerenza.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Risoluzione di un bug nella gestione della connessione per impedire perdite di connessione nella modalità di connettività diretta.
* Risoluzione di un bug nella query TOP, dove è possibile che venga generata un'eccezione NullReference.
* Miglioramento delle prestazioni tramite la riduzione del numero di chiamate di rete per le cache interne.
* Aggiunta del codice di stato, di ActivityID e dell'URI della richiesta in DocumentClientException per una migliore risoluzione dei problemi.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Risoluzione di un problema nella gestione della connessione per una migliore stabilità.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Aggiunta del supporto per il livello di coerenza BoundedStaleness.
* Aggiunta del supporto per la connettività diretta per le operazioni CRUD delle raccolte partizionate.
* Risoluzione di un bug in una query di un database con SQL.
* Risoluzione di un bug nella cache della sessione in cui i token di sessione possono essere impostati in modo non corretto.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Aggiunta del supporto per le query in parallelo nelle raccolte partizionate.
* Aggiunta del supporto per le query TOP/ORDER BY nelle raccolte partizionate.
* Aggiunta del supporto per la coerenza assoluta.
* Aggiunta del supporto per le richieste basate su nomi quando si utilizza la connettività diretta.
* Correzione rendere ActivityId coerente tra tutti i tentativi di richiesta.
* Correzione del bug correlato alla cache di sessione quando si ricrea una raccolta con lo stesso nome.
* Aggiunta Polygon e LineString DataTypes quando si specifica il criterio di indicizzazione per la raccolta criteri per le query spaziali di geo-fencing.
* Risoluzione dei problemi con Java Doc per Java 1.8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Risolto un bug in PartitionKeyDefinitionMap per memorizzare nella cache le raccolte a partizione singola ed evitare richieste aggiuntive di chiavi di partizione da recuperare.
* Risolto un bug per non eseguire un nuovo tentativo se viene fornito un valore di chiave di partizione errato.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Aggiunta del supporto per gli account di database con più aree.
* Aggiunta del supporto per la ripetizione automatica delle richieste limitate con la possibilità di personalizzare il numero massimo di tentativi e il relativo tempo di attesa massimo.  Vedere RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver basato su codice di partizionamento personalizzato è stato deprecato. Usare le raccolte partizionate per un'archiviazione e una velocità effettiva superiori.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Aggiunta del supporto dei criteri per i tentativi per la limitazione di velocità.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Aggiunta del supporto per la durata (TTL) relativa ai documenti.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementazione delle [raccolte partizionate](partitioning-overview.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Risolto un bug in HashPartitionResolver per generare valori hash in little endian per coerenza con altri SDK.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Versione ignorata per riportare il numero di versione in allineamento con altri SDK

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Supporta l'indice geospaziale
* Convalida della proprietà ID per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementa criteri di indicizzazione V2

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* SDK con disponibilità generale

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro

Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata. Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

> [!WARNING]
> Dopo il 30 maggio 2020, Azure Cosmos DB non effettueranno più correzioni di bug, aggiungono nuove funzionalità e forniranno il supporto per le versioni 1. x di Azure Cosmos DB Java SDK per l'API SQL. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalla versione 1.x dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB.
>
> Dopo il 29 febbraio 2016, Azure Cosmos DB non effettueranno più correzioni di bug, aggiungono nuove funzionalità e forniranno il supporto per le versioni 0. x dell'API Azure Cosmos DB Java SDK per SQL. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalla versione 0. x dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB.


| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.5.1](#2.5.1) |03 giugno 2020 |--- |
| [2.5.0](#2.5.0) |12 maggio 2020 |--- |
| [2.4.7](#2.4.7) |20 febbraio 2020 |--- |
| [2.4.6](#2.4.6) |24 gennaio 2020 |--- |
| [2.4.5](#2.4.5) |10 novembre 2019 |--- |
| [2.4.4](#2.4.4) |24 ottobre 2019 |--- |
| [2.4.2](#2.4.2) |26 settembre 2019 |--- |
| [2.4.1](#2.4.1) |18 luglio 2019 |--- |
| [2.4.0](#2.4.0) |04 maggio 2019 |--- |
| [2.3.0](#2.3.0) |24 aprile 2019 |--- |
| [2.2.3](#2.2.3) |16 aprile 2019 |--- |
| [2.2.2](#2.2.2) |05 aprile 2019 |--- |
| [2.2.0](#2.2.0) |27 marzo 2019 |--- |
| [2.1.3](#2.1.3) |13 marzo 2019 |--- |
| [2.1.2](#2.1.2) |09 marzo 2019 |--- |
| [2.1.1](#2.1.1) |13 dicembre 2018 |--- |
| [2.1.0](#2.1.0) |20 novembre 2018 |--- |
| [2.0.0](#2.0.0) |21 settembre 2018 |--- |
| [1.16.4](#1.16.4) |10 settembre 2018 |30 maggio 2020 |
| [1.16.3](#1.16.3) |9 settembre 2018 |30 maggio 2020 |
| [1.16.2](#1.16.2) |29 giugno 2018 |30 maggio 2020 |
| [1.16.1](#1.16.1) |16 maggio 2018 |30 maggio 2020 |
| [1.16.0](#1.16.0) |15 marzo 2018 |30 maggio 2020 |
| [1.15.0](#1.15.0) |14 novembre 2017 |30 maggio 2020 |
| [1.14.0](#1.14.0) |28 ottobre 2017 |30 maggio 2020 |
| [1.13.0](#1.13.0) |25 agosto 2017 |30 maggio 2020 |
| [1.12.0](#1.12.0) |11 luglio 2017 |30 maggio 2020 |
| [1.11.0](#1.11.0) |10 maggio 2017 |30 maggio 2020 |
| [1.10.0](#1.10.0) |11 marzo 2017 |30 maggio 2020 |
| [1.9.6](#1.9.6) |21 febbraio 2017 |30 maggio 2020 |
| [1.9.5](#1.9.5) |31 gennaio 2017 |30 maggio 2020 |
| [1.9.4](#1.9.4) |24 novembre 2016 |30 maggio 2020 |
| [1.9.3](#1.9.3) |30 ottobre 2016 |30 maggio 2020 |
| [1.9.2](#1.9.2) |28 ottobre 2016 |30 maggio 2020 |
| [1.9.1](#1.9.1) |26 ottobre 2016 |30 maggio 2020 |
| [1.9.0](#1.9.0) |03 ottobre 2016 |30 maggio 2020 |
| [1.8.1](#1.8.1) |30 giugno 2016 |30 maggio 2020 |
| [1.8.0](#1.8.0) |14 giugno 2016 |30 maggio 2020 |
| [1.7.1](#1.7.1) |30 aprile 2016 |30 maggio 2020 |
| [1.7.0](#1.7.0) |27 aprile 2016 |30 maggio 2020 |
| [1.6.0](#1.6.0) |29 marzo 2016 |30 maggio 2020 |
| [1.5.1](#1.5.1) |31 dicembre 2015 |30 maggio 2020 |
| [1.5.0](#1.5.0) |04 dicembre 2015 |30 maggio 2020 |
| [1.4.0](#1.4.0) |05 ottobre 2015 |30 maggio 2020 |
| [1.3.0](#1.3.0) |05 ottobre 2015 |30 maggio 2020 |
| [1.2.0](#1.2.0) |05 agosto 2015 |30 maggio 2020 |
| [1.1.0](#1.1.0) |09 luglio 2015 |30 maggio 2020 |
| 1.0.1 |12 maggio 2015 |30 maggio 2020 |
| [1.0.0](#1.0.0) |07 aprile 2015 |30 maggio 2020 |
| 0.9.5-prelease |09 marzo 2015 |29 febbraio 2016 |
| 0.9.4-prelease |17 febbraio 2015 |29 febbraio 2016 |
| 0.9.3-prelease |13 gennaio 2015 |29 febbraio 2016 |
| 0.9.2-prelease |19 dicembre 2014 |29 febbraio 2016 |
| 0.9.1-prelease |19 dicembre 2014 |29 febbraio 2016 |
| 0.9.0-prelease |10 dicembre 2014 |29 febbraio 2016 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).