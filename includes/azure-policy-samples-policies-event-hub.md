---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 50b7b71883d924682a8af60ea365745cf6df863d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758191"
---
|Nome |Descrizione |Effetto/i |Versione |GitHub |
|---|---|---|---|---|
|[È consigliabile rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi di Hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |I client di Hub eventi non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per essere conformi al modello di sicurezza basato su privilegi minimi, è consigliabile creare criteri di accesso a livello di entità per code e argomenti in modo da fornire l'accesso solo all'entità specifica |Audit, Deny, Disabled |1.0.1 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[È consigliabile definire le regole di autorizzazione nell'istanza di Hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Controlla l'esistenza di regole di autorizzazione nelle entità di Hub eventi per concedere l'accesso con privilegi minimi |AuditIfNotExists, Disabled |1.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[È consigliabile abilitare i log di diagnostica in Hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |2.0.0 |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
