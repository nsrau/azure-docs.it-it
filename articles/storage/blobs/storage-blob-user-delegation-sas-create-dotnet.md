---
title: Usare .NET per creare una classe di accesso client di delega utente per un contenitore o un BLOBUse .NET to create a user delegation SAS for a container or blob
titleSuffix: Azure Storage
description: Informazioni su come creare una sAS di delega utente con credenziali di Azure Active Directory usando la libreria client .NET per Archiviazione di Azure.Learn how to create a user delegation SAS with Azure Active Directory credentials by using the .NET client library for Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371837"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Creare una classe di accesso client di delega utente per un contenitore o un BLOB con .NETCreate a user delegation SAS for a container or blob with .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali di Azure Active Directory (Azure AD) per creare una provider di servizi di accesso condiviso di delega utente per un contenitore o un BLOB con la libreria client di Archiviazione di Azure per .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Assegnare ruoli RBAC per l'accesso ai datiAssign RBAC roles for access to data

Quando un'entità di sicurezza di Azure AD tenta di accedere ai dati BLOB, tale entità di sicurezza deve disporre delle autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente di Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo Controllo degli accessi in base al ruolo che conceda l'accesso ai dati BLOB in Archiviazione di Azure.Whether the security principal is a managed identity in Azure or an Azure AD user account running code in the development environment, the security principal must be assigned an RBAC role that grants access to blob data in Azure Storage. Per informazioni sull'assegnazione di autorizzazioni tramite controllo degli accessi in base al ruolo, vedere la sezione **relativa all'assegnazione** dei ruoli RBAC per i diritti di accesso in [Autorizzare l'accesso ai BLOB e alle code di Azure tramite Azure Active Directory.](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Per altre informazioni su come eseguire l'autenticazione con la libreria client di Azure Identity da Archiviazione di Azure, vedere la sezione **Autenticazione con la libreria di identità** di Azure in [Autorizzare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)le risorse di Azure.To learn more about how to authenticate with the Azure Identity client library from Azure Storage, see the section titled Authenticate with the Azure Identity library in Authorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources.

## <a name="add-using-directives"></a>Aggiungere le direttive using

Aggiungere le `using` direttive seguenti al codice per usare le librerie client di Archiviazione di Azure.Add the following directives to your code to use the Azure Identity and Azure Storage client libraries.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Ottenere una credenziale di token autenticataGet an authenticated token credential

Per ottenere una credenziale token che il codice può usare per autorizzare le richieste ad Archiviazione di Azure, creare un'istanza della classe [DefaultAzureCredential.To](/dotnet/api/azure.identity.defaultazurecredential) get a token credential that your code can use to authorize requests to Azure Storage, create an instance of the DefaultAzureCredential class.

Il frammento di codice seguente mostra come ottenere le credenziali del token autenticate e usarle per creare un client di servizio per l'archiviazione BLOB:The following code snippet shows how to get the authenticated token credential and use it to create a service client for Blob storage:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Ottenere la chiave di delega utenteGet the user delegation key

Ogni firma di accesso base è firmata con una chiave. Per creare una firma di accesso client di firma di accesso utente, è innanzitutto necessario richiedere una chiave di delega utente, che viene quindi utilizzata per firmare la firma di accesso base. La chiave di delega utente è analoga alla chiave dell'account usata per firmare una firma di accesso condiviso del servizio o una firma di accesso condiviso dell'account, ad eccezione del fatto che si basa sulle credenziali di Azure AD. Quando un client richiede una chiave di delega utente usando un token OAuth 2.0, Archiviazione di Azure restituisce la chiave di delega utente per conto dell'utente.

Una volta che si dispone della chiave di delega utente, è possibile utilizzare tale chiave per creare un numero qualsiasi di firme di accesso condiviso di delega utente per tutta la durata della chiave. La chiave di delega utente è indipendente dal token OAuth 2.0 utilizzato per acquisirla, pertanto non è necessario rinnovare il token finché la chiave è ancora valida. È possibile specificare che la chiave è valida per un periodo massimo di 7 giorni.

Utilizzare uno dei seguenti metodi per richiedere la chiave di delega dell'utente:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Il frammento di codice seguente ottiene la chiave di delega utente e scrive le relative proprietà:The following code snippet gets the user delegation key and writes out its properties:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Creare il token di firma di accesso condivisoCreate the SAS token

Il frammento di codice seguente illustra la creazione di un nuovo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e fornisce i parametri per la firma di accesso livello di delega dell'utente. Il frammento di codice chiama quindi [il ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) per ottenere la stringa del token di firma di accesso condiviso. Infine, il codice compila l'URI completo, inclusi l'indirizzo di risorsa e il token di firma di accesso condiviso.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

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
```

## <a name="example-get-a-user-delegation-sas"></a>Esempio: Ottenere una sAS di delega utenteExample: Get a user delegation SAS

The following example method shows the complete code for authenticating the security principal and creating the user delegation SAS:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

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
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Esempio: leggere un BLOB con una connessione SAS di delega utenteExample: Read a blob with a user delegation SAS

Nell'esempio seguente viene verificata la sAS di delega degli utenti creata nell'esempio precedente da un'applicazione client simulata. Se la firma di accesso client è valida, l'applicazione client è in grado di leggere il contenuto del BLOB. Se la firma di accesso locale non è valida, ad esempio se è scaduta, Archiviazione di Azure restituisce il codice di errore 403 (Accesso negato).

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
    catch (RequestFailedException e)
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

## <a name="see-also"></a>Vedere anche

- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md)
- [Operazione Get User Delegation Key](/rest/api/storageservices/get-user-delegation-key)
- [Creare una sAS (GH) di delega utenteCreate a user delegation SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
