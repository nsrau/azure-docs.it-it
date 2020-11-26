---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296089"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le aree di lavoro di Azure sinapsi devono usare chiavi gestite dal cliente per crittografare i dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Usare chiavi gestite dal cliente per controllare la crittografia dei dati archiviati nelle aree di lavoro di sinapsi di Azure. Le chiavi gestite dal cliente forniscono la crittografia doppia mediante l'aggiunta di un secondo livello di crittografia sulla crittografia predefinita con chiavi gestite dal servizio. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[È necessario rimuovere le regole del firewall IP nelle aree di lavoro di Azure sinapsi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |La rimozione di tutte le regole del firewall IP migliora la sicurezza assicurando che l'area di lavoro di Azure sinapsi possa essere accessibile solo da un endpoint privato. Questa configurazione controlla la creazione di regole del firewall che consentono l'accesso alla rete pubblica nell'area di lavoro. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[È necessario abilitare la rete virtuale dell'area di lavoro gestita nelle aree di lavoro di sinapsi di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |L'abilitazione di una rete virtuale dell'area di lavoro gestita garantisce che l'area di lavoro sia isolata da altre aree di lavoro. L'integrazione dei dati e le risorse Spark distribuite in questa rete virtuale forniscono anche l'isolamento a livello di utente per le attività Spark. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[È necessario abilitare le connessioni degli endpoint privati nelle aree di lavoro di sinapsi di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Gli endpoint privati possono essere configurati per la connessione privata a un'area di lavoro di sinapsi di Azure. Viene usato per applicare un canale di comunicazione sicuro all'area di lavoro sinapsi di Azure. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Gli endpoint privati gestiti da sinapsi devono connettersi solo a risorse in tenant Azure Active Directory approvati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Proteggere l'area di lavoro sinapsi consentendo solo le connessioni alle risorse in tenant Azure Active Directory (Azure AD) approvati. I tenant Azure AD approvati possono essere definiti durante l'assegnazione dei criteri. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
