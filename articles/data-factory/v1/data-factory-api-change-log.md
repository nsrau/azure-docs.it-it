---
title: Data Factory-log delle modifiche dell'API .NET
description: Descrive le modifiche di rilievo, le aggiunte delle funzionalità, le correzioni di bug e così via, in una versione specifica dell'API .NET per la Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 70df35409d1c84efb996bb40f4e39bde6ad7d5a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496501"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory: log delle modifiche dell'API .NET
> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. 

In questo articolo vengono fornite informazioni sulle modifiche apportate all'SDK di Azure Data Factory in una versione specifica. La versione più recente del pacchetto NuGet per Azure Data Factory è disponibile [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versione 4.11.0
Aggiunte di funzionalità

* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Versione 4.10.0
* A TextFormat sono state aggiunte le proprietà facoltative seguenti:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Aggiunta della proprietà [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) ad AzureMLBatchExecutionActivity
  * Possibilità di passare più input di servizi Web a un esperimento di Azure Machine Learning

## <a name="version-491"></a>Versione 4.9.1
### <a name="bug-fix"></a>Correzione di bug
* Deprecazione dell'autenticazione basata su WebApi per [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>Versione 4.9.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta delle proprietà [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) e [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) a CopyActivity. Per informazioni dettagliate sulla funzionalità, vedere [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Correzione di bug
* Introduzione di un overload del metodo [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) che usa un'istanza [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters).
* Contrassegno di [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) e [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) come facoltativi in CopySink.

## <a name="version-480"></a>Versione 4.8.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Le proprietà facoltative seguenti sono state aggiunte al tipo di attività copia per abilitare l'ottimizzazione delle prestazioni di copia:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Versione 4.7.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta del nuovo tipo StorageFormat [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) per la copia dei file in formato ORC (Optimized Row Columnar).
* Aggiunta delle proprietà [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) e PolyBaseSettings a SqlDWSink.
  * Consente l'uso di polibase per copiare i dati in Azure sinapsi Analytics.

## <a name="version-461"></a>Versione 4.6.1
### <a name="bug-fixes"></a>Correzioni di bug
* Corregge la richiesta HTTP relativa all'elenco delle finestre attività.
  * Rimuove il nome del gruppo di risorse e quello della data factory dal payload della richiesta.

## <a name="version-460"></a>Versione 4.6.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* A [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)sono state aggiunte le proprietà seguenti:
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Set di dati](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* A [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)sono state aggiunte le proprietà seguenti:
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Aggiunta del nuovo tipo [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat)[JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) per definire i set di dati con dati in formato JSON.

## <a name="version-450"></a>Versione 4.5.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Aggiunta di [operazioni di tipo elenco per la finestra attività](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Aggiunta di metodi per recuperare le finestre attività con filtri basati sui tipi di entità (data factory, set di dati, pipeline e attività).
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Versione 4.4.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Il seguente tipo di servizio collegato è stato aggiunto come origini dati e sink per le attività di copia:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Per informazioni concettuali ed esempi, vedere [Servizio collegato di firma di accesso condiviso Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Versione 4.3.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* I seguenti tipi di servizi collegati sono stati aggiunti come origini dati per le attività di copia:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Per informazioni concettuali ed esempi, vedere [Spostare dati da HDFS con Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Per informazioni concettuali ed esempi, vedere [Spostare dati da archivi dati ODBC con Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>Versione 4.2.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* È stato aggiunto il nuovo tipo di attività seguente: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Per informazioni dettagliate sull'attività, vedere [Aggiornamento di modelli di Azure ML tramite l'attività Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md).
* Una nuova proprietà facoltativa [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) è stata aggiunta alla [classe AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* Le proprietà [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) e [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) sono state aggiunte alla classe [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient).
* Consentire la configurazione dei timeout per le chiamate client al servizio Data factory.

## <a name="version-410"></a>Versione 4.1.0
### <a name="feature-additions"></a>Aggiunte di funzionalità
* Sono stati aggiunti i seguenti tipi di servizi collegati:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Sono stati aggiunti i seguenti tipi di attività:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Sono stati aggiunti i seguenti tipi di set di dati:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Sono stati aggiunti i seguenti tipi di origine e sink per Attività di copia:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Versione 4.0.1
### <a name="breaking-changes"></a>Modifiche di rilievo
Le classi seguenti sono state rinominate. I nuovi nomi sono i nomi originali delle classi prima della versione 4.0.0.

| Nome nella versione 4.0.0 | Nome nella versione 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Versione 4.0.0
### <a name="breaking-changes"></a>Modifiche di rilievo
* Le classi/interfacce seguenti sono state rinominate.

| Nome precedente | Nuovo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabella |[Set di dati](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* I metodi **List** ora restituiscono risultati di paging. Se la risposta contiene una proprietà **NextLink** non vuota, l'applicazione client deve continuare il recupero della pagina successiva finché non vengono restituite tutte le pagine.  Esempio:

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
* La classe [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) supporta due nuove proprietà, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, per supportare la copia idempotente in Azure sinapsi Analytics di Azure. Per informazioni dettagliate su queste proprietà, vedere l'articolo [analisi delle sinapsi di Azure](data-factory-azure-sql-data-warehouse-connector.md) .
* È ora supportata l'esecuzione di stored procedure per il database SQL di Azure e le origini di Azure sinapsi Analytics come parte dell'attività di copia. Le classi [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) e [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) hanno le proprietà **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Per informazioni dettagliate su queste proprietà, vedere gli articoli [database SQL di Azure](data-factory-azure-sql-connector.md#sqlsource) e [analisi di sinapsi di Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) in Azure.com.