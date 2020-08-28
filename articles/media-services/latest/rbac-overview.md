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
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 03e6c54ae9931f8f209a5f59150db2801827da06
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003764"
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

L'articolo [definizioni di ruolo predefinite](../../role-based-access-control/built-in-roles.md) indica esattamente ciò che viene concesso dal ruolo. 

Per altre informazioni, vedere gli articoli seguenti:

- [Ruoli di amministratore della sottoscrizione classica, ruoli di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../../role-based-access-control/overview.md)
- [Usa RBAC per gestire l'accesso](../../role-based-access-control/role-assignments-rest.md)
- [Operazioni del provider di risorse di servizi multimediali](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md)
- [Ottenere i criteri della chiave simmetrica usando Media Services .NET](get-content-key-policy-dotnet-howto.md)
