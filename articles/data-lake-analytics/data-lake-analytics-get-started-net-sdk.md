<properties 
   pageTitle="Introduzione ad Azure Data Lake Analytics con .NET SDK | Azure" 
   description="Informazioni su come usare .NET SDK per creare account di Data Lake Store, creare i processi di Data Lake Analytics e inviare i processi scritti in U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/25/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione ad Azure Data Lake Analytics con .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come usare Azure .NET SDK per creare gli account di Azure Data Lake Analytics, definire i processi di Data Lake Analytics in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi agli account di Data Lake Analtyic. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

In questa esercitazione verrà sviluppata un'applicazione console C# contenente uno script U-SQL che legge un file di valori separati da tabulazioni (TSV) e lo converte in un file di valori separati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede nella parte superiore di questa sezione.

**Processo di Data Lake Analytics di base:**

![Diagramma di flusso del processo di Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Creare un account di Analisi Data Lake.
2. Preparare i dati di origine. I processi di Data Lake Analytics possono leggere dati da account di Azure Data Lake Store o da account di archiviazione BLOB di Azure.   
3. Sviluppare uno script U-SQL.
4. Inviare un processo (script U-SQL) all'account di Analisi Data Lake. Il processo legge i dati di origine, elabora i dati come indicato nello script U-SQL e quindi salva l'output in un account di Data Lake Store o in un account di archiviazione BLOB.

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012 con Visual C++ installato**.
- **Microsoft Azure SDK per .NET versione 2.5 o successiva**. Installarlo usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Strumenti di Data Lake per Visual Studio](http://aka.ms/adltoolsvs)**. 
- **Un account di Data Lake Analytics**. Vedere [Creare un account di Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Data Lake Tools per Visual Studio non supporta la creazione di account Analisi Data Lake. Per crearne uno, sarà quindi necessario usare il portale di Azure, Azure PowerShell, Azure .NET SDK o l'interfaccia della riga di comando di Azure.

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
	
	Risulta più semplice usare i percorsi relativi dei file archiviati negli account predefiniti di Data Lake, ma è possibile usare anche percorsi assoluti. Ad esempio
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Non sono attualmente supportate autorizzazioni di accesso a contenitori pubblici o a contenitori BLOB di Azure con BLOB pubblici.
       
       
5. In Program.cs copiare e incollare il codice seguente:

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.WindowsAzure.Storage.Blob;

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

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and contains the U-SQL script.
                    
                    // Authenticate the user
                    // For more information about applications and instructions on how to get a client ID, see: 
                    //   https://azure.microsoft.com/it-IT/documentation/articles/resource-group-create-service-principal-portal/
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REDIRECT-URI>")); // TODO: Replace bracketed values.
                    
                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.
                    
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
                    string jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                    WaitForNewline("Job submitted.", "Waiting for job completion.");

                    // Wait for job completion
                    WaitForJob(jobId);
                    WaitForNewline("Job completed.", "Downloading job output.");

                    // Download job output
                    DownloadFile("/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
                    WaitForNewline("Job output downloaded.", "Deleting accounts.");

                    // Delete accounts
                    DeleteAccounts();
                    WaitForNewline("Accounts deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/it-IT/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/it-IT/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                    _adlaClient.SubscriptionId = subscriptionId;

                    _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
                    _adlaJobClient.SubscriptionId = subscriptionId;

                    _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
                    _adlaCatalogClient.SubscriptionId = subscriptionId;

                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create accounts
                public static void CreateAccounts()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

                    // Create ADLA account
                    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
                    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
                    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
                    _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
                }

                // Delete accounts
                public static void DeleteAccounts()
                {
                    _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);

                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLA accounts within the subscription
                public static List<DataLakeAnalyticsAccount> ListAdlAnalyticsAccounts()
                {
                    var response = _adlaClient.Account.List(_adlaAccountName);
                    var accounts = new List<DataLakeAnalyticsAccount>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);
                    
                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Submit a U-SQL job by providing script contents.
                // Returns the job ID
                public static string SubmitJobByScript(string script, string jobName)
                {
                    var jobId = Guid.NewGuid().ToString();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);
                    
                    return jobId;
                }

                // Submit a U-SQL job by providing a path to the script
                public static string SubmitJobByPath(string scriptPath, string jobName)
                {
                    var script = File.ReadAllText(scriptPath);

                    var jobId = Guid.NewGuid().ToString();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1000, degreeOfParallelism: 1);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);

                    return jobId;
                }

                public static JobResult WaitForJob(string jobId)
                {
                    var jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    while (jobInfo.State != JobState.Ended)
                    {
                        jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    }
                    return jobInfo.Result.Value;
                }

                // List jobs
                public static List<JobInformation> ListJobs()
                {
                    var response = _adlaJobClient.Job.List(_adlaAccountName);
                    var jobs = new List<JobInformation>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaJobClient.Job.ListNext(response.NextPageLink);
                        jobs.AddRange(response);
                    }

                    return jobs;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }
            }
        }

7. Premere **F5** per eseguire l'applicazione.

## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Per informazioni sulle attività di gestione, vedere [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0302_2016-->