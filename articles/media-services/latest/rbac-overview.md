---
title: Controllo degli accessi in base al ruolo per gli account di servizi multimediali-Azure | Microsoft Docs
description: Questo articolo illustra il controllo degli accessi in base al ruolo (RBAC) per gli account di servizi multimediali di Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "66236909"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controllo degli accessi in base al ruolo (RBAC) per gli account di servizi multimediali

Attualmente, servizi multimediali di Azure non definisce ruoli personalizzati specifici del servizio. Per ottenere l'accesso completo all'account di servizi multimediali, i clienti possono usare i ruoli predefiniti di **proprietario** o **collaboratore**. La differenza principale tra questi ruoli è: il **proprietario** può controllare chi ha accesso a una risorsa e il **collaboratore** non può. Il ruolo **Reader** incorporato può essere usato anche se l'utente o l'applicazione avrà solo accesso in lettura alle API di servizi multimediali. 

## <a name="design-principles"></a>Principi di progettazione

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. le API V3 non restituiscono segreti o credenziali per le operazioni **Get** o **List** . Le chiavi sono sempre Null, vuote o purificate dalla risposta. L'utente deve chiamare un metodo di azione separato per ottenere segreti o credenziali. Il ruolo **Reader** non può chiamare operazioni come asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. La presenza di azioni separate consente di impostare autorizzazioni di sicurezza RBAC più granulari in un ruolo personalizzato, se lo si desidera.

Per elencare le operazioni supportate da servizi multimediali, procedere come segue:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

L'articolo [definizioni di ruolo predefinite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) indica esattamente ciò che viene concesso dal ruolo. 

Per altre informazioni, vedere gli articoli seguenti:

- [Ruoli di amministratore della sottoscrizione classica, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Che cos'è RBAC per le risorse di Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Usa RBAC per gestire l'accesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operazioni del provider di risorse di servizi multimediali](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md)
- [Ottenere i criteri della chiave simmetrica usando Media Services .NET](get-content-key-policy-dotnet-howto.md)
