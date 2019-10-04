---
title: Definire un criterio di accesso archiviato con .NET-archiviazione di Azure
description: Informazioni su come definire un criterio di accesso archiviato usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990741"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definire un criterio di accesso archiviato con .NET

Un criterio di accesso archiviato offre un livello di controllo aggiuntivo sulle firme di accesso condiviso a livello di servizio (SAS) sul lato server. La definizione di un criterio di accesso archiviato serve per raggruppare le firme di accesso condiviso e per fornire restrizioni aggiuntive per le firme di accesso condiviso associate ai criteri. È possibile usare un criterio di accesso archiviato per modificare l'ora di inizio, l'ora di scadenza o le autorizzazioni per una firma di accesso condiviso oppure per revocarla dopo che è stata rilasciata.
  
 Le risorse di archiviazione seguenti supportano i criteri di accesso archiviati:  
  
- Contenitori BLOB  
- Condivisioni file  
- Code  
- Tabelle  
  
> [!NOTE]
> Un criterio di accesso archiviato in un contenitore può essere associato a una firma di accesso condiviso che concede le autorizzazioni al contenitore stesso o ai BLOB in esso contenuti. Analogamente, un criterio di accesso archiviato in una condivisione file può essere associato a una firma di accesso condiviso che concede le autorizzazioni per la condivisione stessa o per i file in esso contenuti.  
>
> I criteri di accesso archiviati sono supportati solo per la firma di accesso condiviso del servizio. I criteri di accesso archiviati non sono supportati per la firma di accesso condiviso dell'account o della delega utente.  

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

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](storage-sas-overview.md)
- [Definire un criterio di accesso archiviato](/rest/api/storageservices/define-stored-access-policy)

