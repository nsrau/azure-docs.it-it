<properties 
	pageTitle="Spostare i dati da Salesforce con Data Factory | Microsoft Azure " 
	description="Informazioni su come spostare i dati da Salesforce usando Data factory di Azure." 
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
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Spostare i dati da Salesforce usando Data factory di Azure
Questo articolo illustra come usare l'attività di copia in Data Factory di Azure per copiare dati da Salesforce in qualsiasi archivio dati elencato nella colonna Sink della sezione relativa alle [origini e ai sink supportati](data-factory-data-movement-activities.md#supported-data-stores). Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Attualmente Data Factory supporta solo lo spostamento di dati da Salesforce verso [archivi dati sink supportati]((data-factory-data-movement-activities.md#supported-data-stores), ma non lo spostamento di dati da altri archivi dati a Salesforce.

## Prerequisiti
- È necessario usare una delle seguenti edizioni: Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition.
- È necessario abilitare l'autorizzazione API. Vedere [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Come abilitare l'accesso API in Salesforce dal set di autorizzazioni?).
- Per copiare dati da Salesforce in archivi dati locali, l'ambiente locale deve disporre del gateway di gestione dati versione 2.0 o successiva.

## Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da Salesforce in uno degli archivi dati sink supportati è la procedura guidata per la copia dei dati. Per la procedura dettagliata di creazione di una pipeline mediante la copia guidata dei dati, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).

L'esempio di seguito fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Esempio: Copiare i dati da Salesforce a BLOB di Azure
Nell'esempio i dati vengono copiati da Salesforce a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi. I dati possono essere copiati direttamente in uno qualsiasi dei sink indicati nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores), usando l'attività di copia in Azure Data Factory.

- Un servizio collegato di tipo [Salesforce](#salesforce-linked-service-properties).
- Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#salesforce-dataset-properties).
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#relationalsource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servizio collegato Salesforce**

Questo esempio usa il servizio collegato **Salesforce**. Vedere la sezione sul [Servizio collegato Salesforce](#salesforce-linked-service-properties) per le proprietà supportate da questo servizio collegato. Vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere token di sicurezza) per istruzioni su come reimpostare o ottenere il token di sicurezza.

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
			}
		}
	}

**Servizio collegato Archiviazione di Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input Salesforce**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

> [AZURE.IMPORTANT]  La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da RelationalSource, vedere le [proprietà del tipo RelationalSource](#relationalsource-type-properties).
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SaleforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
					},
					"sink": {
						"type": "BlobSink"
					}
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
					"concurrency": 1,
					"executionPriorityOrder": "OldestFirst",
					"retry": 0,
					"timeout": "01:00:00"
				}
			}
			]	
		}
	}

> [AZURE.IMPORTANT]  La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Proprietà del servizio collegato Salesforce

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Salesforce.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà del tipo deve essere impostata su: **Salesforce** | Sì | 
| username |Specificare il nome utente per l'account utente. | Sì |
| password | Specifica la password per l'account utente. | Sì |
| securityToken | Specificare il token di sicurezza per l'account utente. Vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere token di sicurezza) per istruzioni su come reimpostare o ottenere il token di sicurezza. Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.it-IT.api.meta/api/sforce_api_concepts_security.htm) (Sicurezza e API). | Sì | 

## Proprietà del set di dati Salesforce

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **RelationalTable** presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella in Salesforce. | No (se la **query** di **RelationalSource** è specificata) | 

> [AZURE.IMPORTANT]  La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## Proprietà del tipo RelationalSource
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **RelationalSource** (che comprende Salesforce), sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Query SQL-92 o query [Salesforce Object Query Language (SOQL) ](https://developer.salesforce.com/docs/atlas.it-IT.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Ad esempio: selezionare * da MyTable\_\_c. | No (se **tableName** di **set di dati** è specificato) |

> [AZURE.IMPORTANT]  La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.<br> Quando si specifica una query che include clausole sulla colonna relativa a data e ora, usare SOQL, ad esempio $$Text.Format('SELECT Id, Type, Name, BillingCity, BillingCountry FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Limiti delle richieste Salesforce
Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Per i dettagli, vedere la sezione **API Request Limits** (Limiti delle richieste API) nell'articolo [Salesforce API Request Limits](http://resources.docs.salesforce.com/200/20/it-IT/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti delle richieste API di Salesforce).
 
Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali. Se il numero totale di richieste supera il limite, l'account di Salesforce verrà bloccato per 24 ore. In entrambi gli scenari è anche possibile ricevere l'errore "REQUEST\_LIMIT\_EXCEEDED" ("LIMITE\_RICHIESTE\_SUPERATO").
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Salesforce
Tipo Salesforce | Tipo basato su .Net
--------------- | ---------------
Numero automatico | String
Casella di controllo | Boolean
Valuta | Double
Data | DateTime
Data/ora | DateTime
Email | String
ID | String
Relazione di ricerca | String
Elenco a discesa seleziona multipla | String
Number | Double
Percentuale | Double
Telefono | String
Elenco a discesa | String
Text | String
Area di testo | String
Area di testo (Long) | String
Area di testo (Rich) | String
Testo (Crittografato) | String
URL | String

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0720_2016-->