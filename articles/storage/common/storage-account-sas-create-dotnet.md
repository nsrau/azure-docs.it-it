---
title: Creare una sAS dell'account con .NETCreate an account SAS with .NET
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso dell'account usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9da27cef7bafa94715a42db86fc5a5675a049eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137870"
---
# <a name="create-an-account-sas-with-net"></a>Creare una sAS dell'account con .NETCreate an account SAS with .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare la chiave dell'account di archiviazione per creare una sAS dell'account con la libreria client di [Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="create-an-account-sas"></a>Creare un SAS dell'account

Per creare una firma di accesso condiviso dell'account per un contenitore, chiamare il metodo [CloudStorageAccount.GetSharedAccessSignature.To](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) create an account SAS for a container, call the CloudStorageAccount.GetSharedAccessSignature method.

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

## <a name="use-an-account-sas-from-a-client"></a>Usare una server di accesso sas dell'account da un clientUse an account SAS from a client

Per usare la configurazione di accesso sAS dell'account per accedere alle API a livello di servizio per il servizio BLOB, creare un oggetto client del servizio BLOB usando la configurazione di accesso locale e l'endpoint di archiviazione BLOB per l'account di archiviazione. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

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

## <a name="next-steps"></a>Passaggi successivi

- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md)
- [Creare un SAS dell'account](/rest/api/storageservices/create-account-sas)
