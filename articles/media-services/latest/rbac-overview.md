---
title: Controllo di accesso basato sui ruoli per gli account di servizi multimediali - Azure | Microsoft Docs
description: Questo articolo illustra il controllo di accesso basato sui ruoli (RBAC) per gli account di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471894"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controllo di accesso basato sui ruoli (RBAC) per gli account di servizi multimediali

Attualmente, servizi multimediali di Azure non è definito uno specifico di ruoli personalizzati per il servizio. I clienti possono usare i ruoli predefiniti del **Owner** oppure **collaboratore** per ottenere accesso completo a un account servizi multimediali. È la differenza principale tra questi ruoli: il **proprietario** possono controllare chi può accedere a una risorsa e il **collaboratore** non è possibile. L'account lettore predefinito ha solo accesso in lettura all'account di servizi multimediali. 

## <a name="design-principles"></a>Principi di progettazione

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. API v3 non restituiscono i segreti o le credenziali sul **ottenere** oppure **elenco** operazioni. Le chiavi sono sempre Null, vuote o purificate dalla risposta. L'utente deve chiamare un metodo di azione separata per ottenere i segreti o le credenziali. Il **lettore** ruolo non è possibile chiamare le operazioni in modo che non è possibile chiamare operazioni come ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Con azioni separate consente di impostare le autorizzazioni di sicurezza più granulari RBAC in un ruolo personalizzato, se lo si desidera.

Per visualizzare un elenco di servizi multimediali supporta le operazioni, eseguire:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Il [le definizioni di ruolo predefinite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) articolo indica esattamente ciò che concede il ruolo. 

Vedere gli articoli seguenti per altre informazioni:

- [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Che cos'è RBAC per risorse di Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Usare RBAC per gestire l'accesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Ottenere i criteri di chiave simmetrica mediante .NET di servizi multimediali](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>Passaggi successivi

[Sviluppo con servizi multimediali v3 API](media-services-apis-overview.md)
