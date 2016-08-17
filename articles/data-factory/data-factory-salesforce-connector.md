<properties
	pageTitle="Spostare i dati da Salesforce usando Data Factory | Microsoft Azure"
	description="Informazioni su come spostare dati da Salesforce usando Azure Data Factory."
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

# Spostare dati da Salesforce usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Salesforce in qualsiasi archivio dati elencato nella colonna Sink della tabella relativa a [origini e sink supportati](data-factory-data-movement-activities.md#supported-data-stores). Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

Attualmente Azure Data Factory supporta solo lo spostamento di dati da Salesforce verso [archivi dati sink supportati]((data-factory-data-movement-activities.md#supported-data-stores), ma non supporta lo spostamento di dati da altri archivi dati a Salesforce.

## Prerequisiti
- È necessario usare una delle seguenti edizioni di Salesforce: Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition.
- È necessario abilitare l'autorizzazione API. Vedere [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Come abilitare l'accesso API in Salesforce dal set di autorizzazioni?)
- Per copiare dati da Salesforce in archivi dati locali, è necessario che nell'ambiente locale sia installato almeno Gateway di gestione dati 2.0.

## Copia dati guidata
Il modo più semplice per creare una pipeline che copia i dati da Salesforce in uno degli archivi dati sink supportati è la procedura guidata Copia dati. Per una rapida procedura dettagliata di creazione di una pipeline mediante la copia guidata dei dati, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).

L'esempio seguente fornisce le definizioni JSON si esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Esempio: Copiare i dati da Salesforce in un BLOB di Azure
Nell'esempio i dati vengono copiati da Salesforce a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi. I dati possono essere copiati direttamente in uno qualsiasi dei sink indicati nell'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md#supported-data-stores) tramite l'attività di copia in Azure Data Factory.

Ecco gli elementi di Data Factory che è necessario creare per implementare lo scenario. Le sezioni che seguono l'elenco forniscono informazioni dettagliate su questi passaggi.

- Un servizio collegato di tipo [Salesforce](#salesforce-linked-service-properties).
- Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#salesforce-dataset-properties).
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#relationalsource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servizio collegato Salesforce**

Questo esempio usa il servizio collegato **Salesforce**. Vedere la sezione [Servizio collegato Salesforce](#salesforce-linked-service-properties) per le proprietà supportate da questo servizio collegato. Vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere token di sicurezza) per istruzioni su come ottenere o reimpostare il token di sicurezza.

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

> [AZURE.IMPORTANT] La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

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

La pipeline contiene un'attività di copia configurata per l'uso dei set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **source** è impostato su **RelationalSource** e il tipo **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da RelationalSource, vedere [Proprietà del tipo RelationalSource](#relationalsource-type-properties).

	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SalesforceToAzureBlob",
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

> [AZURE.IMPORTANT] La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Proprietà del servizio collegato Salesforce

La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato Salesforce.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **Salesforce**. | Sì |
| username |Specificare un nome utente per l'account utente. | Sì |
| password | Specificare la password per l'account utente. | Sì |
| securityToken | Specificare un token di sicurezza per l'account utente. Vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere token di sicurezza) per istruzioni su come ottenere o reimpostare un token di sicurezza. Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.it-IT.api.meta/api/sforce_api_concepts_security.htm) (Sicurezza e API). | Sì |

## Proprietà del set di dati Salesforce

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per un set di dati di tipo **RelationalTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella in Salesforce. | No (se è specificata una **query** di **RelationalSource**) |

> [AZURE.IMPORTANT]  La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## Proprietà del tipo RelationalSource
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e diversi criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e, in caso di attività di copia, variano in base ai tipi di origini e sink.

In caso di attività di copia con origine di tipo **RelationalSource** (che include Salesforce), sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Query SQL-92 o query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.it-IT.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Ad esempio: selezionare * da MyTable\_\_c. | No (se è specificato **tableName** di **set di dati**) |

> [AZURE.IMPORTANT] La parte "\_\_c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.<br> Quando si specifica una query che include la clausola **where** nella colonna DateTime, usare SOQL. Ad esempio: $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd) o query SQL ad esempio: $$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts'{0:yyyy-MM-dd HH:mm:ss}'}} AND LastModifiedDate < {{ts'{1:yyyy-MM-dd HH:mm:ss}'}}', WindowStart, WindowEnd).

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Recupero di dati dal report di Salesforce
È possibile recuperare i dati dai report di Salesforce specificando una query come {call "<nome report>"}, ovvero "query": "{call "TestReport"}".

## Limiti delle richieste Salesforce
Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Per i dettagli, vedere la sezione API Request Limits (Limiti delle richieste API) nell'articolo [Salesforce API Request Limits](http://resources.docs.salesforce.com/200/20/it-IT/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti delle richieste API di Salesforce).

Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali. Se il numero totale di richieste supera il limite, l'account di Salesforce verrà bloccato per 24 ore. In entrambi gli scenari è anche possibile che venga visualizzato l'errore "REQUEST\_LIMIT\_EXCEEDED" ("LIMITE\_RICHIESTE\_SUPERATO").


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Salesforce
Tipo Salesforce | Tipo basato su .NET
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

## Prestazioni e ottimizzazione  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0803_2016-->