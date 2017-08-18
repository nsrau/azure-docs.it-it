---
title: Copiare BLOB da un account di archiviazione in un asset di Servizi multimediali di Azure | Microsoft Docs
description: "Questo argomento illustra come copiare un BLOB esistente in un asset di Servizi multimediali. L’esempio usa le estensioni SDK .NET dei Servizi multimediali di Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/03/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 839ad8c9437918b3f64d42500a7bfbad19afa3c4
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Copia di un BLOB esistente in un asset di Servizi multimediali
Questo argomento illustra come copiare BLOB da un account di archiviazione in un nuovo asset di Servizi multimediali di Azure (AMS) usando le [estensioni SDK .NET dei Servizi multimediali di Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

I metodi di estensione funzionano con:

- Asset regolari.
- Asset di archivio live (formato FragBlob).
- Asset di origine e destinazione appartenenti a diversi account di Servizi multimediali (anche in datacenter diversi). Questa operazione, tuttavia, può comportare l'addebito di costi. Per altre informazioni sui prezzi, vedere [Trasferimenti di dati](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> È sconsigliabile tentare di modificare i contenuti dei contenitori BLOB generati da Servizi multimediali senza usare le API di Servizi multimediali.
> 

L'argomento mostra due esempi di codice:

1. Copiare i BLOB da un asset in un account AMS in un nuovo asset in un altro account AMS.
2. Copiare i BLOB da alcuni account di storage in un nuovo asset in un account AMS.

## <a name="copy-blobs-between-two-ams-accounts"></a>Copiare i BLOB tra due account AMS  

### <a name="prerequisites"></a>Prerequisiti

Due account dei Servizi multimediali. Vedere l'argomento [Come creare un account di Servizi multimediali](media-services-portal-create-account.md).

### <a name="download-sample"></a>Scaricare un esempio
È possibile seguire i passaggi descritti in questo articolo oppure scaricare un esempio che contiene il codice descritto in questo articolo da [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

### <a name="set-up-your-project"></a>Configurare il progetto

1. Configurare l'ambiente di sviluppo come descritto in [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Sviluppo di applicazioni di Servizi multimediali con .NET). 
2. Aggiungere gli altri riferimenti richiesti per il progetto System.Configuration.
3. Aggiungere la sezione appSettings al file con estensione CONFIG e aggiornare i valori in base all'account di Servizi multimediali, all'account di archiviazione di destinazione e all'ID dell'asset di origine.  

```   
<appSettings>
    <add key="AMSSourceAADTenantDomain" value="AADTenantDomain"/>
    <add key="AMSSourceRESTAPIEndpoint" value="RESTAPIEndpoint"/>
    <add key="AMSDestAADTenantDomain" value="AADTenantDomain"/>
    <add key="AMSDestRESTAPIEndpoint" value="RESTAPIEndpoint"/>
    <add key="DestStorageAccountName" value="name"/>
    <add key="DestStorageAccountKey" value="key"/>
    <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>
</appSettings>
```

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>Copiare i BLOB da un asset in un account AMS in un asset in un altro account AMS

Il codice seguente usa il metodo dell'estensione **IAsset.Copy** per copiare tutti i file di un asset di origine in un asset di destinazione usando un'unica estensione.

```
using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Linq;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;

namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        static string _sourceAADTenantDomain = ConfigurationManager.AppSettings["AMSSourceAADTenantDomain"];
        static string _sourceRESTAPIEndpoint = ConfigurationManager.AppSettings["AMSSourceRESTAPIEndpoint"];
        static string _destAADTenantDomain = ConfigurationManager.AppSettings["AMSDestAADTenantDomain"];
        static string _destRESTAPIEndpoint = ConfigurationManager.AppSettings["AMSDestRESTAPIEndpoint"];
        static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
        static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
        static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];

        private static CloudMediaContext _sourceContext = null;
        private static CloudMediaContext _destContext = null;

        static void Main(string[] args)
        {
            var tokenCredentials1 = new AzureAdTokenCredentials(_sourceAADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider1 = new AzureAdTokenProvider(tokenCredentials1);
            var tokenCredentials2 = new AzureAdTokenCredentials(_destAADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider2 = new AzureAdTokenProvider(tokenCredentials2);

            // Create the context for your source Media Services account.
            _sourceContext = new CloudMediaContext(new Uri(_sourceRESTAPIEndpoint), tokenProvider1);

            // Create the context for your destination Media Services account.
            _destContext = new CloudMediaContext(new Uri(_destRESTAPIEndpoint), tokenProvider2);

            // Get the credentials of the default Storage account bound to your destination Media Services account.
            StorageCredentials destinationStorageCredentials =
                new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);

            // Get a reference to the source asset in the source context.
            IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();

            // Create an empty destination asset in the destination context.
            IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);

            // Copy the files in the source asset instance into the destination asset instance.
            sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

            Console.WriteLine("Done");
        }
    }
}
```

## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>Copiare i BLOB da un account di archiviazione in un account AMS 

### <a name="prerequisites"></a>Prerequisiti

- Un account di archiviazione da cui si desidera copiare i BLOB.
- Un account AMS in cui si desidera copiare i BLOB.

### <a name="set-up-your-project"></a>Configurare il progetto

1. Configurare l'ambiente di sviluppo come descritto in [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Sviluppo di applicazioni di Servizi multimediali con .NET). 
2. Aggiungere gli altri riferimenti richiesti per il progetto System.Configuration.
3. Aggiungere la sezione appSettings al file di configurazione e aggiornare i valori in base alla risorsa di archiviazione di origine e agli account AMS di destinazione.

```
<appSettings>
  <add key="SourceStorageAccountName" value="name" />
  <add key="SourceStorageAccountKey" value="key" />
  <add key="AMSAADTenantDomain" value="tenant"/>
  <add key="AMSESTAPIEndpoint" value="endpoint"/>
  <add key="AMSStorageAccountName" value="name" />
  <add key="AMSStorageAccountKey" value="key" />
</appSettings>
```

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>Copiare i BLOB da alcuni account di storage in un nuovo asset in un account AMS

Il codice seguente copia i BLOB da un account di archiviazione in un asset di Servizi multimediali. 

>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) argomento.

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
    
namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AMSAADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _AMSRESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSESTAPIEndpoint"];
        private static readonly string _AMSStorageAccountName =
            ConfigurationManager.AppSettings["AMSStorageAccountName"];
        private static readonly string _AMSStorageAccountKey =
            ConfigurationManager.AppSettings["AMSStorageAccountKey"];
        private static readonly string _sourceStorageAccountName =
            ConfigurationManager.AppSettings["SourceStorageAccountName"];
        private static readonly string _sourceStorageAccountKey =
            ConfigurationManager.AppSettings["SourceStorageAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static CloudStorageAccount _sourceStorageAccount = null;
        private static CloudStorageAccount _destinationStorageAccount = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain, 
                AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Create the context for your source Media Services account.
            _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);
            
            _sourceStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName,
                    _sourceStorageAccountKey), true);

            _destinationStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_AMSStorageAccountName,
                    _AMSStorageAccountKey), true);

            CloudBlobClient sourceCloudBlobClient =
                _sourceStorageAccount.CreateCloudBlobClient();
            CloudBlobContainer sourceContainer =
                sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");

            CreateAssetFromExistingBlobs(sourceContainer);

            Console.WriteLine("Done");
        }

        static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
        {
            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);

            ILocator destinationLocator =
                _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            CloudBlobContainer destAssetContainer =
                destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);

            if (destAssetContainer.CreateIfNotExists())
            {
                destAssetContainer.SetPermissions(new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = sourceBlobContainer.ListBlobs();

            foreach (var sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);

                ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);

                CopyBlob(sourceBlob as ICloudBlob, destAssetContainer);

                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
                Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Set the primary asset file.
            // If, for example, we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray().FirstOrDefault();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            if (ismAssetFile != null)
            {
                ismAssetFile.IsPrimary = true;
                ismAssetFile.Update();
            }

            return asset;
        }

        /// <summary>
        /// Copies the specified blob into the specified container.
        /// </summary>
        /// <param name="sourceBlob">The source container.</param>
        /// <param name="destinationContainer">The destination container.</param>
        static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
        {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                    // The StartCopyFromBlob is an async operation, 
                    // so we want to check if the copy operation is completed before proceeding. 
                    // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                    destinationBlob.FetchAttributes();
                    if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                    {
                        break;
                    }
                    //It's still not completed. So wait for some time.
                    System.Threading.Thread.Sleep(1000);
                }

                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
        }
    }
}
```
## <a name="next-steps"></a>Passaggi successivi

Ora è possibile codificare gli asset caricati. Per altre informazioni, vedere [Encode assets](media-services-portal-encode.md)(Codificare gli asset).

È anche possibile usare Funzioni di Azure per attivare un processo di codifica basato su un file che arriva nel contenitore configurato. Per altre informazioni, vedere [questo esempio](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


