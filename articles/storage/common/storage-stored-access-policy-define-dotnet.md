---
title: Creare criteri di accesso archiviati con .NET
titleSuffix: Azure Storage
description: Informazioni su come creare criteri di accesso archiviati usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: f4a0d69f3687f0dcc174a2d8a1275a2bf55d9ecf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85504390"
---
# <a name="create-a-stored-access-policy-with-net"></a>Creare criteri di accesso archiviati con .NET

Un criterio di accesso archiviato offre un livello di controllo aggiuntivo sulle firme di accesso condiviso a livello di servizio (SAS) sul lato server. La definizione di un criterio di accesso archiviato serve per raggruppare le firme di accesso condiviso e per fornire restrizioni aggiuntive per le firme di accesso condiviso associate ai criteri. È possibile usare un criterio di accesso archiviato per modificare l'ora di inizio, l'ora di scadenza o le autorizzazioni per una firma di accesso condiviso oppure per revocarla dopo che è stata rilasciata.
  
Le seguenti risorse di archiviazione di Azure supportano i criteri di accesso archiviati:  
  
- Contenitori BLOB  
- Condivisioni file  
- Code  
- Tabelle  
  
> [!NOTE]
> Un criterio di accesso archiviato in un contenitore può essere associato a una firma di accesso condiviso che concede le autorizzazioni al contenitore stesso o ai BLOB in esso contenuti. Analogamente, un criterio di accesso archiviato in una condivisione file può essere associato a una firma di accesso condiviso che concede le autorizzazioni per la condivisione stessa o per i file in esso contenuti.  
>
> I criteri di accesso archiviati sono supportati solo per la firma di accesso condiviso del servizio. I criteri di accesso archiviati non sono supportati per la firma di accesso condiviso dell'account o della delega utente.  

Per altre informazioni sui criteri di accesso archiviati, vedere [definire un criterio di accesso archiviato](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Creare criteri di accesso archiviati

L'operazione REST sottostante per creare criteri di accesso archiviati è [impostata come ACL del contenitore](/rest/api/storageservices/set-container-acl). È necessario autorizzare l'operazione a creare criteri di accesso archiviati tramite chiave condivisa usando le chiavi di accesso dell'account in una stringa di connessione. L'autorizzazione dell'operazione di **impostazione ACL del contenitore** con Azure ad credenziali non è supportata. Per ulteriori informazioni, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Negli esempi di codice seguenti vengono creati criteri di accesso archiviati in un contenitore. È possibile usare i criteri di accesso per specificare i vincoli per una firma di accesso condiviso del servizio sul contenitore o i relativi BLOB.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per creare un criterio di accesso archiviato in un contenitore con la versione 12 della libreria client .NET per archiviazione di Azure, chiamare uno dei metodi seguenti:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

Nell'esempio seguente viene creato un criterio di accesso archiviato che è attivo per un giorno e che concede le autorizzazioni di lettura/scrittura:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per creare un criterio di accesso archiviato in un contenitore con la versione 12 della libreria client .NET per archiviazione di Azure, chiamare uno dei metodi seguenti:

- [CloudBlobContainer. semissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Nell'esempio seguente viene creato un criterio di accesso archiviato che è attivo per un giorno e che concede le autorizzazioni di lettura, scrittura ed elenco:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Vedere anche

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](storage-sas-overview.md)
- [Definire criteri di accesso archiviati](/rest/api/storageservices/define-stored-access-policy)
- [Configurare le stringhe di connessione di archiviazione di Azure](storage-configure-connection-string.md)
