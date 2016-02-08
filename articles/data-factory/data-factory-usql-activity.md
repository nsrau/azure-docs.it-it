<properties 
	pageTitle="Eseguire script U-SQL in Azure Data Lake Analytics da Data factory di Azure" 
	description="Informazioni su come elaborare i dati eseguendo gli script U-SQL nel servizio di calcolo di Azure Data Lake Analytics." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Eseguire script U-SQL in Azure Data Lake Analytics da Data factory di Azure 
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'**attività U-SQL di Data Lake Analytics** che esegue uno script **U-SQL** in un servizio di calcolo collegato di **Azure Data Lake Analytics**.

> [AZURE.NOTE] 
È necessario creare un account di Azure Data Lake Analytics prima di creare una pipeline con un'attività U-SQL di Data Lake Analytics. Per altre informazioni su Azure Data Lake Analytics, vedere [Introduzione ad Azure Data Lake con .NET SDK](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Fare riferimento all'esercitazione [Creare la prima pipeline](data-factory-build-your-first-pipeline.md) per la procedura dettagliata per la creazione di una data factory, dei servizi collegati, dei set di dati e di una pipeline. Usare i frammenti JSON con l'editor di Data factory o Visual Studio o Azure PowerShell per creare le entità di Data factory.

## Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data factory di Azure prima di usare l'attività U-SQL di Data Lake Analytics in una pipeline.

Nell'esempio seguente viene fornita la definizione JSON per un servizio collegato di Azure Data Lake Analytics.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
Tipo | La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. | Sì
accountName | Nome dell'account di Azure Data Lake Analytics. | Sì
dataLakeAnalyticsUri | URI di Azure Data Lake Analytics. | No 
autorizzazione | Il codice di autorizzazione viene recuperato automaticamente dopo aver fatto clic sul pulsante **Autorizza** nell'editor di Data factory e aver completato l'accesso OAuth. | Sì 
subscriptionId | ID sottoscrizione di Azure | No (se non specificata, viene usata la sottoscrizione della Data factory). 
resourceGroupName | Nome del gruppo di risorse di Azure | No (se non specificata, viene usato il gruppo di risorse della Data factory).
sessionId | ID di sessione dalla sessione di autorizzazione OAuth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questo valore viene generato automaticamente nell'editor di Data factory. | Sì

Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato il messaggio di errore seguente: Errore dell'operazione relativa alle credenziali: invalid\_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z.

 
| Tipo di utente | Scade dopo |
| :-------- | :----------- | 
| Utente non AAD (@hotmail.com, @live.com e così via) | 12 ore |
| L'utente AAD e l'origine basata su OAuth si trovano in un [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) diverso rispetto al tenant di Data factory. | 12 ore |
| L'utente AAD e l'origine basata su OAuth si trovano sullo stesso tenant rispetto al tenant di Data factory. | 14 giorni |

Per evitare o risolvere questo problema, è necessario ripetere l'autorizzazione con il pulsante **Autorizza** alla **scadenza del token** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice riportato nella sezione seguente.

  
### Per generare valori sessionId e authorization a livello di codice 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Per informazioni dettagliate sulle classi di Data factory usate nel codice, vedere gli argomenti [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). È necessario aggiungere un riferimento a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe WindowsFormsWebAuthenticationDialog.
 
 
## Attività U-SQL di Data Lake Analytics 

Il frammento JSON seguente definisce una pipeline con un'attività U-SQL di Data Lake Analytics. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Data Lake Analytics creato in precedenza.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


Nella tabella seguente vengono descritti i nomi e le descrizioni delle proprietà specifiche per questa attività.

Proprietà | Descrizione | Obbligatorio
:-------- | :----------- | :--------
type | La proprietà type deve essere impostata su **DataLakeAnalyticsU-SQL**. | Sì
scriptPath | Percorso della cartella contenente lo script U-SQL. | No (se si usa uno script)
scriptLinkedService | Servizi collegati che collegano la risorsa di archiviazione contenente lo script alla Data factory | No (se si usa uno script)
script | Specificare lo script inline anziché specificare le proprietà scriptPath e scriptLinkedService. Ad esempio: "script" : "CREATE DATABASE test". | No (se si usano le proprietà scriptPath e scriptLinkedService)
degreeOfParallelism | Il numero massimo di nodi che verranno usati contemporaneamente per eseguire il processo. | No
priority | Determina quali processi rispetto a tutti gli altri disponibili nella coda devono essere selezionati per essere eseguiti per primi. Più è basso il numero, maggiore sarà la priorità. | No 
parameters | Parametri per lo script U-SQL | No 

Per la definizione dello script, vedere la sezione [Definizione dello script SearchLogProcessing.txt](#script-definition).

### Set di dati di input e output di esempio

#### Set di dati di input
In questo esempio i dati di input si trovano in un archivio di Azure Data Lake (file SearchLog.tsv nella cartella datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Set di dati di output
In questo esempio i dati di output generati dallo script U-SQL sono memorizzati in un archivio di Azure Data Lake (cartella datalake/output).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Servizio collegato dell'archivio di Azure Data Lake di esempio
Ecco la definizione del servizio collegato dell'archivio di Azure Data Lake di esempio usato dai set di dati di input/output precedenti.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Vedere [Spostare dati da e verso l'archivio di Azure Data Lake](data-factory-azure-datalake-connector.md) per una descrizione delle proprietà JSON nel servizio collegato dell'archivio di Azure Data Lake e negli snippet JSON dei set di dati precedenti.

### Definizione dello script

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

I valori dei parametri **@in** e **@out** nello script U-SQL riportato sopra vengono passati in modo dinamico da ADF usando la sezione 'parameters'. Vedere la sezione 'parameters' riportata sopra nella definizione della pipeline.

È possibile specificare anche altre proprietà come degreeOfParallelism, priorità e così via nella definizione della pipeline per i processi in esecuzione sul servizio di Analisi Azure Data Lake.

<!---HONumber=AcomDC_0128_2016-->