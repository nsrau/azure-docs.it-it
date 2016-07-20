<properties 
	pageTitle="Data Factory: log delle modifiche dell'API .NET | Microsoft Azure" 
	description="Vengono descritte le modifiche rilevanti, le aggiunte di funzionalità, le correzioni di bug e così via in una versione specifica dell'API .NET per Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/05/2016" 
	ms.author="spelluru"/>

# Azure Data Factory: log delle modifiche dell'API .NET 
In questo articolo vengono fornite informazioni sulle modifiche apportate all'SDK di Azure Data Factory in una versione specifica. La versione più recente del pacchetto NuGet per Azure Data Factory è disponibile [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

## Versione 4.7.0
Data di rilascio: 20/05/2016

### Aggiunte di funzionalità
* Aggiunti i nuovi tipi StorageFormat e [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) per la copia dei file in formato ORC (Optimized Row Columnar).
* Aggiunte le proprietà [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
    * Abilita l'uso di PolyBase per la copia di dati in SQL Data Warehouse.

## Versione 4.6.1
Data di rilascio: 26/04/2016

### Correzioni di bug
* Corregge la richiesta HTTP relativa all'elenco delle finestre attività.
    * Rimuove il nome del gruppo di risorse e quello della data factory dal payload della richiesta.

## Versione 4.6.0
Data di rilascio: 14/04/2016

### Aggiunte di funzionalità

- Le proprietà seguenti sono state aggiunte a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
	- [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
	- [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
	- [Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Le proprietà seguenti sono state aggiunte a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
	- [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Aggiunti i nuovi tipi [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) e [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) per definire i set di dati con dati in formato JSON.

## Versione 4.5.0
Data di rilascio: 24/02/2016

### Aggiunte di funzionalità
* Aggiunte [operazioni elenco per la finestra attività](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Aggiunti metodi di recupero delle finestre attività con filtri basati sui tipi di entità, ad esempio data factory, set di dati, pipeline e attività.
* Sono stati aggiunti i seguenti tipi di servizi collegati:
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Sono stati aggiunti i seguenti tipi di set di dati:
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Sono stati aggiunti i seguenti tipi di origine dati per le attività di copia:
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## Versione 4.4.0
Data di rilascio: 28/01/2016

### Aggiunte di funzionalità

- Il seguente tipo di servizio collegato è stato aggiunto come origini dati e sink per le attività di copia:
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Servizio collegato di firma di accesso condiviso di Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service).

## Versione 4.3.0
Data di rilascio: 25/11/2015

### Aggiunte di funzionalità

- I seguenti tipi di servizi collegati sono stati aggiunti come origini dati per le attività di copia:
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Spostare dati da HDFS con Data Factory](data-factory-hdfs-connector.md).
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Per informazioni concettuali ed esempi, vedere [Spostare dati da archivi dati ODBC con Azure Data Factory](data-factory-odbc-connector.md).

## Versione 4.2.0
Data di rilascio: 10/11/2015

### Aggiunte di funzionalità

- È stato aggiunto il nuovo tipo di attività seguente: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Per informazioni dettagliate sull'attività, vedere [Aggiornamento di modelli di Azure ML tramite l'attività Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Una nuova proprietà facoltativa [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) è stata aggiunta alla [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
- Le proprietà [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) sono state aggiunte alla classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
- Consentire la configurazione dei timeout per le chiamate client al servizio Data factory.


## Versione 4.1.0
Data di rilascio: 28/10/2015

### Aggiunte di funzionalità
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


## Versione 4.0.1
Data di rilascio: 13/10/2015

### Modifiche di rilievo
Le classi seguenti sono state rinominate. I nuovi nomi sono i nomi originali delle classi prima della versione 4.0.0.
 
Nome nella versione 4.0.0 | Nome nella versione 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Versione 4.0.0
Data di rilascio: 02/10/2015

### Modifiche di rilievo



- Le classi/interfacce seguenti sono state rinominate.

| Nome precedente | Nuovo nome |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabella | [Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- La **versione API** per questa release è: **2015-10-01**.

- I metodi **List** ora restituiscono risultati di paging. Se la risposta contiene una proprietà **NextLink** non vuota, l'applicazione client deve continuare il recupero della pagina successiva finché non vengono restituite tutte le pagine. Di seguito è fornito un esempio:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- L'API della pipeline **List** restituisce solo il riepilogo di una pipeline anziché i dettagli completi. Ad esempio, le attività in un riepilogo delle pipeline può contenere solo il nome e il tipo.

### Aggiunte di funzionalità
- La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) supporta due nuove proprietà, ovvero **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, a supporto della copia idempotente in SQL Data Warehouse di Azure. Vedere l'articolo [SQL Data Warehouse di Azure](data-factory-azure-sql-data-warehouse-connector.md) e in particolare le sezioni [meccanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e [meccanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) per informazioni dettagliate su queste proprietà.

- Ora è supportata l'esecuzione di stored procedure su origini di database SQL di Azure e SQL Data Warehouse di Azure come parte dell'attività di copia. Per garantire tale supporto, le classi [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) dispongono delle seguenti proprietà: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Vedere gli articoli [Database SQL di Azure](data-factory-azure-sql-connector.md#sqlsource) e [SQL Data Warehouse di Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) su Azure.com per informazioni dettagliate su queste proprietà.

<!---HONumber=AcomDC_0706_2016-->