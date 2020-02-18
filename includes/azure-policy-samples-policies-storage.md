---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170289"
---
|Nome |Descrizione |Effetto/i |Versione |
|---|---|---|---|
|[SKU degli account di archiviazione consentiti](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Questo criterio consente di specificare un set di SKU di account di archiviazione che possono essere distribuiti dall'organizzazione. |Nega |1.0.0 |
|[Controlla l'accesso di rete senza restrizioni agli account di archiviazione](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare invece regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire le connessioni da client Internet o locali specifici, è possibile concedere l'accesso al traffico da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici |Audit, Disabled |1.0.0 |
|[Distribuisci Advanced Threat Protection negli account di archiviazione](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Questo criterio abilita Advanced Threat Protection negli account di archiviazione. |DeployIfNotExists, Disabled |1.0.0 |
|[L'archiviazione con ridondanza geografica deve essere abilitata per gli account di archiviazione](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Questo criterio controlla tutti gli account di archiviazione in cui non è abilitata l'archiviazione con ridondanza geografica. |Audit, Disabled |1.0.0 |
|[È consigliabile abilitare il trasferimento sicuro agli account di archiviazione](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Controlla il requisito del trasferimento sicuro nell'account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione |Audit, Deny, Disabled |1.0.0 |
|[Gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Alcuni servizi Microsoft che interagiscono con gli account di archiviazione vengono eseguiti da reti alle quali non è possibile concedere l'accesso tramite le regole di rete. Per far sì che questo tipo di servizi funzioni come previsto, consentire al set di servizi Microsoft attendibili di ignorare le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione. |Audit, Deny, Disabled |1.0.0 |
|[È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |È possibile usare la nuova versione di Azure Resource Manager per gli account di archiviazione per fornire funzionalità di sicurezza migliorate, ad esempio controllo di accesso (controllo degli accessi in base al ruolo) più avanzato, controllo ottimizzato, distribuzione e governance basate su Azure Resource Manager, accesso alle identità gestite, accesso all'insieme di credenziali delle chiavi per i segreti, autenticazione basata su Azure AD e supporto di tag e gruppi di risorse per una gestione della sicurezza semplificata |Audit, Deny, Disabled |1.0.0 |
