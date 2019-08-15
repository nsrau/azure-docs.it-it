---
title: Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET (anteprima)-archiviazione di Azure
description: Informazioni su come creare una firma di accesso condiviso di delega utente usando le credenziali Azure Active Directory in archiviazione di Azure tramite la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bed95c070649785a701f9d08a98faf29c8ee1413
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990689"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con .NET (anteprima)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con la [libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Installare i pacchetti di anteprima

Gli esempi in questo articolo usano la versione di anteprima più recente della libreria client di archiviazione di Azure per l'archiviazione BLOB. Per installare il pacchetto di anteprima, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Gli esempi in questo articolo usano anche la versione di anteprima più recente della [libreria client di identità di Azure per .NET](https://www.nuget.org/packages/Azure.Identity/) per l'autenticazione con le credenziali Azure ad. La libreria client Azure Identity autentica un'entità di sicurezza. L'entità di sicurezza autenticata può quindi creare la firma di accesso condiviso della delega utente. Per altre informazioni sulla libreria client di Azure Identity, vedere [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per eseguire l'autenticazione con Azure AD le credenziali tramite la libreria client di identità di Azure, usare un'entità servizio o un'identità gestita come entità di sicurezza, a seconda della posizione in cui è in esecuzione il codice. Se il codice è in esecuzione in un ambiente di sviluppo, usare un'entità servizio a scopo di test. Se il codice è in esecuzione in Azure, usare un'identità gestita. Questo articolo presuppone che si stia eseguendo codice dall'ambiente di sviluppo e che venga illustrato come usare un'entità servizio per creare la firma di accesso condiviso della delega utente.

Per creare un'entità servizio con l'interfaccia della riga di comando di Azure e assegnare un ruolo RBAC, chiamare il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Fornire un ruolo di accesso ai dati di archiviazione di Azure da assegnare alla nuova entità servizio. Il ruolo deve includere l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . Per altre informazioni sui ruoli predefiniti forniti per archiviazione di Azure, vedere [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

Fornire inoltre l'ambito per l'assegnazione di ruolo. L'entità servizio creerà la chiave di delega utente, ovvero un'operazione eseguita a livello dell'account di archiviazione, in modo che l'assegnazione di ruolo debba avere come ambito il livello dell'account di archiviazione, il gruppo di risorse o la sottoscrizione. Per ulteriori informazioni sulle autorizzazioni RBAC per la creazione di una firma di accesso condiviso di delega utente, vedere la sezione **assegnare autorizzazioni con RBAC** in creare una firma di accesso condiviso [utente (API REST)](/rest/api/storageservices/create-a-user-delegation-sas).

Se non si dispone di autorizzazioni sufficienti per assegnare un ruolo all'entità servizio, potrebbe essere necessario richiedere al proprietario o all'amministratore dell'account di eseguire l'assegnazione di ruolo.

L'esempio seguente usa l'interfaccia della riga di comando di Azure per creare una nuova entità servizio e assegnare il ruolo di **lettore dati BLOB di archiviazione** con l'ambito dell'account

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Il `az ad sp create-for-rbac` comando restituisce un elenco di proprietà dell'entità servizio in formato JSON. Copiare questi valori in modo che sia possibile usarli per creare le variabili di ambiente necessarie nel passaggio successivo.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

## <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La libreria client di identità di Azure legge i valori da tre variabili di ambiente in fase di esecuzione per autenticare l'entità servizio. Nella tabella seguente viene descritto il valore da impostare per ogni variabile di ambiente.

|Variabile di ambiente|Value
|-|-
|`AZURE_CLIENT_ID`|ID app per l'entità servizio
|`AZURE_TENANT_ID`|ID tenant Azure AD dell'entità servizio
|`AZURE_CLIENT_SECRET`|Password generata per l'entità servizio

> [!IMPORTANT]
> Dopo aver impostato le variabili di ambiente, chiudere e riaprire la finestra della console. Se si usa Visual Studio o un altro ambiente di sviluppo, potrebbe essere necessario riavviare l'ambiente di sviluppo per poter registrare le nuove variabili di ambiente.

## <a name="add-using-directives"></a>Aggiungere le direttive using

Aggiungere le direttive seguenti `using` al codice per usare le versioni di anteprima delle librerie client identità di Azure e archiviazione di Azure.

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

## <a name="authenticate-the-service-principal"></a>Autenticare l'entità servizio

Per autenticare l'entità servizio, creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Il `DefaultAzureCredential` Costruttore legge le variabili di ambiente create in precedenza.

Il frammento di codice seguente mostra come ottenere le credenziali autenticate e usarle per creare un client del servizio per l'archiviazione BLOB

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

## <a name="example-get-a-user-delegation-sas"></a>Esempio: Ottenere una firma di accesso condiviso di delega utente

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Esempio: Leggere un BLOB con una firma di accesso condiviso dell'utente

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

## <a name="see-also"></a>Vedere anche

- [Operazione di ottenimento della chiave di delega utente](/rest/api/storageservices/get-user-delegation-key)
- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-a-user-delegation-sas)
