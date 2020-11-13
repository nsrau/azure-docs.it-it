---
title: Creare una firma di accesso condiviso con .NET
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso (SAS) dell'account usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1cb882ac1051c41f4d887a9ff4dd8cd64bc9f56c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593604"
---
# <a name="create-an-account-sas-with-net"></a>Creare una firma di accesso condiviso con .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare la chiave dell'account di archiviazione per creare una firma di accesso condiviso dell'account con la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Creare un SAS dell'account

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Una firma di accesso condiviso dell'account è firmata con la chiave di accesso dell'account. Utilizzare la classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) per creare le credenziali utilizzate per firmare la firma di accesso condiviso. Successivamente, creare un nuovo oggetto [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) e chiamare il [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) per ottenere la stringa del token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Per creare una firma di accesso condiviso dell'account per un contenitore, chiamare il metodo [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

L'esempio di codice seguente crea una firma di accesso condiviso valida per i servizi BLOB e File e concede al client le autorizzazioni di lettura, scrittura ed elenco per accedere alle API a livello di servizio. Per la firma di accesso condiviso dell'account il protocollo è limitato ad HTTPS, pertanto è necessario creare una richiesta HTTPS. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Usare una firma di accesso condiviso dell'account da un client

Per usare la firma di accesso condiviso dell'account per accedere alle API a livello di servizio per il servizio BLOB, costruire un oggetto client del servizio BLOB usando la firma di accesso condiviso e l'endpoint di archiviazione BLOB per l'account di archiviazione.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)



:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

In questo frammento di codice sostituire il `<storage-account>` segnaposto con il nome dell'account di archiviazione.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](storage-sas-overview.md)
- [Creare un SAS dell'account](/rest/api/storageservices/create-account-sas)
