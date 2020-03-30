---
title: Riferimento per il monitoraggio dei dati di Azure Cosmos DB Documenti Microsoft
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588723"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB
Questo articolo fornisce un riferimento ai dati di log e delle metriche raccolti per analizzare le prestazioni e la disponibilità di Azure Cosmos DB. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per Azure Cosmos DB, vedere [Monitoraggio di Cosmos DB.](monitor-cosmos-db.md)


## <a name="resource-logs"></a>Log risorse
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. Nei log di monitoraggio di Azure vengono raccolti nella tabella **AzureDiagnostics** con un valore **ResourceProvider** pari a *MICROSOFT. DOCUMENTDB*. 

| Proprietà o campo di Archiviazione di Azure | Proprietà Log di Monitoraggio di AzureAzure Monitor Logs property | Descrizione |
| --- | --- | --- |
| **time** | **TimeGenerated** | Data e ora (UTC) in cui si è verificata l'operazione. |
| **Resourceid** | **Risorsa** | Account Azure Cosmos DB per cui vengono abilitati i log.|
| **Categoria** | **Category** | Per i log di Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** sono i tipi di log disponibili. |
| **Nomeoperazione** | **Nomeoperazione** | Nome dell'operazione. Questo valore può essere una delle operazioni seguenti: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| **Proprietà** | n/d | Il contenuto di questo campo è descritto nelle righe seguenti. |
| **Activityid** | **activityId_g** | GUID univoco per l'operazione registrata. |
| **Useragent** | **userAgent_s** | Stringa che specifica l'agente utente del client che esegue la richiesta. Il formato è {nome agente utente}/{versione}.|
| **requestResourceType (tipo di richiesta)** | **requestResourceType_s** | Tipo di risorsa di accesso. Questo valore può essere uno dei seguenti tipi di risorsa: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction oppure Offer. |
| **statusCode** | **statusCode_s** | Stato di risposta dell'operazione. |
| **requestResourceId** | **Resourceid** | ID risorsa relativo alla richiesta. Il valore può puntare a databaseRid, collectionRid o documentRid a seconda dell'operazione eseguita.|
| **clientIpAddress** | **clientIpAddress_s** | Indirizzo IP del client. |
| **requestCharge** | **requestCharge_s** | Numero di unità riservate usate dall'operazione. |
| **collectionRid** | **collectionId_s** | ID univoco per la raccolta.|
| **Durata** | **duration_s** | Durata dell'operazione, in millisecondi. |
| **requestLength** | **requestLength_s** | Lunghezza della richiesta in byte. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Questo valore è non vuoto quando vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione. Il valore punta all'ID risorsa dell'utente. |

Per un elenco di tutte le categorie di log di Monitoraggio di Azure e collegamenti agli schemi associati, vedere Categorie e schemi di log di monitoraggio di [Azure.](../azure-monitor/platform/diagnostic-logs-schema.md) 

## <a name="metrics"></a>Metriche
The following tables list the platform metrics collected for Azure CosmOS DB. Tutte le metriche vengono archiviate nello spazio dei nomi **Cosmos DB standard metrics**.

Per un elenco di tutte le metriche di supporto di Monitoraggio di Azure (incluso CosmosDB), vedere [Metriche supportate](../azure-monitor/platform/metrics-supported.md)di Monitoraggio di Azure. 

#### <a name="request-metrics"></a>Metriche per le richieste
            
|Metrica (nome visualizzato metrico)|Unità (tipo di aggregazione) |Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| TotaleRichieste (richieste totali) | Conteggio (conteggio) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutti | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Utilizzato per monitorare le richieste per codice di stato, contenitore con granularità minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (richieste di metadati) |Conteggio (conteggio) | Conteggio delle richieste di metadati. Azure Cosmos DB gestisce il contenitore di metadati di sistema per ogni account, che consente di enumerare gratuitamente raccolte, database e così via e le relative configurazioni. | DatabaseName, CollectionName, Region, StatusCode| Tutti| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (richieste Mongo) | Conteggio (conteggio) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

#### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metrica (nome visualizzato metrico)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Addebito richiesta mongo) | Conteggio (totale) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutti |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (unità richieste totali)| Conteggio (totale) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutti| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| Provisioning di Throughput (velocità effettiva di cui è stato eseguito il provisioning)ProvisionedThroughput (Provisioned Throughput)| Conteggio (massimo) |Velocità effettiva di provisioning alla granularità del contenitoreProvisioned throughput at container granularity| DatabaseName, NomeContenitore| 5M| | Utilizzato per monitorare la velocità effettiva di cui è stato eseguito il provisioning per ogni contenitore.|

#### <a name="storage-metrics"></a>Metriche di archiviazione

|Metrica (nome visualizzato metrico)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (spazio di archiviazione disponibile) |Byte (totale) | Spazio di archiviazione totale disponibile segnalato con una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (utilizzo dati) |Byte (totale) |Utilizzo totale dei dati riportato con una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Utilizzato per monitorare l'utilizzo totale dei dati in contenitore e area, la granularità minima deve essere di 5 minuti.|
| IndexUsage (Index Usage) | Byte (totale) |Utilizzo totale dell'indice riportato con una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Utilizzato per monitorare l'utilizzo totale dei dati in contenitore e area, la granularità minima deve essere di 5 minuti. |
| DocumentQuota (quota documento) | Byte (totale) | Quota di archiviazione totale segnalata con una granularità di 5 minuti per area.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Utilizzato per monitorare la quota totale in contenitore e area, la granularità minima deve essere di 5 minuti.|
| DocumentCount (conteggio documenti) | Conteggio (totale) |Numero totale di documenti segnalati con una granularità di 5 minuti per area| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Utilizzato per monitorare il numero di documenti in contenitore e area, la granularità minima deve essere di 5 minuti.|

#### <a name="latency-metrics"></a>Metriche di latenza

|Metrica (nome visualizzato metrico)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Uso |
|---|---|---|---| ---| ---|
| ReplicaLatenz (Latenza di replica)| MilliSeconds (minimo, massimo, medio) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutti | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |
| Latenza lato server| MilliSeconds (media) | Tempo impiegato dal server per elaborare la richiesta. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Area | Tutti | Utilizzato per monitorare la latenza delle richieste nel server di database Cosmos di Azure.Used to monitor the request latency on the Azure Cosmos DB server. |



#### <a name="availability-metrics"></a>Metriche di disponibilità

|Metrica (nome visualizzato metrico) |Unità (tipo di aggregazione)|Descrizione| Granularità temporali| Mapping metrica legacy | Uso |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilità del servizio)| Percentuale (minima, massima) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Rappresenta la percentuale di richieste totali superate. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


#### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metrica (nome visualizzato metrico)|Unità (tipo di aggregazione)|Descrizione|Dimensioni| Granularità temporali| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra richiede) | Conteggio (conteggio) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutti| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (Cassandra Richiedi addebiti) | Conteggio (Somma, Min, Max, Media) | Unità richiesta utilizzate dalle richieste dell'API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutti| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (Chiusure di connessione Cassandra) |Conteggio (conteggio) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutti | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="see-also"></a>Vedere anche

- Vedere Monitoraggio di [Azure Cosmos DB](monitor-cosmos-db.md) per una descrizione del monitoraggio di Azure Cosmos DB.
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere Monitoraggio delle risorse di [Azure con Monitoraggio di Azure.See Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
