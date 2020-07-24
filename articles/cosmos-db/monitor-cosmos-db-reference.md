---
title: Riferimento ai dati di monitoraggio Azure Cosmos DB | Microsoft Docs
description: Informazioni di riferimento su log e metriche per il monitoraggio dei dati da Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: how-to
ms.date: 07/17/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: 89dc81cdd06bedb6237cf48312ee7ed0510d93ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084739"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB

Questo articolo fornisce un riferimento ai dati di log e delle metriche raccolti per analizzare le prestazioni e la disponibilità di Azure Cosmos DB. Per informazioni su come raccogliere e analizzare i dati di monitoraggio per Azure Cosmos DB, vedere l'articolo di monitoraggio [Azure Cosmos DB](monitor-cosmos-db.md) .

## <a name="resource-logs"></a>Log risorse

La tabella seguente elenca le proprietà dei log delle risorse in Azure Cosmos DB. I log delle risorse vengono raccolti nei log di monitoraggio di Azure o nell'archiviazione di Azure. In monitoraggio di Azure, i log vengono raccolti nella tabella **AzureDiagnostics** con il nome del provider di risorse * * `MICROSOFT.DOCUMENTDB` .

| Proprietà o campo di Archiviazione di Azure | Proprietà dei log di monitoraggio di Azure | Descrizione |
| --- | --- | --- |
| **time** | **TimeGenerated** | Data e ora (UTC) in cui si è verificata l'operazione. |
| **resourceId** | **Risorsa** | Account Azure Cosmos DB per cui vengono abilitati i log.|
| **category** | **Categoria** | Per Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** sono i tipi di log disponibili. |
| **operationName** | **NomeOperazione** | Nome dell'operazione. Il nome dell'operazione può essere `Create` ,, `Update` `Read` , `ReadFeed` , `Delete` , `Replace` , `Execute` , `SqlQuery` , `Query` , `JSQuery` , `Head` , `HeadFeed` o `Upsert` .   |
| **properties** | n/d | Il contenuto di questo campo è descritto nelle righe seguenti. |
| **activityId** | **activityId_g** | GUID univoco per l'operazione registrata. |
| **userAgent** | **userAgent_s** | Stringa che specifica l'agente utente del client da cui è stata inviata la richiesta. Il formato dell'agente utente è `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Tipo di risorsa di accesso. Questo valore può essere un database, un contenitore, un documento, un allegato, un utente, un'autorizzazione, una stored procedure, un trigger, una funzione definita dall'utente o un'offerta. |
| **statusCode** | **statusCode_s** | Stato di risposta dell'operazione. |
| **requestResourceId** | **ResourceId** | ID risorsa relativo alla richiesta. A seconda dell'operazione eseguita, questo valore può puntare a `databaseRid` , `collectionRid` o `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Indirizzo IP del client. |
| **requestCharge** | **requestCharge_s** | Numero di ur/sec utilizzati dall'operazione. |
| **collectionRid** | **collectionId_s** | ID univoco per la raccolta.|
| **duration** | **duration_d** | Durata dell'operazione, in millisecondi. |
| **requestLength** | **requestLength_s** | Lunghezza della richiesta in byte. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Questo valore è non vuoto quando vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione. Il valore punta all'ID risorsa dell'utente. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|

Per un elenco di tutte le categorie di log di monitoraggio di Azure e i collegamenti agli schemi associati, vedere [categorie e schemi dei log di monitoraggio di Azure](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Metriche
Le tabelle seguenti elencano le metriche della piattaforma raccolte per Azure CosmOS DB. Tutte le metriche vengono archiviate nello spazio dei nomi **Cosmos DB metriche standard**.

Per un elenco di tutte le metriche di supporto di monitoraggio di Azure (incluse Azure Cosmos DB), vedere [metriche supportate di monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Metriche per le richieste
            
|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione) |Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Totale richieste) | Conteggio (conteggio) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutti | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Utilizzato per monitorare le richieste per codice di stato, contenitore a una granularità di minuti. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (richieste di metadati) |Conteggio (conteggio) | Conteggio delle richieste di metadati. Azure Cosmos DB gestisce il contenitore dei metadati di sistema per ogni account, che consente di enumerare le raccolte, i database e così via e le relative configurazioni, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Tutti| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (richieste Mongo) | Conteggio (conteggio) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

#### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (costo richiesta Mongo) | Conteggio (totale) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (Totale unità richiesta)| Conteggio (totale) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutti| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| ProvisionedThroughput (velocità effettiva con provisioning)| Conteggio (massimo) |Velocità effettiva con provisioning in granularità del contenitore| DatabaseName, ContainerName| 5M| | Usato per monitorare la velocità effettiva con provisioning per ogni contenitore.|

#### <a name="storage-metrics"></a>Metriche di archiviazione

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (archiviazione disponibile) |Byte (totale) | Totale spazio di archiviazione disponibile segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (utilizzo dati) |Byte (totale) |Utilizzo totale dei dati segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti.|
| IndexUsage (utilizzo indici) | Byte (totale) |Totale utilizzo di indici restituiti a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti. |
| DocumentQuota (quota documento) | Byte (totale) | Quota di archiviazione totale restituita a una granularità di 5 minuti per area.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Usato per monitorare la quota totale nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|
| DocumentCount (Conteggio documenti) | Conteggio (totale) |Numero totale di documenti segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Usato per monitorare il numero di documenti nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|

#### <a name="latency-metrics"></a>Metriche di latenza

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| ReplicationLatency (latenza di replica)| MilliSecondi (minimo, massimo, medio) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutti | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |
| Latenza lato server| MilliSecondi (media) | Tempo impiegato dal server per elaborare la richiesta. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Region | Tutti | Utilizzato per monitorare la latenza delle richieste nel server Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Metriche di disponibilità

|Metrica (nome visualizzato della metrica) |Unità (tipo di aggregazione)|Descrizione| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilità del servizio)| Percentuale (minima, massima) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Rappresenta la percentuale delle richieste passate totali. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


#### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| CassandraRequests (richieste Cassandra) | Conteggio (conteggio) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutti| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (addebiti per le richieste Cassandra) | Conteggio (Sum, min, Max, AVG) | Unità richiesta utilizzate dal API Cassandra | DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutti| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (chiusure della connessione Cassandra) |Conteggio (conteggio) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutti | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio Azure Cosmos DB, vedere [Azure Cosmos DB di monitoraggio](monitor-cosmos-db.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md).
