---
title: 'Azure Cosmos DB: API Java, risorse e SDK per SQL | Documentazione Microsoft'
description: Informazioni complete sull'SDK e sull'API Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Java SDK.
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 11/14/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 979fcebd60d62c714a1c521013cf391ebf18a73c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Java SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

SQL API Java SDK supporta operazioni sincrone. Per il supporto di operazioni asincrone, usare [SQL API Async Java SDK](sql-api-sdk-async-java.md). 

<table>

<tr><td>**Download dell'SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione a SDK Java](sql-api-java-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-java-application.md)</td></tr>

<tr><td>**Runtime minimo supportato**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

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
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](sql-api-sql-query.md#Aggregates).
* Aggiunta del supporto per la modifica del feed.
* Aggiunta del supporto per informazioni sulla quota della raccolta tramite RequestOptions.setPopulateQuotaInfo.
* Aggiunta del supporto per la registrazione dello script della procedura archiviata tramite RequestOptions.setScriptLoggingEnabled.
* Risoluzione di un bug in cui una query in modalità DirectHttps rischiava di bloccarsi in presenza di errori di limitazione.
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
* Risoluzione di un bug nella query TOP in cui è possibile che venga generata un'eccezione NullReferenece.
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
* Aggiunta del supporto per il criterio di ripetizione per la limitazione.  

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
* Convalida la proprietà id per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri ?, /, #, \, o terminare con uno spazio.
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
> Tutte le versioni dell'SDK per Java di SQL precedenti alla versione **1.0.0** sono state ritirate il **29 febbraio 2016**.
> 
> 

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.15.0](#1.15.0) |14 novembre 2017 |--- |
| [1.14.0](#1.14.0) |28 ottobre 2017 |--- |
| [1.13.0](#1.13.0) |25 agosto 2017 |--- |
| [1.12.0](#1.12.0) |11 luglio 2017 |--- |
| [1.11.0](#1.11.0) |10 maggio 2017 |--- |
| [1.10.0](#1.10.0) |11 marzo 2017 |--- |
| [1.9.6](#1.9.6) |21 febbraio 2017 |--- |
| [1.9.5](#1.9.5) |31 gennaio 2017 |--- |
| [1.9.4](#1.9.4) |24 novembre 2016 |--- |
| [1.9.3](#1.9.3) |30 ottobre 2016 |--- |
| [1.9.2](#1.9.2) |28 ottobre 2016 |--- |
| [1.9.1](#1.9.1) |26 ottobre 2016 |--- |
| [1.9.0](#1.9.0) |03 ottobre 2016 |--- |
| [1.8.1](#1.8.1) |30 giugno 2016 |--- |
| [1.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1.7.1](#1.7.1) |30 aprile 2016 |--- |
| [1.7.0](#1.7.0) |27 aprile 2016 |--- |
| [1.6.0](#1.6.0) |29 marzo 2016 |--- |
| [1.5.1](#1.5.1) |31 dicembre 2015 |--- |
| [1.5.0](#1.5.0) |04 dicembre 2015 |--- |
| [1.4.0](#1.4.0) |05 ottobre 2015 |--- |
| [1.3.0](#1.3.0) |05 ottobre 2015 |--- |
| [1.2.0](#1.2.0) |05 agosto 2015 |--- |
| [1.1.0](#1.1.0) |09 luglio 2015 |--- |
| [1.0.1](#1.0.1) |12 maggio 2015 |--- |
| [1.0.0](#1.0.0) |07 aprile 2015 |--- |
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

