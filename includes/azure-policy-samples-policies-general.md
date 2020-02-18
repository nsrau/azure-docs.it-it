---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169879"
---
|Nome |Descrizione |Effetto/i |Versione |
|---|---|---|---|
|[Località consentite](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Questi criteri consentono di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. Usare per imporre requisiti di conformità geografica. Esclude i gruppi di risorse, Microsoft.AzureActiveDirectory/b2cDirectories e le risorse che usano l'area "global". |deny |1.0.0 |
|[Località consentite per i gruppi di risorse](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Questi criteri consentono di limitare le località in cui l'organizzazione può creare gruppi di risorse. Usare per imporre requisiti di conformità geografica. |deny |1.0.0 |
|[Tipi di risorse consentiti](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Questo criterio consente di specificare i tipi di risorse che l'organizzazione può distribuire. Solo i tipi di risorse che supportano i 'tag' e la 'località' saranno interessati dal criterio. Per includere tutte le risorse, duplicare il criterio e modificare la 'modalità' impostandola su 'Tutte'. |deny |1.0.0 |
|[Controlla che la località della risorsa corrisponda alla località del gruppo di risorse](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Controlla che la località della risorsa corrisponda alla località del gruppo di risorse |controllo |1.0.0 |
|[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Controlla i ruoli predefiniti, ad esempio "Proprietario, Collaboratore, Lettore" invece che i ruoli Controllo degli accessi in base al ruolo personalizzati, che sono soggetti a errori. L'uso di ruoli personalizzati è considerato un'eccezione e richiede una revisione rigorosa e la modellazione delle minacce |Audit, Disabled |1.0.0 |
|[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Questo criterio garantisce che non esista alcun ruolo di proprietario della sottoscrizione personalizzato. |Audit, Disabled |1.0.0 |
|[Tipi di risorse non consentiti](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Questo criterio consente di specificare i tipi di risorse che non possono essere distribuiti dall'organizzazione. |Nega |1.0.0 |
