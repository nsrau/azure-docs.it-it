<properties 
	pageTitle="Data Factory - Log delle modifiche dell'API .NET | Microsoft Azure" 
	description="Vengono descritte le modifiche rilevanti, le aggiunte di funzionalità, le correzioni di bug e così via in una versione specifica dell'API .NET per la Data Factory di Azure." 
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
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Data factory di Azure - Log delle modifiche .NET SDK 
In questo articolo vengono fornite informazioni sulle modifiche apportate all'SDK di Data factory di Azure in una versione specifica. La versione più recente del pacchetto NuGet per Data factory di Azure è disponibile [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

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

<!---HONumber=Oct15_HO3-->