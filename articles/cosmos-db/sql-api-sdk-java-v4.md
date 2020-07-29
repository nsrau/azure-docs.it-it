---
title: 'Azure Cosmos DB Java SDK v4 per API SQL: note sulla versione e risorse'
description: Informazioni complete su Azure Cosmos DB Java SDK v4 per SDK e API SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6cd4d64361e1975a6752ff3a15600923ce3fdf98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322891"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 per API Core (SQL): note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 per API Core (SQL) combina un'API asincrona e un'API sincrona in un unico artefatto Maven. V4 SDK offre prestazioni migliorate, nuove funzionalità API e supporto asincrono basato su Project Reactor e sulla [libreria Netty](https://netty.io/). Si prevede quindi che Azure Cosmos DB Java SDK v4 offra prestazioni più elevate rispetto ad [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Queste note sulla versione sono destinate solo ad Azure Cosmos DB Java SDK v4. Se si usa una versione precedente, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento alla versione 4.
>
> Per iniziare rapidamente, eseguire questi tre passaggi.
> 1. Installare la [prima versione supportata di Java Runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) per poter usare l'SDK.
> 2. Consultare la [Guida di avvio rapido per Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), che consente di accedere all'artefatto Maven e prende in esame le richieste di base di Azure Cosmos DB.
> 3. Leggere i [suggerimenti sulle prestazioni](performance-tips-java-sdk-v4-sql.md) e le guide alla [risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4 per ottimizzare l'SDK per l'applicazione.
>
> I [workshop e i laboratori su Azure Cosmos DB](https://aka.ms/cosmosworkshop) costituiscono un'altra risorsa molto utile per imparare a usare Azure Cosmos DB Java SDK v4.
>

| |  |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentazione sull'API** | [Documentazione di riferimento API Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Contribuire all'SDK** | [Repository centrale di Azure SDK per Java in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Operazioni preliminari** | [Avvio rapido: Compilare un'app Java per gestire i dati dell'API SQL di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: Esempi di Java per l'API SQL](sql-api-java-sdk-samples.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App console con feed di modifiche**| [Feed di modifiche - Esempio di Java SDK v4](create-sql-api-java-changefeed.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Esempio di App Web**| [Compilare un'app Web con Java SDK v4](sql-api-java-application.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risolvere i problemi di Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Eseguire la migrazione a v4 da un SDK precedente** | [Eseguire la migrazione a Java v4 SDK](migrate-java-v4-sdk.md) |
| **Runtime minimo supportato**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Cronologia delle versioni

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
* Aggiunta dell'API abilitata per la registrazione degli script a `CosmosStoredProcedureRequestOptions` .
* Il `DirectConnectionConfig` valore predefinito è 1h e il valore predefinito è `idleEndpointTimeout` `connectTimeout` 5S.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione del problema relativo all' `GatewayConnectionConfig` `idleConnectionTimeout` override di `DirectConnectionConfig` `idleConnectionTimeout` .
* Correzione `responseContinuationTokenLimitInKb` delle API Get e set in `CosmosQueryRequestOptions` .
* Correzione del problema relativo al feed delle modifiche e delle query durante la ricreazione della raccolta con lo stesso nome.
* Correzione del problema relativo alla generazione di query Top ClassCastException.
* Correzione di un problema relativo alla generazione della query Order by NullPointerException.
* Correzione del problema relativo alla gestione delle richieste annullate in modalità diretta che causa la chiamata del reattore `onErrorDropped` . 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nuove funzioni e caratteristiche
* Aggiunta del supporto per la `GROUP BY` query.
* Il valore predefinito di maxConnectionsPerEndpoint è stato aumentato a 130 in DirectConnectionConfig.
* Il valore predefinito di maxRequestsPerConnection è stato aumentato a 30 in DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione dei problemi relativi alla query Order by che restituisce risultati duplicati quando si riprende utilizzando il token di continuazione. 
* Correzione di problemi con query value che restituiscono valori null per l'oggetto annidato.
* È stata stabilita un'eccezione del puntatore null sul gestore richieste in RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nuove funzioni e caratteristiche
* Rinominato `QueryRequestOptions` in `CosmosQueryRequestOptions`.
* Aggiornato `ChangeFeedProcessorBuilder` al modello del generatore.
* Aggiornato `CosmosPermissionProperties` con le nuove API nome contenitore e risorse figlio.
* Sono stati aggiunti altri esempi & documenti arricchiti in `CosmosClientBuilder` . 
* `CosmosDatabase`  &  `CosmosContainer` API aggiornate con throughputProperties per il supporto della scalabilità automatica/Autopilot. 
* Rinominato `CosmosClientException` in `CosmosException`. 
* Sostituito `AccessCondition`  &  `AccessConditionType` dalle `ifMatchETag()`  &  `ifNoneMatchETag()` API. 
* Unire tutti i `Cosmos*AsyncResponse`  &  `CosmosResponse` tipi a un singolo `CosmosResponse` tipo.
* Rinominato `CosmosResponseDiagnostics` in `CosmosDiagnostics`.  
* Incluso `FeedResponseDiagnostics` in `CosmosDiagnostics` . 
* È stata rimossa la `jackson` dipendenza da Azure-cosmos & che si basa su Azure-core. 
* Sostituito `CosmosKeyCredential` con il `AzureKeyCredential` tipo. 
* Aggiunta `ProxyOptions` di API a `GatewayConnectionConfig` . 
* Aggiornamento dell'SDK per l'uso `Instant` del tipo anziché di `OffsetDateTime` . 
* Aggiunto nuovo tipo enum `OperationKind` . 
* Rinominato `FeedOptions` in `QueryRequestOptions`. 
* Sono state aggiunte `getETag()`  &  `getTimestamp()` API ai `Cosmos*Properties` tipi. 
* Aggiunta `userAgent` di informazioni in `CosmosException`  &  `CosmosDiagnostics` . 
* Il carattere di nuova riga è stato aggiornato in `Diagnostics` per il carattere di nuova riga del sistema. 
* Le API sono state rimosse `readAll*` . usare invece query selezionare tutte le API.
* Aggiunta dell'API per la `ChangeFeedProcessor` stima del ritardo.   
* Aggiunta del supporto per il provisioning automatico della velocità effettiva di scalabilità automatica per l'SDK.  
* Sostituito `ConnectionPolicy` con nuove configurazioni di connessione. API esposte `DirectConnectionConfig`  &  `GatewayConnectionConfig` tramite `CosmosClientBuilder` per le configurazioni di connessione di tipo Direct & gateway.
* Spostato `JsonSerializable`  &  `Resource` nel pacchetto di implementazione. 
* Aggiunta dell' `contentResponseOnWriteEnabled` API a CosmosClientBuilder che disabilita il contenuto completo della risposta durante le operazioni di scrittura.
* API esposte `getETag()` sui tipi di risposta.
* Spostato `CosmosAuthorizationTokenResolver` nell'implementazione. 
* API rinominata `preferredLocations`  &  `multipleWriteLocations` in `preferredRegions`  &  `multipleWriteRegions` . 
* Aggiornamento `reactor-core` a 3.3.5. release, `reactor-netty` a 0.9.7. Release & `netty` a 4.1.49. final versions. 
* Aggiunta del supporto per `analyticalStoreTimeToLive` in SDK.     
* `CosmosClientException`estende `AzureException` . 
* Le API sono state rimosse `maxItemCount`  &  `requestContinuationToken` `FeedOptions` usando invece `byPage()` API da `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Introdotta `CosmosPermissionProperties` sulla superficie pubblica per le `Permission` API.
* `SqlParameterList`Tipo rimosso & sostituito con`List`
* Correzione di più perdite di memoria nel client TCP diretto. 
* Aggiunta del supporto per le `DISTINCT` query. 
* Sono state rimosse le dipendenze esterne `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Spostato `CosmosPagedFlux`  &  `CosmosPagedIterable` nel `utils` pacchetto. 
* Aggiornamento Netty a 4.1.45. Final & Project Reactor alla versione 3.3.3.
* Contratti REST pubblici aggiornati per `Final` le classi.
* Aggiunta del supporto per la diagnostica avanzata per le operazioni di punto.
* Pacchetto aggiornato in`com.azure.cosmos`
* Aggiunta `models` del pacchetto per i contratti modello/REST
* Aggiunta `utils` del pacchetto per i `CosmosPagedFlux`  &  `CosmosPagedIterable` tipi. 
* API pubbliche aggiornate da usare nell' `Duration` SDK.
* Sono stati aggiunti tutti i contratti Rest al `models` pacchetto.
* `RetryOptions` è stato rinominato in `ThrottlingRetryOptions`.
* Aggiunta `CosmosPagedFlux`  &  `CosmosPagedIterable` dei tipi di paginazione per le API di query. 
* Aggiunto il supporto per la condivisione di TransportClient tra più istanze di CosmosClients usando una nuova API nel`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Ottimizzazioni delle query tramite la rimozione della doppia serializzazione/deserializzazione. 
* Ottimizzazioni delle intestazioni di risposta rimuovendo la copia superflua avanti e indietro. 
* `ByteBuffer`Serializzazione/deserializzazione ottimizzata mediante la rimozione di istanze di stringa intermedie.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione dell' `toString()` eccezione del puntatore null ConnectionPolicy.
* Correzione del problema relativo all'analisi dei risultati della query in caso di query Order by. 
* Problemi di perdita dei socket corretti con il client TCP diretto.
* Correzione del `orderByQuery` bug del token di continuazione.
* `ChangeFeedProcessor`correzione di bug per la gestione di divisioni di partizione & quando la partizione non è stata trovata.
* `ChangeFeedProcessor`correzione di bug durante la sincronizzazione degli aggiornamenti dei lease tra thread diversi.
* Correzione race condition causando `ArrayIndexOutOfBound` un'eccezione in StoreReader

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).