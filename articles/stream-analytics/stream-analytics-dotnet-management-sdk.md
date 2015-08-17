<properties 
	pageTitle="Informazioni su come usare .NET SDK per la gestione di Analisi di flusso | Microsoft Azure" 
	description="Introduzione a .NET SDK per la gestione di Analisi di flusso. Informazioni su come configurare ed eseguire i processi di analisi: creare un progetto, gli input, gli output e le trasformazioni." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/04/2015" 
	ms.author="jeffstok"/>


# Impostare ed eseguire processi di analisi tramite .NET SDK per la gestione di analisi di flusso di Azure

Informazioni su come impostare ed eseguire processi di analisi tramite .NET SDK per la gestione di analisi di flusso Impostare un progetto, creare origini di input e output, trasformazioni e avviare e arrestare i processi. Per i processi di analisi, è possibile trasmettere i dati di flusso dall'archiviazione BLOB o da un hub eventi.

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Analisi di flusso consente ai clienti di configurare processi di flusso per analizzare i flussi di dati e di condurre operazioni di analisi pressoché in tempo reale.

Per informazioni di riferimento sulle API REST per .NET, vedere [. NET SDK di gestione di analisi di flusso](https://msdn.microsoft.com/library/azure/dn889315.aspx)


## Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

- Installare Visual Studio 2012 o 2013.
- Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/). 
- Creare un gruppo di risorse di Azure nella sottoscrizione. Di seguito è riportato un esempio di script di Azure PowerShell: Per ulteriori informazioni su Azure PowerShell , vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md).  


		# Configure the Azure PowerShell session to access Azure Resource Manager
		Switch-AzureMode AzureResourceManager

		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Create an Azure resource group	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	Configurare un'origine di input e una destinazione di output da usare. Vedere [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-get-started.md) per configurare un input e/o un output di esempio da usare in base alle informazioni fornite in questo articolo.


## Configurare un progetto

Per creare un processo di analisi, per prima cosa occorre impostare il progetto.

1. Creare un'applicazione console .NET di Visual Studio C#.
2. Nella Console di Gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è .NET SDK di gestione di Analisi di flusso di Azure. Il secondo è il client Azure Active Directory che verrà usato per l'autenticazione.

		Install-Package Microsoft.Azure.Management.StreamAnalytics
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Aggiungere la sezione **appSettings** seguente al file App.config:

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
		  <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
		</appSettings>


	Sostituire i valori per **SubscriptionId** e **ActiveDirectoryTenantId** con gli ID della sottoscrizione Azure e del tenant. È possibile ottenere questi valori eseguendo il cmdlet Azure PowerShell seguente:

		Get-AzureAccount

5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Aggiungere un metodo helper di autenticazione:

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior: PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });
		
		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();
		
		    if (result != null)
		    {
		        return result.AccessToken;
		    }
		
		    throw new InvalidOperationException("Failed to acquire token");
		}  


## Creare un client di gestione di Analisi di flusso.

Un oggetto **StreamAnalyticsManagementClient** consente di gestire il processo e i componenti di processo, ad esempio l'input, l'output e la trasformazione.

Aggiungere il codice seguente all'inizio del metodo **Main**.

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Il valore della variabile **resourceGroupName** deve essere lo stesso del nome del gruppo di risorse creato o selezionato nei passaggi preliminari.

Nelle sezioni rimanenti di questo articolo si presuppone che il codice sia all'inizio del metodo **Main**.

## Creare un processo di Analisi di flusso.

Il codice seguente crea un processo di Analisi di flusso nel gruppo di risorse che è stato definito. Verranno aggiunti un input, un output e la trasformazione al processo in un secondo momento.

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Creare un'origine di input di Analisi di flusso.

Il codice seguente crea un'origine di input di Analisi di flusso con il tipo di origine di input BLOB e la serializzazione CSV. Per creare un'origine di input di hub eventi, usare **EventHubStreamInputDataSource** invece di **BlobStreamInputDataSource**. Analogamente, è possibile personalizzare il tipo di serializzazione dell'origine di input.

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Le origini di input, dall'archiviazione BLOB o un hub eventi, sono legate a un processo specifico. Per usare la stessa origine di input per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.


## Testare un'origine di input di Analisi di flusso

Il metodo **TestConnection** verifica se il processo di Analisi di flusso è in grado di connettersi all'origine di input, nonché altri aspetti specifici del tipo di origine di input. Ad esempio, nell'origine di input BLOB creata in precedenza, il metodo verifica che il nome dell'account di archiviazione e la coppia di chiavi possano essere usati per connettersi all'account di archiviazione, nonché verificare che il contenitore specificato esista.

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Creare una destinazione di output di Analisi di flusso

La creazione di una destinazione di output è molto simile alla creazione di un'origine di input di Analisi di flusso. Analogamente alle origini di input, le destinazioni di output sono legate a un processo specifico. Per usare la stessa destinazione di output per processi diversi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

Il codice seguente crea una destinazione di output (database SQL di Azure). È possibile personalizzare il tipo di dati della destinazione di output e/o il tipo di serializzazione.

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Testare una destinazione di output di Analisi di flusso

Una destinazione di output di Analisi di flusso dispone inoltre del metodo **TestConnection** per il test delle connessioni.

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Creare una trasformazione di Analisi di flusso

Il codice seguente crea una trasformazione di Analisi di flusso con la query "select * from Input" e specifica l'assegnazione di un'unità di streaming per il processo di Analisi di flusso. Per altre informazioni sulla regolazione di unità di streaming, vedere [Scalabilità dei processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md).


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Analogamente all'input e all'output, una trasformazione è inoltre collegata a un processo di Analisi di flusso specifico in cui è stato creata.

## Avvio di un processo di Analisi di flusso
Dopo aver creato un processo di Analisi di flusso nonché gli input, gli output e la trasformazione, è possibile avviare il processo chiamando il metodo **Start**.

Il codice di esempio seguente avvia un processo di Analisi di flusso con un'ora di inizio dell'output personalizzato impostata sulle 12:12:12 UTC del 12 dicembre 2012:

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Arresto di un processo di Analisi di flusso
È possibile arrestare un processo di Analisi di flusso in esecuzione chiamando il metodo **Stop**.

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Eliminazione di un processo di Analisi di flusso
Il metodo **Delete** consente di eliminare il processo, nonché le risorse secondarie sottostanti, inclusi gli input, gli output e la trasformazione del processo.

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Passaggi successivi

Sono state fornite le nozioni di base dell'utilizzo di .NET SDK per creare ed eseguire i processi di analisi. Per altre informazioni, vedere gli argomenti seguenti:

- [Introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md)
- [.NET SDK per la gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=August15_HO6-->