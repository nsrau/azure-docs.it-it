---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: b3647f02274bdf0efa4cd8079c55be330f28abc9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170279"
---
|Nome |Descrizione |Effetto/i |Versione |
|---|---|---|---|
|[È consigliabile rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi del bus di servizio](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |I client del bus di servizio non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per essere conformi al modello di sicurezza basato su privilegi minimi, è consigliabile creare criteri di accesso a livello di entità per code e argomenti in modo da fornire l'accesso solo all'entità specifica |Audit, Deny, Disabled |1.0.1 |
|[È consigliabile abilitare i log di diagnostica nel bus di servizio](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |2.0.0 |
