---
title: 'Data Factory: log delle modifiche dell&quot;API .NET | Microsoft Docs'
description: "Vengono descritte le modifiche rilevanti, le aggiunte di funzionalità, le correzioni di bug e così via in una versione specifica dell&quot;API .NET per Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 06702281ab2a44641231783aa1ddad87c22095e8
ms.lasthandoff: 03/29/2017


---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory: log delle modifiche dell'API .NET
In questo articolo vengono fornite informazioni sulle modifiche apportate all'SDK di Azure Data Factory in una versione specifica. La versione più recente del pacchetto NuGet per Azure Data Factory è disponibile [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versione 4.11.0
Aggiunte di funzionalità

* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versione 4.10.0
* A TextFormat sono state aggiunte le proprietà facoltative seguenti:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Aggiunta della proprietà [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) ad AzureMLBatchExecutionActivity
  * Possibilità di passare più input di servizi Web a un esperimento di Azure Machine Learning

## <a name="version-491"></a>Versione 4.9.1
### <a name="bug-fix"></a>Correzione di bug
* Deprecazione dell'autenticazione basata su WebApi per [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versione 4.9.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta delle proprietà [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Per informazioni dettagliate sulla funzionalità, vedere [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Correzione di bug
* Introduzione di un overload del metodo [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) che usa un'istanza [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
* Contrassegno di [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) come facoltativi in CopySink.

## <a name="version-480"></a>Versione 4.8.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Le proprietà facoltative seguenti sono state aggiunte al tipo di attività copia per abilitare l'ottimizzazione delle prestazioni di copia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versione 4.7.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta del nuovo tipo StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) per la copia dei file in formato ORC (Optimized Row Columnar).
* Aggiunta delle proprietà [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
  * Abilita l'uso di PolyBase per la copia di dati in SQL Data Warehouse.

## <a name="version-461"></a>Versione 4.6.1
### <a name="bug-fixes"></a>Correzioni di bug
* Corregge la richiesta HTTP relativa all'elenco delle finestre attività.
  * Rimuove il nome del gruppo di risorse e quello della data factory dal payload della richiesta.

## <a name="version-460"></a>Versione 4.6.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* A [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)sono state aggiunte le proprietà seguenti:
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* A [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)sono state aggiunte le proprietà seguenti:
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Aggiunta del nuovo tipo [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) per definire i set di dati con dati in formato JSON.

## <a name="version-450"></a>Versione 4.5.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta di [operazioni di tipo elenco per la finestra attività](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Aggiunta di metodi per recuperare le finestre attività con filtri basati sui tipi di entità (data factory, set di dati, pipeline e attività).
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versione 4.4.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Il seguente tipo di servizio collegato è stato aggiunto come origini dati e sink per le attività di copia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Servizio collegato di firma di accesso condiviso Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Versione 4.3.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* I seguenti tipi di servizi collegati sono stati aggiunti come origini dati per le attività di copia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Spostare dati da HDFS con Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Spostare dati da archivi dati ODBC con Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>Versione 4.2.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* È stato aggiunto il nuovo tipo di attività seguente: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Per informazioni dettagliate sull'attività, vedere [Aggiornamento di modelli di Azure ML tramite l'attività Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md).
* Una nuova proprietà facoltativa [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) è stata aggiunta alla [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* Le proprietà [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) sono state aggiunte alla classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
* Consentire la configurazione dei timeout per le chiamate client al servizio Data factory.

## <a name="version-410"></a>Versione 4.1.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Sono stati aggiunti i seguenti tipi di attività:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Sono stati aggiunti i seguenti tipi di origine e sink per Attività di copia:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versione 4.0.1
### <a name="breaking-changes"></a>Modifiche di rilievo
Le classi seguenti sono state rinominate. I nuovi nomi sono i nomi originali delle classi prima della versione 4.0.0.

| Nome nella versione 4.0.0 | Nome nella versione 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versione 4.0.0
### <a name="breaking-changes"></a>Modifiche di rilievo
* Le classi/interfacce seguenti sono state rinominate.

| Nome precedente | Nuovo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabella |[Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* I metodi **List** ora restituiscono risultati di paging. Se la risposta contiene una proprietà **NextLink** non vuota, l'applicazione client deve continuare il recupero della pagina successiva finché non vengono restituite tutte le pagine.  Di seguito è fornito un esempio:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** restituisce solo il riepilogo di una pipeline anziché i dettagli completi. Ad esempio, le attività in un riepilogo delle pipeline può contenere solo il nome e il tipo.

### <a name="feature-additions"></a>Aggiunte di funzionalità
* La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) supporta due nuove proprietà, ovvero **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, a supporto della copia idempotente in SQL Data Warehouse di Azure. Per informazioni dettagliate su queste proprietà, vedere l'articolo [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md).
* Ora è supportata l'esecuzione di stored procedure su origini di database SQL di Azure e SQL Data Warehouse di Azure come parte dell'attività di copia. Le classi [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) hanno le proprietà **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Vedere gli articoli [Database SQL di Azure](data-factory-azure-sql-connector.md#sqlsource) e [SQL Data Warehouse di Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) su Azure.com per informazioni dettagliate su queste proprietà.  

