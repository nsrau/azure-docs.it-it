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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestire Azure Data Lake Analytics tramite .NET SDK di Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite .NET SDK di Azure. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con installato Visual C++**.
* **Microsoft Azure SDK per .NET versione 2.5 o successiva**.  Installarlo usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* **Pacchetti NuGet richiesti**

### <a name="install-nuget-packages"></a>Installare i pacchetti NuGet
   
   1. In Visual Studio, fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   2. Nella scheda **Gestione pacchetti NuGet** verificare che l'opzione **Origine pacchetto** sia impostata su **nuget.org** e che la casella di controllo **Includi versione preliminare** sia selezionata.

   3. Cercare e installare i pacchetti NuGet seguenti:

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - Questa esercitazione usa la versione&2;.2.12
    - Microsoft.Azure.Management.DataLake.Analytics - Questa esercitazione usa la versione&2;.1.0 in anteprima
    - Microsoft.Azure.Management.DataLake.Store - Questa esercitazione usa la versione&2;.1.0 in anteprima

   4. Chiudere la finestra **Gestione pacchetti NuGet**.

## <a name="client-management-objects"></a>Oggetti di gestione client
Le API di Azure Data Lake Analytics e Azure Data Lake Store includono set di oggetti di gestione client dai quali è possibile eseguire la maggior parte della programmazione; si trovano nei due spazi dei nomi seguenti:
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

Nella tabella seguente vengono indicati gli oggetti di gestione client, con le variabili usate per gli esempi di codice presenti nell'articolo.

| Oggetti di gestione client                  | Variabile di codice        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsCatalogManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Oggetti di gestione client di Data Lake Store:
* DataLakeStoreAccountManagementClient - Usato per creare e gestire account Data Lake Store.
* DataLakeFileSystemAccountManagementClient - Usato per attività eseguite su file system quali creare cartelle e file, caricare file, elencare file, accedere a elenchi ACL e credenziali e aggiungere collegamenti ai BLOB del servizio di archiviazione di Azure.

Sebbene sia possibile creare collegamenti all'archiviazione di Azure da Data Lake, non è possibile accedere al contenuto. Per accedervi è necessario usare le API contenute in Azure Storage SDK. È possibile, tuttavia, eseguire script U-SQL nei BLOB del servizio di archiviazione di Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Oggetti di gestione client di Data Lake Analytics:
* DataLakeAnalyticsAccountManagementClient - Usato per creare e gestire account Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient - Usato per esplorare gli elementi del catalogo in Data Lake Analytics.
* DataLakeAnalyticsJobManagementClient - Usato per inviare e gestire processi in Data Lake Analytics.

### <a name="example"></a>Esempio

Inizializzare gli oggetti di gestione client usando le proprie credenziali, ottenute con il metodo di autenticazione scelto, descritto più avanti nell'articolo. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>Autenticarsi e connettersi ad Azure Data Lake Analytics
Sono disponibili più opzioni per accedere ad Azure Data Lake Analytics.

### <a name="interactive-with-provided-credentials"></a>Modalità interattiva con credenziali fornite
Il frammento seguente mostra il metodo di autenticazione più semplice, in cui l'utente fornisce credenziali come un nome utente, una password o un numero PIN.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

È consigliabile creare una propria applicazione e una propria entità servizio all'interno del tenant di Azure Active Directory e quindi di usare l'ID client dell'applicazione invece dell'ID di esempio usato qui.

### <a name="non-interactive-with-a-client-secret"></a>Modalità non interattiva tramite segreto client
Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client, la chiave per un'applicazione o un'entità servizio. Usare l'opzione di autenticazione con un'["app Web" di Azure AD esistente](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>Modalità non interattiva tramite entità servizio
Come terza opzione, il frammento seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il certificato di un'entità applicazione o servizio. Usare l'opzione di autenticazione con un'["app Web" di Azure AD esistente](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>Creare account
Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. Per ogni account di Data Lake Analytics deve essere configurato un account di Data Lake Store. Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)

### <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure
Se non ne è già stato creato uno, è necessario creare un gruppo di risorse di Azure per creare i componenti di Data Lake Analytics. Sono necessari l'ID sottoscrizione, le credenziali di autenticazione e un percorso. Il codice seguente mostra come creare il gruppo di risorse:

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

Per altre informazioni, vedere [Gruppi di risorse di Azure e Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).


### <a name="create-a-data-lake-store-account"></a>Creare un account Archivio Data Lake
Il codice seguente mostra come creare un account Data Lake Store. Prima di usare il metodo Create, è necessario definirne i parametri specificando una località.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
Il codice seguente mostra come creare un account Data Lake Analytics usando il metodo asincrono. Il metodo CreateAsync accetta una raccolta di account Data Lake Store come parametro. La raccolta deve essere popolata con istanze di oggetti DataLakeStoreAccountInfo. In questo esempio gli oggetti DataLakeStoreAccountInfo vengono ottenuti con un metodo helper (AdlaFromAdlsStoreAccounts).

Per qualsiasi account Data Lake Analytics, è sufficiente includere gli account Data Lake Store necessari per eseguire le analisi richieste. Uno di questi account Data Lake Store deve essere l'account Data Lake Store predefinito.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>Gestire account

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Elencare gli account Data Lake Store e Data Lake Analytics
Il codice seguente elenca gli account Data Lake Store contenuti in una sottoscrizione. Le operazioni di elenco non sempre forniscono tutte le proprietà di un oggetto e in alcuni casi è necessario eseguire un'operazione Get sull'oggetto.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>Ottenere un account
Il codice seguente usa DataLakeAnalyticsAccountManagementClient per ottenere un account Data Lake Analytics, se tale account esiste.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

Analogamente, è possibile usare con le stesse modalità DataLakeStoreAccountManagementClient (_adlsClient), per ottenere un account Data Lake Store.

### <a name="delete-an-account"></a>Eliminare un account
Il codice seguente elimina un account Data Lake Analytics, se tale account esiste.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

È anche possibile eliminare un account di Data Lake Store con DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Ottenere l'account di Data Lake Store predefinito
Per ogni account di Data Lake Analytics deve essere configurato un account di Data Lake Store. Usare questo codice per determinare l'account di archiviazione predefinito per Data Lake Store.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>Gestire le origini dati
Analisi Data Lake supporta attualmente le seguenti origini dati:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Account di archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account Analytics, è necessario impostare un account Azure Data Lake Store come predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account Analytics, è possibile aggiungere altri account Data Lake Store e collegamenti agli account di archiviazione di Azure (BLOB).

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Collegarsi a un account di archiviazione di Azure da un account Data Lake Analytics
È possibile creare collegamenti agli account di archiviazione di Azure.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Elencare le origini dati di Data Lake Store
Il codice seguente elenca gli account Data Lake Store e gli account di archiviazione di Azure usati per un account Data Lake Analytics specificato.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>Caricare e scaricare cartelle e file
È possibile usare l'oggetto di gestione client del file system Data Lake Store per caricare e scaricare file o cartelle individuali da Azure nel computer locale, usando i metodi seguenti:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

Il primo parametro di questi metodi è il nome dell'account Data Lake Store, seguito dai parametri del percorso di origine e di quello di destinazione.

L'esempio seguente mostra come scaricare una cartella in Data Lake Store.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Creare un file in un account Data Lake Store
È possibile usare le operazioni I/O di .NET Framework per creare contenuti di un file in un Data Lake Store. Il codice seguente scrive i primi quattro valori di 100 matrici di byte casuali in un file con estensione csv.

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Elencare i contenitori BLOB di un account di archiviazione di Azure
Il codice seguente elenca i contenitori per un account di archiviazione di Azure specificato.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificare i percorsi degli account di Archiviazione di Azure
Il codice seguente controlla se l'account di Archiviazione di Azure (storageAccntName) esiste in un account di Data Lake Analytics (analyticsAccountName), e se esiste un contenitore (containerName) nell'account di Archiviazione di Azure.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gestire cataloghi e processi
L'oggetto DataLakeAnalyticsCatalogManagementClient offre metodi per gestire il database SQL fornito per ogni archivio di Azure Data Lake Store. DataLakeAnalyticsJobManagementClient offre metodi per inviare e gestire i processi eseguiti nel database con gli script U-SQL.

### <a name="list-databases-and-schemas"></a>Elencare database e schemi
Tra le diverse operazioni che è possibile elencare le più comuni sono i database e i relativi schemi. Il codice seguente ottiene una raccolta di database e quindi enumera lo schema per ogni database.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Elencare i processi non riusciti
Il codice seguente elenca informazioni sui processi non riusciti.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>Vedere anche
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

