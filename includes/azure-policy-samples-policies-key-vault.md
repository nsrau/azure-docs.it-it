---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170129"
---
|Nome |Descrizione |Effetto/i |Versione |
|---|---|---|---|
|[Distribuisci le impostazioni di diagnostica per Key Vault nell'hub eventi](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Distribuisce le impostazioni di diagnostica per Key Vault per lo streaming in un hub eventi a livello di area quando viene creato o aggiornato un Key Vault in cui manca questa impostazione di diagnostica. |deployIfNotExists |1.0.0 |
|[I log di diagnostica in Key Vault devono essere abilitati](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |1.0.0 |
|[Gli oggetti Key Vault devono essere recuperabili](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Questo criterio controlla se gli oggetti insieme di credenziali delle chiavi non sono recuperabili. La funzionalità di eliminazione temporanea permette di conservare in modo efficace le risorse per un periodo di conservazione specificato (90 giorni) anche dopo un'operazione di eliminazione, pur facendo sembrare che l'oggetto sia stato eliminato. Quando la funzionalità di protezione dall'eliminazione è attivata, non è possibile ripulire un insieme di credenziali o un oggetto in stato eliminato fino al termine del periodo di conservazione di 90 giorni. Questi insiemi di credenziali e oggetti possono ancora essere recuperati, assicurando ai clienti il rispetto dei criteri di conservazione. |Audit, Disabled |1.0.0 |
