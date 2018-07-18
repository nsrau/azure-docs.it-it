---
title: 'Azure Cosmos DB: SQL Async Java API, SDK e risorse | Microsoft Docs'
description: Informazioni complete sull'SDK e sull'API SQL Async Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/20/2018
ms.author: sngun
ms.openlocfilehash: e4a3b3a482f56065c54525a4d9cd7971f50f5b2a
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300680"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK per API SQL: risorse e note sulla versione
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

SQL API Async Java SD differisce da SQL API Java SDK offrendo operazioni asincrone con il supporto della libreria [Netty](http://netty.io/). La versione pre-esistente [SQL API Java SDK](sql-api-sdk-java.md) non supporta operazioni asincrone. 

<table>

<tr><td>**Download dell'SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Contribuire all'SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione ad Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Codice di esempio**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Suggerimenti per incrementare le prestazioni**</td><td>[File Leggimi di Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Runtime minimo supportato**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Sostituzione della dipendenza da org.json con jackson per ottimizzare le prestazioni e la gestione delle licenze ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Correzione di un bug nella serializzazione Json Timestamp ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correzione di un bug nella serializzazione di enumerazione Json.
* Correzione di un bug nella gestione di documenti della dimensione di 2MB ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dipendenza com.fasterxml.jackson.core:jackson-databind aggiornata a 2.9.5 a causa di un bug ([jackson databind: github 1599 #](https://github.com/FasterXML/jackson-databind/issues/1599))
* Dipendenza da rxjava-extras aggiornata a 0.8.0.17 a causa di un bug ([rxjava extra: github 30 #](https://github.com/davidmoten/rxjava-extras/issues/30)).
* La descrizione dei metadati nel file pom è aggiornata in modo da essere in linea con il resto della documentazione.
* Funzionalità migliorata per la sintassi ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github 40 #](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Aggiunta del supporto della congestione nella query.
* Aggiunta del supporto per l'ID di intervallo di chiavi di partizione nella query.
* Correzione per consentire un token di continuazione maggiore nell'intestazione della richiesta (correzione bug github #24).
* Dipendenza Netty aggiornata a 4.1.22.Final per assicurare l'arresto di JVM al termine del thread principale.
* Correzione per evitare il passaggio del token di sessione durante la lettura delle risorse master.
* Aggiunta di altri esempi.
* Aggiunta di altri scenari di benchmarking.
* Correzione dei file di intestazione Java per la corretta generazione di documenti Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK disponibile a livello generale con supporto completo di operazioni I/O non di blocco tramite la [libreria Netty](http://netty.io/) in modalità gateway. 

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Nuove caratteristiche, funzionalità e ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre il prima possibile l'aggiornamento alla versione più recente dell'SDK.

Qualsiasi richiesta inviata a Cosmos DB con un SDK ritirato verrà rifiutata dal servizio.

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.0.0](#2.0.0) |20 giugno 2018|--- |
| [1.0.2](#1.0.2) |18 maggio 2018|--- |
| [1.0.1](#1.0.1) |20 aprile 2018|--- |
| [1.0.0](#1.0.0) |27 febbraio 2018|--- |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche 
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

