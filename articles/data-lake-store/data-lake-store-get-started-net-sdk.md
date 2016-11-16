---
title: Usare Data Lake Store .NET SDK per sviluppare le applicazioni | Documentazione Microsoft
description: Usare .NET SDK con Azure Data Lake Store per sviluppare applicazioni
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 47f8601471c6b1f6da5d57d1f30da51af76fba85


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introduzione ad Azure Data Lake Store con .NET SDK
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Informazioni su come usare [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) per eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare file di dati e così via. Per altre informazioni su Data Lake, vedere [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013 o 2015**. Le istruzioni seguenti fanno riferimento a Visual Studio 2015.
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Account di Archivio Data Lake di Azure**. Per istruzioni su come creare un account, vedere [Introduzione ad Azure Data Lake Store tramite il portale di Azure](data-lake-store-get-started-portal.md)
* **Creare un'applicazione di Azure Active Directory**. Usare l'applicazione Azure AD per autenticare l'applicazione Data Lake Store con Azure AD. Per l'autenticazione con Azure AD è possibile usare l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Eseguire l'autenticazione in Data Lake Store con Azure Active Directory).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET
1. Aprire Visual Studio e creare un'applicazione console.
2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
3. In **Nuovo progetto**digitare o selezionare i valori seguenti:
   
   | Proprietà | Valore |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modello |Applicazione console |
   | Nome |CreateADLApplication |
4. Fare clic su **OK** per creare il progetto.
5. Aggiungere i pacchetti NuGet al progetto.
   
   1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   2. Nella scheda **Gestione pacchetti NuGet** verificare che l'opzione **Origine pacchetto** sia impostata su **nuget.org** e che la casella di controllo **Includi versione preliminare** sia selezionata.
   3. Cercare e installare i pacchetti NuGet seguenti:
      
      * `Microsoft.Azure.Management.DataLake.Store` - In questa esercitazione si usa v0.12.5-preview.
      * `Microsoft.Azure.Management.DataLake.StoreUploader` - In questa esercitazione si usa v0.10.6-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - In questa esercitazione si usa v2.2.8-preview.
        
        ![Aggiungere un'origine NuGet](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")
   4. Chiudere la finestra **Gestione pacchetti NuGet**.
6. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.
   
        using System;
        using System.Threading;
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
7. Dichiarare le variabili come illustrato di seguito e specificare i valori per il nome di Data Lake Store e il nome del gruppo di risorse già esistenti. Assicurarsi anche che il nome file e percorso locale forniti esistano già nel computer. Aggiungere il frammento di codice seguente dopo le dichiarazioni dello spazio dei nomi.
   
        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
   
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nelle sezioni rimanenti dell'articolo è possibile vedere come usare i metodi .NET disponibili per eseguire operazioni come autenticazione, caricamento di file e così via.

## <a name="authentication"></a>Autenticazione
### <a name="if-you-are-using-enduser-authentication-recommended-for-this-tutorial"></a>Se si usa l'autenticazione dell'utente finale (consigliata per questa esercitazione)
Questo tipo di autenticazione deve essere usato con un'applicazione client nativa Azure AD esistente. Di seguito ne viene riportata una. Per completare più rapidamente questa esercitazione, è consigliabile adottare questo approccio.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Informazioni utili sul frammento di codice precedente.

* Per completare più rapidamente l'esercitazione, questo frammento di codice usa un dominio di Azure AD e un ID client disponibili per impostazione predefinita per tutte le sottoscrizioni di Azure. È quindi possibile **usare questo frammento così com'è nell'applicazione**.
* Se, tuttavia, si vuole usare il proprio dominio di Azure AD e il proprio ID client dell'applicazione, è necessario creare un'applicazione nativa Azure AD e quindi usare il dominio di Azure AD, l'ID client e l'URI di reindirizzamento per l'applicazione creata. Per istruzioni, vedere [Creare un'applicazione Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

> [!NOTE]
> Le istruzioni disponibili tramite i collegamenti riportati sopra si riferiscono a un'applicazione Web di Azure AD, ma i passaggi sono gli stessi anche se si sceglie di creare un'applicazione client nativa. 
> 
> 

### <a name="if-you-are-using-servicetoservice-authentication-with-client-secret"></a>Se si usa l'autenticazione da servizio a servizio con il segreto client
Il frammento di codice seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il segreto client, la chiave per un'applicazione o un'entità servizio. Usare il frammento con un' [app Web di Azure AD](../resource-group-create-service-principal-portal.md)esistente.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-servicetoservice-authentication-with-certificate"></a>Se si usa l'autenticazione da servizio a servizio con il certificato
Come terza opzione, il frammento seguente può essere usato per autenticare l'applicazione in modo non interattivo, usando il certificato di un'entità applicazione o servizio. Usare il frammento con un' [app Web di Azure AD](../resource-group-create-service-principal-portal.md)esistente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Creare oggetti client
Il frammento seguente crea gli account Data Lake Store e gli oggetti client del file system, usati per inviare richieste al servizio.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Elencare tutti gli account Data Lake Store all'interno di una sottoscrizione
Il frammento seguente elenca tutti gli account Data Lake Store all'interno di una determinata sottoscrizione di Azure.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Creare una directory
Il frammento seguente illustra il metodo `CreateDirectory` che è possibile usare per creare una directory in un account Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Caricare un file
Il frammento seguente illustra il metodo `UploadFile` che è possibile usare per caricare file in un account Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader` supporta il caricamento e il download ricorsivi tra un percorso di file locale e un percorso di file Data Lake Store.    

## <a name="get-file-or-directory-info"></a>Ottenere informazioni su un file o una directory
Il frammento seguente illustra il metodo `GetItemInfo` che è possibile usare per recuperare informazioni su un file o una directory disponibile in Data Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Elencare file o directory
Il frammento seguente illustra il metodo `ListItem` che è possibile usare per elencare il file e le directory in un account Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenare file
Il frammento seguente illustra il metodo `ConcatenateFiles` da usare per concatenare file. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Aggiungere a un file
Il frammento seguente illustra il metodo `AppendToFile` da usare per aggiungere dati a un file già archiviato in un account Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Scaricare un file
Il frammento seguente illustra il metodo `DownloadFile` da usare per scaricare un file da un account Data Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Informazioni di riferimento su .NET SDK con Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
* [Data Lake Store REST Reference (Informazioni di riferimento su REST per Data Lake Store)](https://msdn.microsoft.com/library/mt693424.aspx)




<!--HONumber=Nov16_HO2-->


