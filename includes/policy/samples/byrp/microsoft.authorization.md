---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 51d6a18a39c6c82c5c923d8da96bfab155022cce
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659638"
---
|Nome |Descrizione |Effetto/i |Versione |GitHub |
|---|---|---|---|---|
|[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Controlla i ruoli predefiniti, ad esempio "Proprietario, Collaboratore, Lettore" invece che i ruoli Controllo degli accessi in base al ruolo personalizzati, che sono soggetti a errori. L'uso di ruoli personalizzati è considerato un'eccezione e richiede una revisione rigorosa e la modellazione delle minacce |Audit, Disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Questo criterio garantisce che non esista alcun ruolo di proprietario della sottoscrizione personalizzato. |Audit, Disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
