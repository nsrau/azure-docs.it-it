---
title: Definire criteri di accesso archiviati con .NET - Archiviazione di AzureDefine a stored access policy with .NET - Azure Storage
description: Informazioni su come definire criteri di accesso archiviati usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990741"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definire criteri di accesso archiviati con .NETDefine a stored access policy with .NET

I criteri di accesso archiviati forniscono un ulteriore livello di controllo sulle firme di accesso condiviso (SAS) a livello di servizio sul lato server. La definizione di un criterio di accesso archiviato consente di raggruppare le firme di accesso condiviso e di fornire restrizioni aggiuntive per le firme di accesso condiviso associate dal criterio. È possibile usare un criterio di accesso archiviato per modificare l'ora di inizio, l'ora di scadenza o le autorizzazioni per una sAS o revocarla dopo che è stata rilasciata.
  
 I criteri di accesso archiviati sono supportati dalle seguenti risorse di archiviazione:  
  
- Contenitori BLOB  
- Condivisioni file  
- Code  
- Tabelle  
  
> [!NOTE]
> I criteri di accesso archiviati in un contenitore possono essere associati a una firma di accesso condiviso che concede autorizzazioni al contenitore stesso o ai BLOB in esso contenuti. Analogamente, un criterio di accesso archiviato in una condivisione file può essere associato a una firma di accesso condiviso che concede autorizzazioni alla condivisione stessa o ai file in essa contenuti.  
>
> I criteri di accesso archiviati sono supportati solo per una gestione delle impostazioni di accesso della rete server. I criteri di accesso archiviati non sono supportati per la sAS dell'account o la configurazione di accesso livello di accesso utente.  

## <a name="create-a-stored-access-policy"></a>Creare criteri di accesso archiviati

Il codice seguente crea un criterio di accesso archiviato su un contenitore. È possibile usare i criteri di accesso per specificare i vincoli per una firma di accesso condiviso del servizio sul contenitore o i relativi BLOB.

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Vedere anche

- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md)
- [Definire criteri di accesso archiviati](/rest/api/storageservices/define-stored-access-policy)

