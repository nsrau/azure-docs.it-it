---
title: 'Azure Cosmos DB: SQL Async Java API, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API SQL Async Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 156699b8d8c1a645961f4e919bdd843d995a3d18
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142639"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK per API SQL: risorse e note sulla versione
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

SQL API Async Java SD differisce da SQL API Java SDK offrendo operazioni asincrone con il supporto della libreria [Netty](https://netty.io/). La versione pre-esistente [SQL API Java SDK](sql-api-sdk-java.md) non supporta operazioni asincrone. 

| |  |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentazione sull'API** |[Documentazione di riferimento API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Contribuire all'SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Introduzione** | [Introduzione ad Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Codice di esempio** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Suggerimenti per incrementare le prestazioni**| [File Leggimi di GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Runtime minimo supportato**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Modalità TCP ora attiva per impostazione predefinita
* Le metriche delle query in Cross-Partition restituiscono ora tutte le partizioni
* Global Strong ora funziona correttamente
* Failover per le query che non eseguono correttamente tentativi per il multimaster
* Urti di dipendenza per gli hotfix di sicurezza

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Supporto di bugfix per hash V2

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Bugfix per la perdita di risorse nel client # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Aggiunto il supporto del token di continuazione per le query tra partizioni.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Correzione di alcuni bug in modalità diretta.
* Registrazione migliorata in modalità diretta.
* Gestione delle connessioni migliorata.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* La connettività in modalità diretta è ora disponibile a livello generale. Per un esempio di uso della connettività in modalità diretta, vedere il repository GitHub [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java).
* Aggiunta del supporto per QueryMetrics.
* Modifica delle API che accettano java.util.Collection per il cui ordine è invece importante accettare java.util.List. Adesso ConnectionPolicy#getPreferredLocations(), JsonSerialization e PartitionKey(.) accettano List.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Aggiunta del supporto per la connettività in modalità diretta.
* Modifica delle API che accettano java.util.Collection per il cui ordine è invece importante accettare java.util.List.
  Adesso ConnectionPolicy#getPreferredLocations(), JsonSerialization e PartitionKey(.) accettano List.
* Risolto un bug di sessione per la query di documenti in modalità gateway.
* Aggiornamento delle dipendenze (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79) e RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Correzioni nella gestione delle risposte alle query di dimensioni molto grandi.
* Correzioni nella gestione di token di risorsa quando si creano istanze client ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Aggiornata dipendenza vulnerabile jackson-databind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Risolto un bug di perdita di risorse.
* Aggiunta del supporto per multipoligoni
* Aggiunta del supporto per le intestazioni personalizzate in RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Risolto un bug di creazione dei pacchetti.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Risolto un bug NPE nel percorso di ripetizione dei tentativi di scrittura.
* Risolto un bug NPE nella gestione di endpoint.
* Aggiornate le dipendenze vulnerabili ([GitHub n. 68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Aggiunta di supporto per la registrazione sulla rete di Netty per la risoluzione dei problemi.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Aggiunta di supporto per le scritture in più aree.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Ulteriore supporto per Proxy.
* Ulteriore supporto per l'autorizzazione del token della risorsa.
* Correzione di un bug nella gestione delle chiavi di partizione di grandi dimensioni ([GitHub n. 63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Migliore qualità della documentazione.
* SDK riorganizzato in più moduli granulari.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Risolto un bug per impostazioni locali non di lingua inglese ([GitHub n. 51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Aggiunti metodi di supporto nella risorsa dei conflitti.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Sostituzione della dipendenza da org.json con jackson per ottimizzare le prestazioni e la gestione delle licenze ([GitHub n. 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Rimozione della classe OfferV2 obsoleta.
* Aggiunta del metodo della funzione di accesso alla classe Offer per il contenuto di velocità effettiva.
* Modifica di qualsiasi metodo in Document/Resource che restituisce i tipi org.json in modo che restituisca un tipo di oggetto jackson.
* Modifica del metodo getObject(.) delle classi che estendono JsonSerializable in modo che restituiscano un tipo ObjectNode jackson.
* Modifica del metodo getCollection(.) in modo che restitusca la Collection di ObjectNode.
* Rimozione dei costruttori delle sottoclassi JsonSerializable con l'arg org.json.JSONObject.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) usa ora due spazi per il rientro.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Aggiunta del supporto per il criterio indice univoco.
* Aggiunta del supporto per limitare la dimensione del token di continuazione della risposta nelle opzioni di feed.
* Aggiunta del supporto per la suddivisione della partizione in Cross Query della partizione.
* Correzione di un bug nella serializzazione Json Timestamp ([GitHub n. 32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correzione di un bug nella serializzazione di enumerazione Json.
* Correzione di un bug nella gestione di documenti della dimensione di 2 MB ([GitHub n. 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dipendenza com.fasterxml.jackson.core:jackson-databind aggiornata a 2.9.5 a causa di un bug ([jackson databind: GitHub n. 1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Dipendenza da rxjava-extras aggiornata a 0.8.0.17 a causa di un bug ([rxjava-extras: GitHub n. 30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* La descrizione dei metadati nel file pom è aggiornata in modo da essere in linea con il resto della documentazione.
* Funzionalità migliorata per la sintassi ([GitHub n. 41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub n. 40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Aggiunta del supporto della congestione nella query.
* Aggiunta del supporto per l'ID di intervallo di chiavi di partizione nella query.
* Correzione per consentire un token di continuazione maggiore nell'intestazione della richiesta (correzione bug GitHub n. 24).
* Dipendenza Netty aggiornata a 4.1.22.Final per assicurare l'arresto di JVM al termine del thread principale.
* Correzione per evitare il passaggio del token di sessione durante la lettura delle risorse master.
* Aggiunta di altri esempi.
* Aggiunta di altri scenari di benchmarking.
* Correzione dei file di intestazione Java per la corretta generazione di documenti Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK disponibile a livello generale con supporto completo di operazioni I/O non di blocco tramite la [libreria Netty](https://netty.io/) in modalità gateway. 

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Nuove caratteristiche, funzionalità e ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre il prima possibile l'aggiornamento alla versione più recente dell'SDK.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni **1. x** di Async Java SDK per SQL API verranno ritirate il **30 agosto 2020**.
> 
>
<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5 mar 2019|--- |
| [2.4.2](#2.4.2) |1 mar 2019|--- |
| [2.4.1](#2.4.1) |20 feb, 2019|--- |
| [2.4.0](#2.4.0) |8 febbraio 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 febbraio 2019|--- |
| [2.3.1](#2.3.1) |15 gen 2019|--- |
| [2.3.0](#2.3.0) |29 novembre 2018|--- |
| [2.2.2](#2.2.2) |8 novembre 2018|--- |
| [2.2.1](#2.2.1) |2 novembre 2018|--- |
| [2.2.0](#2.2.0) |22 settembre 2018|--- |
| [2.1.0](#2.1.0) |5 settembre 2018|--- |
| [2.0.1](#2.0.1) |16 agosto 2018|--- |
| [2.0.0](#2.0.0) |20 giugno 2018|--- |
| [1.0.2](#1.0.2) |18 maggio 2018|30 agosto 2020 |
| [1.0.1](#1.0.1) |20 aprile 2018|30 agosto 2020 |
| [1.0.0](#1.0.0) |27 febbraio 2018|30 agosto 2020 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

