---
title: Controllo degli accessi in base al ruolo per gli account di Servizi multimediali - Azure Documenti Microsoft
description: Questo articolo illustra il controllo degli accessi in base al ruolo per gli account di Servizi multimediali di Azure.This article discusses role-based access control (RBAC) for Azure Media Services accounts.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236909"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controllo degli accessi in base al ruolo per gli account di Servizi multimedialiRole-based access control (RBAC) for Media Services accounts

Attualmente, Servizi multimediali di Azure non definisce alcun ruolo personalizzato specifico per il servizio. Per ottenere l'accesso completo all'account Servizi multimediali, i clienti possono utilizzare i ruoli predefiniti **di Proprietario** o **Collaboratore**. La differenza principale tra questi ruoli è: il **proprietario** può controllare chi ha accesso a una risorsa e il **Collaboratore** non può. È inoltre possibile utilizzare il ruolo **Lettore** incorporato, ma l'utente o l'applicazione disterrà solo l'accesso in lettura alle API di Servizi multimediali. 

## <a name="design-principles"></a>Principi di progettazione

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. Le API v3 non restituiscono segreti o credenziali nelle operazioni **Get** o **List.** Le chiavi sono sempre Null, vuote o purificate dalla risposta. L'utente deve chiamare un metodo di azione separato per ottenere segreti o credenziali. Il ruolo **Reader** non può chiamare operazioni come Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. La disponibilità di azioni separate consente di impostare autorizzazioni di sicurezza RBAC più granulari in un ruolo personalizzato, se lo si desidera.

Per elencare le operazioni supportate da Servizi multimediali, eseguire le operazioni supportate da Servizi multimediali:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

L'articolo [sulle definizioni di ruolo predefinite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) indica esattamente cosa concede il ruolo. 

Per altre informazioni, vedere gli articoli seguenti:

- [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Che cos'è IL controllo degli accessi in base al ruolo per le risorse di Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Usare il controllo degli accessi in base al ruolo per gestire l'accessoUse RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operazioni del provider di risorse di Servizi multimediali](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo con le API di Servizi multimediali v3Developing with Media Services v3 APIs](media-services-apis-overview.md)
- [Ottenere i criteri della chiave simmetrica con Servizi multimediali .NETGet content key policy using Media Services .NET](get-content-key-policy-dotnet-howto.md)
