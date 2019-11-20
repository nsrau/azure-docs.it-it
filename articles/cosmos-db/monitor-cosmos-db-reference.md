---
title: Riferimento ai dati di monitoraggio Azure Cosmos DB | Microsoft Docs
description: Informazioni di riferimento su log e metriche per il monitoraggio dei dati da Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f46c9bf53d791eaec68763edc32996847e78ebd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186903"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Riferimento ai dati di monitoraggio Azure Cosmos DB
Questo articolo fornisce un riferimento ai dati di log e delle metriche raccolti per analizzare le prestazioni e la disponibilità dei Azure Cosmos DB. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per Azure Cosmos DB, vedere [Cosmos DB di monitoraggio](monitor-cosmos-db.md) .


## <a name="resource-logs"></a>Log risorse
La tabella seguente elenca le proprietà per i log delle risorse Azure Cosmos DB quando vengono raccolti nei log di monitoraggio di Azure o nell'archiviazione di Azure. Nei log di monitoraggio di Azure vengono raccolti nella tabella **AzureDiagnostics** con un valore **ResourceProvider** pari a *Microsoft. DOCUMENTDB*.

| Proprietà o campo di Archiviazione di Azure | Proprietà dei log di monitoraggio di Azure | DESCRIZIONE |
| --- | --- | --- |
| **time** | **TimeGenerated** | Data e ora (UTC) in cui si è verificata l'operazione. |
| **resourceId** | **Risorsa** | Account Azure Cosmos DB per cui vengono abilitati i log.|
| **category** | **Categoria** | Per i log di Azure Cosmos DB, **DataPlaneRequests** è l'unico valore disponibile. |
| **operationName** | **OperationName** | Nome dell'operazione. Questo valore può essere una delle operazioni seguenti: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| **properties** | N/D | Il contenuto di questo campo è descritto nelle righe seguenti. |
| **activityId** | **activityId_g** | GUID univoco per l'operazione registrata. |
| **userAgent** | **userAgent_s** | Stringa che specifica l'agente utente del client che esegue la richiesta. Il formato è {nome agente utente}/{versione}.|
| **requestResourceType** | **requestResourceType_s** | Tipo di risorsa di accesso. Questo valore può essere uno dei seguenti tipi di risorsa: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction oppure Offer. |
| **statusCode** | **statusCode_s** | Stato di risposta dell'operazione. |
| **requestResourceId** | **ResourceId** | ID risorsa relativo alla richiesta. Il valore può puntare a databaseRid, collectionRid o documentRid a seconda dell'operazione eseguita.|
| **clientIpAddress** | **clientIpAddress_s** | Indirizzo IP del client. |
| **requestCharge** | **requestCharge_s** | Numero di unità riservate usate dall'operazione. |
| **collectionRid** | **collectionId_s** | ID univoco per la raccolta.|
| **duration** | **duration_s** | Durata dell'operazione, in millisecondi. |
| **requestLength** | **requestLength_s** | Lunghezza della richiesta in byte. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Questo valore è non vuoto quando vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione. Il valore punta all'ID risorsa dell'utente. |

## <a name="metrics"></a>Metrica
Le tabelle seguenti elencano le metriche della piattaforma raccolte per Azure CosmOS DB. Tutte le metriche vengono archiviate nello spazio dei nomi **Cosmos DB metriche standard**.

#### <a name="request-metrics"></a>Metriche per le richieste
            
|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione) |DESCRIZIONE|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Totale richieste) | Conteggio (conteggio) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutti | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Utilizzato per monitorare le richieste per codice di stato, contenitore a una granularità di minuti. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (richieste di metadati) |Conteggio (conteggio) | Conteggio delle richieste di metadati. Azure Cosmos DB gestisce il contenitore dei metadati di sistema per ogni account, che consente di enumerare le raccolte, i database e così via e le relative configurazioni, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Tutti| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (richieste Mongo) | Conteggio (conteggio) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

#### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (costo richiesta Mongo) | Conteggio (totale) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (Totale unità richiesta)| Conteggio (totale) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutti| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| ProvisionedThroughput (velocità effettiva con provisioning)| Conteggio (massimo) |Velocità effettiva con provisioning in granularità del contenitore| DatabaseName, ContainerName| 5M| | Usato per monitorare la velocità effettiva con provisioning per ogni contenitore.|

#### <a name="storage-metrics"></a>Metriche di archiviazione

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (archiviazione disponibile) |Byte (totale) | Totale spazio di archiviazione disponibile segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (utilizzo dati) |Byte (totale) |Utilizzo totale dei dati segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti.|
| IndexUsage (utilizzo indici) | Byte (totale) |Totale utilizzo di indici restituiti a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Usato per monitorare l'utilizzo totale dei dati in un contenitore e in un'area, la granularità minima dovrebbe essere di 5 minuti. |
| DocumentQuota (quota documento) | Byte (totale) | Quota di archiviazione totale restituita a una granularità di 5 minuti per area.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Usato per monitorare la quota totale nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|
| DocumentCount (Conteggio documenti) | Conteggio (totale) |Numero totale di documenti segnalato a una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Usato per monitorare il numero di documenti nel contenitore e nell'area, la granularità minima dovrebbe essere di 5 minuti.|

#### <a name="latency-metrics"></a>Metriche di latenza

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| ReplicationLatency (latenza di replica)| MilliSecondi (minimo, massimo, medio) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutti | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |


#### <a name="availability-metrics"></a>Metriche di disponibilità

|Metrica (nome visualizzato della metrica) |Unità (tipo di aggregazione)|DESCRIZIONE| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilità del servizio)| Percentuale (minima, massima) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Rappresenta la percentuale delle richieste passate totali. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


#### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metrica (nome visualizzato della metrica)|Unità (tipo di aggregazione)|DESCRIZIONE|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| CassandraRequests (richieste Cassandra) | Conteggio (conteggio) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutti| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (addebiti per le richieste Cassandra) | Conteggio (Sum, min, Max, AVG) | Unità richiesta utilizzate dalle richieste dell'API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutti| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (chiusure della connessione Cassandra) |Conteggio (conteggio) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutti | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio Azure Cosmos DB, vedere [Azure Cosmos DB di monitoraggio](monitor-cosmos-db.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con](../azure-monitor/insights/monitor-azure-resource.md) monitoraggio di Azure.