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
   ms.date="06/22/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione ad Azure Data Lake Analytics con .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come usare Azure .NET SDK per creare gli account di Analisi Azure Data Lake, definire i processi di Analisi Data Lake in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi agli account di Analisi Data Lake. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

In questa esercitazione verrà sviluppata un'applicazione console C# contenente uno script U-SQL che legge un file di valori separati da tabulazioni (TSV) e lo converte in un file di valori separati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede disponibili nella parte superiore di questa sezione.

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012 con Visual C++ installato**.
- **Microsoft Azure SDK per .NET versione 2.5 o successiva**. Installarlo usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools per Visual Studio](http://aka.ms/adltoolsvs)**.
- Creare un'applicazione Azure Active Directory (AAD) e recuperarne l'**ID client**, l'**ID tenant** e la **chiave**. Per altre informazioni sulle applicazioni AAD e le istruzioni su come ottenere un ID client, vedere [Usare il portale per creare un'applicazione di Active Directory che acceda alle risorse](../resource-group-create-service-principal-portal.md). Dopo aver creato l'applicazione e generato la chiave, anche l'URI di risposta e la chiave saranno disponibili dal portale.


##Creare un'applicazione console

In questa esercitazione verranno elaborati alcuni log di ricerca. Il log di ricerca può essere archiviato in Data Lake Store o in un archivio BLOB di Azure.

Un log di ricerca di esempio è stato copiato in un contenitore BLOB di Azure pubblico. Nell'applicazione scaricare il file nella workstation in uso e quindi caricare il file nell'account di Data Lake Store predefinito.

**Per creare un'applicazione**

1. Aprire Visual Studio.
2. Creare un'applicazione console C#.
3. Aprire la console di Gestione pacchetti NuGet ed eseguire i seguenti comandi:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
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

	>[AZURE.NOTE] Attualmente esiste un problema noto con il servizio di Azure Data Lake. Se l'applicazione di esempio viene interrotta o si verifica un errore, potrebbe essere necessario eliminare manualmente gli account Archivio Data Lake e Analisi Data Lake che lo script crea. Se non si ha familiarità con il portale, la guida [Gestire Analisi Azure Data Lake con il portale di Azure](data-lake-analytics-manage-use-portal.md) descrive come iniziare.
       
5. In Program.cs copiare e incollare il codice seguente:

		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Rest;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System;
		using System.Collections.Generic;
		using System.IO;
		
		namespace SdkSample
		{
		  class Program
		  {
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		
		    private static string _adlaAccountName;
		    private static string _adlsAccountName;
		    private static string _resourceGroupName;
		    private static string _location;
		    private static string _tenantId;
		    private static string _subId;
		    private static string _clientId;
		    private static string _clientKey;
		
		    private static void Main(string[] args)
		    {
		      _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
		      _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
		      _resourceGroupName = "<RESOURCE-GROUP>"; // TODO: Replace this value. This resource group should already exist.
		      _location = "East US 2";
		      _tenantId = "<TENANT-ID>";
		      _subId = "<SUBSCRIPTION-ID>";
		      _clientId = "<CLIENT-ID>";
		      _clientKey = "<CLIENT-KEY>";
		
		      string localFolderPath = @"c:\temp"; // TODO: Make sure this exists and contains SampleUSQLScript.txt.
		      var tokenCreds = Authenticate(_tenantId, _clientId, _clientKey);
		
		      SetupClients(tokenCreds, _subId); 
		
		      // Run sample scenarios
		      WaitForNewline("Authenticated.", "Creating NEW accounts.");
		      CreateAccounts();
		      WaitForNewline("Accounts created.", "Preparing the source data file.");
		
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
		      WaitForNewline("Job output downloaded.", "Deleting accounts.");
		
		      // Delete accounts
		      _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);
		      _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
		
		      WaitForNewline("Accounts deleted. You can now exit.");
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(string reason, string nextAction = "")
		    {
		      Console.WriteLine(reason + "\r\nPress ENTER to continue...");
		
		      Console.ReadLine();
		
		      if (!String.IsNullOrWhiteSpace(nextAction))
		        Console.WriteLine(nextAction);
		    }
		
		    public static TokenCredentials Authenticate(string tenantId, string clientId, string clientKey)
		    {
		      var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + _tenantId);
		      var creds = new ClientCredential(_clientId, _clientKey);
		      var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/", creds).Result;
		
		      return new TokenCredentials(tokenAuthResult.AccessToken);
		    }
		
		    public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
		    {
		      _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
		      _adlaClient.SubscriptionId = subscriptionId;
		
		      _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
		
		      _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
		
		      _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
		      _adlsClient.SubscriptionId = subscriptionId;
		
		      _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
		    }
		
		    public static void CreateAccounts()
		    {
		      //ADLS account first, ADLA requires an ADLS account
		      var adlsParameters = new DataLakeStoreAccount(location: _location);
		      _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		      var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
		      var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
		      var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
		      _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
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
		  }
		}

6. Premere **F5** per eseguire l'applicazione.

## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Per informazioni sulle attività di gestione, vedere [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0921_2016-->