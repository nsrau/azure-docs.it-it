<properties 
   pageTitle="Introduzione ad Azure Data Lake Analytics con .NET SDK | Azure" 
   description="Informazioni su come usare .NET SDK per creare account di Data Lake Store, creare i processi di Data Lake Analytics e inviare i processi scritti in U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>  
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione ad Azure Data Lake Analytics con .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come usare Azure .NET SDK per inviare processi scritti in [U-SQL](data-lake-analytics-u-sql-get-started.md) a Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

Questa esercitazione illustra come sviluppare un'applicazione console C# per inviare un processo U-SQL che legge un file con valori delimitati da tabulazioni (TSV) e lo converte in un file con valori delimitati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede disponibili nella parte superiore di questo articolo.

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012 con Visual C++ installato**.
- **Microsoft Azure SDK per .NET versione 2.5 o successiva**. Installarlo usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- Un **account di Azure Data Lake Analytics**. Vedere [Manage Data Lake Analytics using Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) (Gestire Azure Data Lake Analytics tramite Azure .NET SDK).

##Creare un'applicazione console

In questa esercitazione verranno elaborati alcuni log di ricerca. Il log di ricerca può essere archiviato in Data Lake Store o in un archivio BLOB di Azure.

Un log di ricerca di esempio è disponibile in un contenitore BLOB di Azure pubblico. Nell'applicazione scaricare il file nella workstation e quindi caricarlo nell'account Data Lake Store predefinito dell'account Data Lake Analytics.

**Per creare un'applicazione**

1. Aprire Visual Studio.
2. Creare un'applicazione console C#.
3. Aprire la console di Gestione pacchetti NuGet ed eseguire i seguenti comandi:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage

4. Aggiungere un nuovo file al progetto denominato **SampleUSQLScript.txt** e quindi incollare il seguente script U-SQL. I processi di Data Lake Analtyics vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Questo script U-SQL legge il file dei dati di origine mediante **Extractors.Tsv()** e quindi crea un file CSV mediante **Outputters.Csv()**.
    
    Nel programma C# è necessario preparare il file **/Samples/Data/SearchLog.tsv** e la cartella **/Output/**.
	
	Risulta più semplice usare i percorsi relativi dei file archiviati negli account predefiniti di Data Lake, è possibile usare anche percorsi assoluti. Ad esempio
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] Attualmente esiste un problema noto con il servizio di Azure Data Lake. Se l'applicazione di esempio viene interrotta o si verifica un errore, potrebbe essere necessario eliminare manualmente gli account Archivio Data Lake e Analisi Data Lake che lo script crea. Se non si ha familiarità con il portale, la guida [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md) descrive come iniziare.
       
5. In Program.cs copiare e incollare il codice seguente:

		using System;
		using System.IO;
		using System.Collections.Generic;
		using System.Threading;
		using Microsoft.Rest;
		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.WindowsAzure.Storage.Blob;

		namespace SdkSample
		{
		  class Program
		  {
			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
			private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;
			private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
			private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		
		    private static void Main(string[] args)
		    {
				string localFolderPath = @"c:\temp";

				// Connect to Azure
				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				SetupClients(creds, SUBSCRIPTIONID);

				// Transfer the source file from a public Azure Blob container to Data Lake Store.
				CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
				blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
				UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
				WaitForNewline("Source data file prepared.", "Submitting a job.");


				// Submit the job
				Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
				WaitForNewline("Job submitted.", "Waiting for job completion.");

				// Wait for job completion
				WaitForJob(jobId);
				WaitForNewline("Job completed.", "Downloading job output.");

				// Download job output
				DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		
		      	WaitForNewline("Job output downloaded. You can now exit.");
		    }
		
			public static ServiceClientCredentials AuthenticateAzure(
				string domainName,
				string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
			{
				_adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
				_adlaClient.SubscriptionId = subscriptionId;

				_adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

				_adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
			}

			public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
			{
				var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
				var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
				var uploader = new DataLakeStoreUploader(parameters, frontend);
				uploader.Execute();
			}

			public static void DownloadFile(string srcPath, string destPath)
			{
				var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
				var fileStream = new FileStream(destPath, FileMode.Create);

				stream.CopyTo(fileStream);
				fileStream.Close();
				stream.Close();
			}

			// Helper function to show status and wait for user input
			public static void WaitForNewline(string reason, string nextAction = "")
			{
				Console.WriteLine(reason + "\r\nPress ENTER to continue...");

				Console.ReadLine();

				if (!String.IsNullOrWhiteSpace(nextAction))
					Console.WriteLine(nextAction);
			}


			// List all Data Lake Analytics accounts within the subscription
			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
				for (int i = 0; i < accounts.Count; i++)
				{
					Console.WriteLine(accounts[i].Name);
				}

				return accounts;
			}
			public static Guid SubmitJobByPath(string scriptPath, string jobName)
			{
				var script = File.ReadAllText(scriptPath);

				var jobId = Guid.NewGuid();
				var properties = new USqlJobProperties(script);
				var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
				var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

				return jobId;
			}

			public static JobResult WaitForJob(Guid jobId)
			{
				var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				while (jobInfo.State != JobState.Ended)
				{
					jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				}
				return jobInfo.Result.Value;
			}
		  }
		}

6. Premere **F5** per eseguire l'applicazione. L'output è simile al seguente:

	![Output di .NET SDK per il processo U-SQL di Azure Data Lake Analytics](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)  

7. Controllare il file di output. Il nome file e percorso predefinito è c:\\Temp\\SearchLog-from-Data-Lake.csv.

## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Analisi Data Lake, vedere [Panoramica di Analisi Data Lake di Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0928_2016-->