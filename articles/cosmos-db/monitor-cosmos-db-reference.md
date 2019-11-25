---
title: Azure Cosmos DB monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456521"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB monitoring data reference
This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Cosmos DB. See [Monitoring Cosmos DB](monitor-cosmos-db.md) for details on collecting and analyzing monitoring data for Azure Cosmos DB.


## <a name="resource-logs"></a>Log risorse
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. In Azure Monitor Logs, they're collected in the **AzureDiagnostics** table with a **ResourceProvider** value of *MICROSOFT.DOCUMENTDB*. 

| Proprietà o campo di Archiviazione di Azure | Azure Monitor Logs property | Description |
| --- | --- | --- |
| **time** | **TimeGenerated** | Data e ora (UTC) in cui si è verificata l'operazione. |
| **resourceId** | **Risorsa** | Account Azure Cosmos DB per cui vengono abilitati i log.|
| **category** | **Categoria** | For Azure Cosmos DB logs, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** are the available log types. |
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
| **duration** | **duration_s** | The duration of the operation, in milliseconds. |
| **requestLength** | **requestLength_s** | Lunghezza della richiesta in byte. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Questo valore è non vuoto quando vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione. Il valore punta all'ID risorsa dell'utente. |

For a list of all Azure Monitor log categories and links to associated schemas, see [Azure Monitor Logs categories and schemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Metriche
The following tables list the platform metrics collected for Azure CosmOS DB. All metrics are stored in the namespace **Cosmos DB standard metrics**.

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Metriche per le richieste
            
|Metric (Metric Display Name)|Unit (Aggregation Type) |Description|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total Requests) | Count (Count) | Numero di richieste eseguite| DatabaseName, CollectionName, Region, StatusCode| Tutto | Totale richieste, Http 2xx, Http 3xx, Http 400, Http 401, Errore server interno, Servizio non disponibile, Richieste limitate, Media richieste al secondo | Used to monitor requests per status code, container at a minute granularity. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60. |
| MetadataRequests (Metadata Requests) |Count (Count) | Conteggio delle richieste di metadati. Azure Cosmos DB maintains system metadata container for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge. | DatabaseName, CollectionName, Region, StatusCode| Tutto| |Usata per monitorare le limitazioni dovute a richieste di metadati.|
| MongoRequests (Mongo Requests) | Count (Count) | Numero di richieste Mongo eseguite | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutto |Mongo Query Request Rate (Frequenza richieste di query Mongo), Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo), Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo), Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo), Mongo Count Request Rate (Frequenza richieste di conteggio Mongo)| Usata per monitore gli errori delle richieste Mongo, utilizzi per tipo di comando. |

#### <a name="request-unit-metrics"></a>Metriche delle unità richiesta

|Metric (Metric Display Name)|Unit (Aggregation Type)|Description|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Count (Total) |Unità richiesta Mongo utilizzate| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tutto |Mongo Query Request Charge (Addebito richieste di query Mongo), Mongo Update Request Charge (Addebito richieste di aggiornamento Mongo), Mongo Delete Request Charge (Addebito richieste di eliminazione Mongo), Mongo Insert Request Charge (Addebito richieste di inserimento Mongo), Mongo Count Request Charge (Addebito richieste di conteggio Mongo)| Usata per monitorare le unità richiesta Mongo in un minuto.|
| TotalRequestUnits (Total Request Units)| Count (Total) | Unità richiesta utilizzate| DatabaseName, CollectionName, Region, StatusCode |Tutto| TotalRequestUnits| Usata per monitorare l'utilizzo delle unità richiesta totali con una granularità di un minuto. Per ottenere la media delle unità richiesta utilizzate al secondo, usare il tipo di aggregazione Totale al minuto e dividere per 60.|
| ProvisionedThroughput (Provisioned Throughput)| Count (Maximum) |Provisioned throughput at container granularity| DatabaseName, ContainerName| 5M| | Used to monitor provisioned throughput per container.|

#### <a name="storage-metrics"></a>Metriche di archiviazione

|Metric (Metric Display Name)|Unit (Aggregation Type)|Description|Dimensioni| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Bytes (Total) | Total available storage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M| Spazio di archiviazione disponibile| Usata per monitorare la capacità di archiviazione disponibile (applicabile solo per le raccolte di archiviazioni fisse). La granularità minima deve essere di 5 minuti.| 
| DataUsage (Data Usage) |Bytes (Total) |Total data usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M |Dimensioni dei dati | Used to monitor total data usage at container and region, minimum granularity should be 5 minutes.|
| IndexUsage (Index Usage) | Bytes (Total) |Total Index usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M| Dimensioni dell'indice| Used to monitor total data usage at container and region, minimum granularity should be 5 minutes. |
| DocumentQuota (Document Quota) | Bytes (Total) | Total storage quota reported at 5-minutes granularity per region.| DatabaseName, CollectionName, Region| 5M |Capacità di archiviazione| Used to monitor total quota at container and region, minimum granularity should be 5 minutes.|
| DocumentCount (Document Count) | Count (Total) |Total document count reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M |Conteggio documenti|Used to monitor document count at container and region, minimum granularity should be 5 minutes.|

#### <a name="latency-metrics"></a>Metriche di latenza

|Metric (Metric Display Name)|Unit (Aggregation Type)|Description|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replication Latency)| MilliSeconds (Minimum, Maximum, Average) | Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica| SourceRegion, TargetRegion| Tutto | Usata per monitorare la latenza di replica P99 tra due aree per un account abilitato per la replica geografica. |


#### <a name="availability-metrics"></a>Metriche di disponibilità

|Metric (Metric Display Name) |Unit (Aggregation Type)|Description| Granularità temporali| Mapping metrica legacy | Utilizzo |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service Availability)| Percent (Minimum, Maximum) | Disponibilità delle richieste di account con una granularità di un'ora| 1H | Disponibilità del servizio | Represents the percent of total passed requests. Una richiesta viene considerata non riuscita a causa di un errore di sistema se il codice di stato è 410, 500 o 503. Usata per monitorare la disponibilità dell'account con una granularità di un'ora. |


#### <a name="cassandra-api-metrics"></a>Metriche dell'API Cassandra

|Metric (Metric Display Name)|Unit (Aggregation Type)|Description|Dimensioni| Granularità temporali| Utilizzo |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (Count) | Numero di richieste dell'API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tutto| Usata per monitorare le richieste di Cassandra con una granularità di un minuto. Per ottenere la media delle richieste al secondo, usare il tipo di aggregazione Conteggio al minuto e dividere per 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Count (Sum, Min, Max, Avg) | Unità richiesta utilizzate dalle richieste dell'API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tutto| Usata per monitorare le UR usate al minuto da un account dell'API Cassandra.|
| CassandraConnectionClosures (Cassandra Connection Closures) |Count (Count) |Numero di connessioni a Cassandra chiuse| ClosureReason, Region| Tutto | Usata per monitorare la connettività tra i client e l'API Cassandra di Azure Cosmos DB.|

## <a name="see-also"></a>Vedere anche

- See [Monitoring Azure Cosmos DB](monitor-cosmos-db.md) for a description of monitoring Azure Cosmos DB.
- See [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
