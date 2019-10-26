---
title: 'Azure Cosmos DB: API Java SQL, risorse & SDK'
description: Informazioni complete sull'SDK e sull'API Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: eb8110c953787d1ebf7f01037808ee77db2eb8b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934150"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Java SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
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

SQL API Java SDK supporta operazioni sincrone. Per il supporto di operazioni asincrone, usare [SQL API Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Download dell'SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentazione sull'API**|[Documentazione di riferimento API Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuire all'SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Attività iniziali**|[Introduzione a SDK Java](sql-api-java-get-started.md)|
|**Esercitazione sull'app Web**|[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-java-application.md)|
|**Runtime minimo supportato**|[Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Aggiornamenti della cache degli intervalli di chiavi di partizione ottimizzati.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Aggiornamenti della cache della raccolta ottimizzata.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Aggiunto il supporto per recuperare il messaggio di eccezione interna dalla stringa di diagnostica della richiesta.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* È stata introdotta l'API della versione in PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Aggiunto il supporto del timeout separato per la modalità diretta.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Utilizzo del messaggio di errore null dal servizio e generazione dell'eccezione client del documento.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Miglioramento della connessione socket, aggiungendo SoKeepAlive default true.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Aggiunta del supporto per la stringa di diagnostica della richiesta.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Correzione di un bug in PartitionKey per hash V2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Aggiunta del supporto per gli indici compositi.
* Correzione del bug in Gestione endpoint globale per forzare l'aggiornamento.
* Correzione del bug per Upsert con le condizioni preliminari in modalità diretta.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Correzione del bug nella cache degli indirizzi del gateway.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Aggiunto il supporto per la scrittura in più aree per la modalità diretta.
* Aggiunto il supporto per la gestione di IOExceptions generate come eccezioni ServiceUnavailable, da un proxy.
* Risolto un bug nei criteri di ripetizione dei tentativi di individuazione degli endpoint.
* Risolto un bug per assicurare che non vengano generate eccezioni del puntatore Null in BaseDatabaseAccountConfigurationProvider.
* Correzione di un bug per assicurarsi che QueryIterator non restituisca valori null.
* Risolto un bug per garantire che sia consentito PartitionKey ampio

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Aggiunto il supporto per la scrittura in più aree per la modalità gateway.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Risolto un bug negli intervalli di chiavi di partizione di lettura per una query.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Risolto un bug nell'impostazione delle dimensioni dell'intestazione dei token di continuazione in modalità DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Aggiunta del supporto del failover dello streaming.
* Aggiunta del supporto per i metadati personalizzati.
* Logica di gestione delle sessioni migliorata.
* Correzione di un bug nella cache dell'intervallo di chiavi di partizione.
* Correzione di un bug NPE in modalità diretta.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Aggiunta del supporto per indice univoco.
* Aggiunta del supporto per limitare la dimensione del token di continuazione nelle opzioni di feed.
* Correzione di un bug nella serializzazione Json (timestamp).
* Correzione di un bug nella serializzazione Json (enumerazione).
* Dipendenza su com.fasterxml.jackson.core:jackson-databind.aggiornata a 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Funzionalità migliorata per il pool di connessioni per la modalità diretta.
* Funzionalità migliorata per il piano di query di prelettura per query di partizione non orderby.
* Funzionalità migliorata per la generazione UUID.
* Funzionalità migliorata per la logica di coerenza di sessione.
* Aggiunta di supporto per multipoligoni.
* Aggiunta del supporto per le statistiche dell'intervallo di chiavi di partizione per la raccolta.
* Correzione di un bug in modalità di supporto per più aree.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Miglioramento delle prestazioni di serializzazione Json.
* Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Modifiche interne per le librerie Friend di Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Risolto un problema durante la lettura di singoli intervalli di chiavi di partizione.
* Risolto un problema nell'analisi di ResourceID che interessa il database con nomi brevi.
* Risolto una problema causato dalla codifica di chiavi di partizione.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correzioni di bug importanti per richiedere l'elaborazione durante le suddivisioni delle partizioni.
* Risolto un problema relativo ai livelli di coerenza Strong e BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.
* Risolto un bug nella raccolta in modalità di sessione di lettura.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Supporto abilitato per la raccolta partizionata con valore ridotto di 2.500 UR/s e riduzione con incrementi di 100 UR/s.
* Correzione di un bug nell'assembly nativo che può causare l'eccezione NullRef in alcune query.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Correzione di un bug nella configurazione del motore di query che può generare eccezioni per le query in modalità Gateway.
* Correzione di alcuni bug nel contenitore della sessione che possono generare un'eccezione "Risorsa proprietario non trovata" per le richieste immediatamente dopo la creazione della raccolta.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](sql-query-aggregates.md).
* Aggiunta del supporto per la modifica del feed.
* Aggiunta del supporto per informazioni sulla quota della raccolta tramite RequestOptions.setPopulateQuotaInfo.
* Aggiunta del supporto per la registrazione dello script della procedura archiviata tramite RequestOptions.setScriptLoggingEnabled.
* Correzione di un bug in cui la query in modalità DirectHttps potrebbe smettere di rispondere quando si verificano errori di limitazione.
* Risoluzione di un bug in modalità di coerenza di sessione.
* Risoluzione di un bug che potrebbe causare l'eccezione NullReferenceException in HttpContext quando la frequenza delle richieste è elevata.
* Miglioramento delle prestazioni della modalità DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Aggiunta del supporto del proxy basato su istanza di client semplice con l'API ConnectionPolicy.setProxy().
* Aggiunta dell'API DocumentClient.close() per arrestare correttamente l'istanza di DocumentClient.
* Miglioramento delle prestazioni delle query nella modalità di connettività diretta, tramite derivazione del piano di query dall'assembly nativo invece che dal gateway.
* Impostare FAIL_ON_UNKNOWN_PROPERTIES = false in modo che gli utenti non debbano definire JsonIgnoreProperties in POJO.
* Refactoring della registrazione per usare SLF4J.
* Risoluzione di altri bug nel lettore di coerenza.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Risoluzione di un bug nella gestione della connessione per impedire perdite di connessione nella modalità di connettività diretta.
* Risoluzione di un bug nella query TOP, dove è possibile che venga generata un'eccezione NullReference.
* Miglioramento delle prestazioni tramite la riduzione del numero di chiamate di rete per le cache interne.
* Aggiunta del codice di stato, di ActivityID e dell'URI della richiesta in DocumentClientException per una migliore risoluzione dei problemi.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Risoluzione di un problema nella gestione della connessione per una migliore stabilità.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Aggiunta del supporto per il livello di coerenza BoundedStaleness.
* Aggiunta del supporto per la connettività diretta per le operazioni CRUD delle raccolte partizionate.
* Risoluzione di un bug in una query di un database con SQL.
* Risoluzione di un bug nella cache della sessione in cui i token di sessione possono essere impostati in modo non corretto.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Aggiunta del supporto per le query in parallelo nelle raccolte partizionate.
* Aggiunta del supporto per le query TOP/ORDER BY nelle raccolte partizionate.
* Aggiunta del supporto per la coerenza assoluta.
* Aggiunta del supporto per le richieste basate su nomi quando si utilizza la connettività diretta.
* Correzione rendere ActivityId coerente tra tutti i tentativi di richiesta.
* Correzione del bug correlato alla cache di sessione quando si ricrea una raccolta con lo stesso nome.
* Aggiunta Polygon e LineString DataTypes quando si specifica il criterio di indicizzazione per la raccolta criteri per le query spaziali di geo-fencing.
* Risoluzione dei problemi con Java Doc per Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Risolto un bug in PartitionKeyDefinitionMap per memorizzare nella cache le raccolte a partizione singola ed evitare richieste aggiuntive di chiavi di partizione da recuperare.
* Risolto un bug per non eseguire un nuovo tentativo se viene fornito un valore di chiave di partizione errato.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Aggiunta del supporto per gli account di database con più aree.
* Aggiunta del supporto per la ripetizione automatica delle richieste limitate con la possibilità di personalizzare il numero massimo di tentativi e il relativo tempo di attesa massimo.  Vedere RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver basato su codice di partizionamento personalizzato è stato deprecato. Usare le raccolte partizionate per un'archiviazione e una velocità effettiva superiori.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Aggiunta del supporto dei criteri per i tentativi per la limitazione di velocità.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Aggiunta del supporto per la durata (TTL) relativa ai documenti.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Risolto un bug in HashPartitionResolver per generare valori hash in little endian per coerenza con altri SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Aggiungi resolver per partizioni hash e a intervalli come supporto per applicazioni di partizionamento orizzontale in più partizioni.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versione ignorata per riportare il numero di versione in allineamento con altri SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Supporta l'indice geospaziale
* Convalida la proprietà ID per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa criteri di indicizzazione V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni **1. x** di SQL SDK per Java verranno ritirate il **30 maggio 2020**.
> 
>

> [!WARNING]
> Tutte le versioni dell'SDK per Java di SQL precedenti alla versione **1.0.0** sono state ritirate il **29 febbraio 2016**.
> 
> 

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 marzo, 2018 |--- |
| [2.1.2](#2.1.2) |9 mar 2018 |--- |
| [2.1.1](#2.1.1) |13 dicembre 2018 |--- |
| [2.1.0](#2.1.0) |20 nov, 2018 |--- |
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

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedi anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

