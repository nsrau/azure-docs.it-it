---
title: Gestire Azure Data Lake Analytics usando .NET SDK di Azure | Microsoft Docs
description: 'Informazioni su come gestire processi, origini dati e utenti di Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cf8873a3cc5067717edf586da209b05cef3092ff
ms.openlocfilehash: fc921785e5e4aae84982a348814c1760ddd6bb8c
ms.lasthandoff: 02/23/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestire Azure Data Lake Analytics tramite .NET SDK di Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite .NET SDK di Azure. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, sono necessari i prerequisiti seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure
Se non ne è già stato creato uno, è necessario creare un gruppo di risorse di Azure per creare i componenti di Data Lake Analytics. Il codice seguente mostra come creare il gruppo di risorse:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Per altre informazioni, vedere [Gruppi di risorse di Azure e Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics).


## <a name="connect-to-azure-data-lake"></a>Connettersi ad Azure Data Lake
Sono necessari i pacchetti NuGet seguenti:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


Il metodo Main dell'esempio di codice seguente mostra come connettersi ad Azure e inizializzare gli oggetti di gestione client di Data Lake per un account di analisi e un account di archiviazione.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Oggetti di gestione client di Data Lake
Azure Data Lake SDK include set di oggetti di gestione client dai quali è possibile eseguire la maggior parte della programmazione; si trovano nei due spazi dei nomi seguenti:
* Microsoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

La tabella seguente elenca gli oggetti e le relative variabili usate per gli esempi in questo articolo.

| Oggetti di gestione client                  | Variabile di codice         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsCatalogManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Oggetti di gestione client di Data Lake Store:
* DataLakeStoreAccountManagementClient - Usato per creare e gestire archivi Data Lake.
* DataLakeFileSystemAccountManagementClient - Usato per attività eseguite su file system quali creare cartelle e file, caricare file, elencare file, accedere a elenchi ACL e credenziali e aggiungere collegamenti ai BLOB del servizio di archiviazione di Azure.

Sebbene sia possibile creare collegamenti all'archiviazione di Azure da Data Lake, non è possibile accedere al contenuto. Per accedervi è necessario usare le API contenute in Azure Storage SDK. È possibile, tuttavia, eseguire script U-SQL nei BLOB del servizio di archiviazione di Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Oggetti di gestione client di Data Lake Analytics:
* DataLakeAnaylyticsAccountManagementClient - Usato per creare e gestire account di Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient - Usato per configurare database SQL, incluso l'elenco degli schemi.
* DataLakeAnalyticsJobManagementClient - Usato per creare e gestire processi U-SQL.

## <a name="create-accounts"></a>Creare account
Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione.  Il pagamento viene richiesto solo per la durata di esecuzione di un processo.  Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

Per ogni account di Data Lake Analytics deve essere configurato un account di Data Lake Store.
  
### <a name="create-a-data-lake-store-account"></a>Creare un account Archivio Data Lake
Il codice seguente mostra come creare un account di Data Lake Store. Prima di usare il metodo Create, è necessario definirne i parametri specificando una località.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
Il codice seguente mostra come creare un account di Data Lake Analytics. Il metodo Create dell'oggetto DataLakeAnalyticsAccountManagementClient accetta una raccolta di account di archiviazione Data Lake come uno dei propri parametri. La raccolta deve essere popolata con istanze di oggetti DataLakeStoreAccountInfo. In questo esempio gli oggetti DataLakeStoreAccountInfo vengono ottenuti con un metodo helper (AdlaFromAdlsStoreAccounts). Inoltre, non tutti gli account di archiviazione di Data Lake di una sottoscrizione devono necessariamente trovarsi in un singolo account di Data Lake Analytics, pertanto questo codice verifica i nomi a fronte di un elenco approvato.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>Gestire account

### <a name="list-data-lake-store-and-analytic-accounts"></a>Elencare account di Data Lake Store e Data Lake Analytics
Il codice seguente elenca gli account di archiviazione di Data Lake contenuti in una sottoscrizione. Le operazioni di elenco non sempre forniscono tutte le proprietà di un oggetto e in alcuni casi è necessario eseguire un'operazione Get sull'oggetto.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>Ottenere un account
Il codice seguente usa DataLakeAnalyticsAccountManagementClient per restituire un account di Data Lake Analytics, se tale account esiste. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

Analogamente, è possibile usare con le stesse modalità DataLakeStoreAccountManagementClient (_adlsClient), per ottenere un account di Data Lake Store.        
### <a name="delete-an-account"></a>Eliminare un account
Il codice seguente elimina un account di Data Lake Analytics, se tale account esiste. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

È anche possibile eliminare un account di Data Lake Store con DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Ottenere l'account di Data Lake Store predefinito
Per ogni account di Data Lake Analytics deve essere configurato un account di Data Lake Store. Usare questo codice per determinare l'account di archiviazione predefinito per Data Lake Store.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>Gestire le origini dati
Analisi Data Lake supporta attualmente le seguenti origini dati:

* [Archiviazione di Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e collegamenti agli account di archiviazione di Azure. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Includere un collegamento ad Archiviazione di Azure in Data Lake
È possibile creare collegamenti ai blog di Archiviazione di Azure nell'ambiente Data Lake. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Elencare le origini dati di Data Lake
Il codice seguente elenca gli account di Data Lake Store e gli account di Data Lake Storage (per Archiviazione di Azure) per un account Data Lake Analytics specificato.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Caricare un file in un account Data Lake Store
Il codice seguente usa DataLakeStoreFileSystemManagementClient per caricare un file locale in un account Data Lake Store.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Creare un file in un account di Data Lake Store
Oltre a caricare i file, è possibile creare file a livello di codice nell'account di Data Lake Store per l'analisi. Il codice seguente scrive i primi quattro valori di 100 matrici di byte casuali in un file con estensione csv.

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Copiare i file da un account di Data Lake Store
Il codice seguente mostra le operazioni eseguite su file system con un oggetto DataLakeFileSystemAccountManagementClient. Copia i file del foglio di calcolo dalla directory Samples/Data/AmbulanceData in una directory locale nel computer in uso.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Elencare i contenitori di Archiviazione di Azure
Il codice seguente elenca i contenitori per un account di Archiviazione di Azure specificato.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificare i percorsi degli account di Archiviazione di Azure
Il codice seguente controlla se l'account di Archiviazione di Azure (storageAccntName) esiste in un account di Data Lake Analytics (analyticsAccountName), e se esiste un contenitore (containerName) nell'account di Archiviazione di Azure. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gestire cataloghi e processi
L'oggetto DataLakeAnalyticsCatalogManagementClient offre metodi per gestire il database SQL fornito per ogni archivio di Azure Data Lake Store. DataLakeAnalyticsJobManagementClient offre metodi per inviare e gestire i processi eseguiti nel database con gli script U-SQL.

### <a name="list-databases-and-schemas"></a>Elencare database e schemi
Tra le diverse operazioni che è possibile elencare le più comuni sono i database e i relativi schemi. Il codice seguente ottiene una raccolta di database e quindi enumera lo schema per ogni database.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

Se eseguito sul database master predefinito, l'output di questo esempio è il seguente:

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>Elencare colonne di tabella
Il codice seguente mostra come accedere al database con un client di gestione del catalogo di Data Lake Analytics per elencare le colonne di una tabella specificata.

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Elencare i processi non riusciti
Il codice seguente elenca informazioni sui processi non riusciti.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>Fare riferimento ad Archiviazione di Azure negli script U-SQL
Il codice seguente è l'inizio di uno script U-SQL, che indica di leggere i dati da un file su un account di Data Lake Store: "/Samples/Data/SearchLog.tsv"

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

Per leggere i dati da un BLOB in un account di Archiviazione di Azure collegato, è necessario usare l'URL completo del BLOB nel formato seguente:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

Se ad esempio un file di origine (SearchLog.tsv) è archiviato in un contenitore BLOB denominato "samples" nell'account di archiviazione "contoso_33", il percorso dell'istruzione FROM sarà il seguente:

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Gruppi di risorse di Azure e Data Lake Analytics
Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Azure Resource Manager consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata. Per la distribuzione viene usato un modello; questo modello può essere usato per diversi ambienti, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md). 

Un servizio di Analisi Data Lake può includere i componenti seguenti:

* Account di Azure Data Lake Analytics
* Account di archiviazione predefinito obbligatorio di Azure Data Lake
* Uno o più account di Azure Data Lake Analytics
* Uno o più account di Azure Data Lake Store, almeno uno è obbligatorio
* Account aggiuntivi di Azure Data Lake collegati
* Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo di gestione delle risorse per semplificarne la gestione.

![Account e archiviazione di Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Analisi Data Lake e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure,
mentre il gruppo di gestione delle risorse può trovarsi anche in un data center diverso.  

## <a name="see-also"></a>Vedere anche
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

