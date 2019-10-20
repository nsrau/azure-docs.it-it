---
title: Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET (anteprima)-archiviazione di Azure
description: Informazioni su come creare una firma di accesso condiviso di delega utente usando le credenziali Azure Active Directory in archiviazione di Azure tramite la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595221"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET (anteprima)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con la libreria client di archiviazione di Azure per .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Eseguire l'autenticazione con la libreria di identità di Azure (anteprima)

La libreria client di identità di Azure per .NET (anteprima) autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure.

Quando il codice è in esecuzione nell'ambiente di sviluppo, l'autenticazione può essere gestita automaticamente oppure è possibile che sia necessario un account di accesso del browser, a seconda degli strumenti che si stanno usando. Microsoft Visual Studio supporta Single Sign-On (SSO), in modo che l'account utente Active Azure AD venga usato automaticamente per l'autenticazione. Per ulteriori informazioni su SSO, vedere [Single Sign-on to Applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

Altri strumenti di sviluppo potrebbero richiedere l'accesso tramite un Web browser. È anche possibile usare un'entità servizio per l'autenticazione dall'ambiente di sviluppo. Per altre informazioni, vedere [creare un'identità per l'app Azure nel portale](../../active-directory/develop/howto-create-service-principal-portal.md).

Dopo l'autenticazione, la libreria client Azure Identity riceve una credenziale token. Questa credenziale del token viene quindi incapsulata nell'oggetto client del servizio creato per eseguire operazioni in archiviazione di Azure. Questa operazione viene gestita automaticamente dalla libreria ottenendo le credenziali del token appropriate.

Per altre informazioni sulla libreria client di Azure Identity, vedere [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Assegnare i ruoli RBAC per l'accesso ai dati

Quando un Azure AD entità di sicurezza tenta di accedere ai dati BLOB, l'entità di sicurezza deve avere le autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo RBAC che concede l'accesso ai dati BLOB in archiviazione di Azure. Per informazioni sull'assegnazione di autorizzazioni tramite RBAC, vedere la sezione assegnare i **ruoli RBAC per i diritti di accesso** in [autorizzare l'accesso a BLOB e code di Azure con Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Installare i pacchetti di anteprima

Gli esempi in questo articolo usano la versione di anteprima più recente della [libreria client di archiviazione di Azure per l'archiviazione BLOB](https://www.nuget.org/packages/Azure.Storage.Blobs). Per installare il pacchetto di anteprima, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Gli esempi in questo articolo usano anche la versione di anteprima più recente della [libreria client di identità di Azure per .NET](https://www.nuget.org/packages/Azure.Identity/) per l'autenticazione con le credenziali Azure ad. Per installare il pacchetto di anteprima, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Aggiungere le direttive using

Aggiungere le seguenti direttive di `using` al codice per usare le versioni di anteprima delle librerie di identità di Azure e client di archiviazione di Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Ottenere le credenziali di un token autenticato

Per ottenere una credenziale token che il codice può usare per autorizzare le richieste ad archiviazione di Azure, creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Il frammento di codice seguente mostra come ottenere le credenziali del token autenticato e usarle per creare un client del servizio per l'archiviazione BLOB:

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Ottenere la chiave di delega utente

Ogni firma di accesso condiviso è firmata con una chiave. Per creare una firma di accesso condiviso di delega utente, è necessario prima richiedere una chiave di delega utente, che viene quindi usata per firmare la firma di accesso condiviso. La chiave di delega utente è analoga alla chiave dell'account usata per firmare una firma di accesso condiviso del servizio o una firma di accesso condiviso dell'account, con la differenza che si basa sulle credenziali Azure AD. Quando un client richiede una chiave di delega utente usando un token OAuth 2,0, archiviazione di Azure restituisce la chiave di delega utente per conto dell'utente.

Una volta creata la chiave di delega utente, è possibile usare tale chiave per creare un numero qualsiasi di firme di accesso condiviso per la delega utente, per tutta la durata della chiave. La chiave di delega utente è indipendente dal token OAuth 2,0 usato per acquisirla, pertanto non è necessario rinnovare il token finché la chiave è ancora valida. È possibile specificare che la chiave sia valida per un periodo di un massimo di 7 giorni.

Per richiedere la chiave di delega utente, usare uno dei metodi seguenti:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Il seguente frammento di codice ottiene la chiave di delega dell'utente e ne scrive le proprietà:

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Creare il token SAS

Il frammento di codice seguente illustra come creare un nuovo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e specificare i parametri per la firma di accesso condiviso della delega utente. Il frammento chiama quindi [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) per ottenere la stringa del token SAS. Infine, il codice compila l'URI completo, inclusi l'indirizzo della risorsa e il token di firma di accesso condiviso.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Esempio: ottenere una firma di accesso condiviso di delega utente

Il metodo di esempio seguente mostra il codice completo per l'autenticazione dell'entità di sicurezza e la creazione della firma di accesso condiviso della delega utente:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Esempio: lettura di un BLOB con una firma di accesso condiviso dell'utente

Nell'esempio seguente viene verificata la firma di accesso condiviso della delega utente creata nell'esempio precedente da un'applicazione client simulata. Se la firma di accesso condiviso è valida, l'applicazione client è in grado di leggere il contenuto del BLOB. Se la firma di accesso condiviso non è valida, ad esempio se è scaduta, archiviazione di Azure restituisce il codice di errore 403 (accesso negato).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedi anche

- [Operazione di ottenimento della chiave di delega utente](/rest/api/storageservices/get-user-delegation-key)
- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
