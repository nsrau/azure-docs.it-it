---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5b1e2f52366e800a3f12e304ed0e626b300e948c
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347248"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I registri contenitori devono essere crittografati con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controlla i registri contenitori per cui non è abilitata la crittografia con chiavi gestite dal cliente. Azure crittografa automaticamente il contenuto del registro inattivo con le chiavi gestite dal servizio. È possibile integrare la crittografia predefinita con un ulteriore livello di crittografia usando una chiave creata e gestita in Azure Key Vault. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[I registri contenitori non devono consentire l'accesso alla rete senza restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controlla i registri contenitori per cui non sono configurate regole di rete o firewall (IP) e che quindi consentono l'accesso a tutte le reti per impostazione predefinita. La restrizione dell'accesso alla rete protegge i registri contenitori da potenziali minacce. I registri contenitori con almeno una regola IP/firewall o una rete virtuale configurata sono considerati conformi. Per altre informazioni sulle regole di rete di Registro Container, vedere [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[I registri contenitori devono usare collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Controlla i registri contenitori che non includono almeno una connessione a endpoint privato approvata. I client in una rete virtuale possono accedere in modo sicuro alle risorse con connessioni a endpoint privati tramite collegamenti privati. È quindi possibile disabilitare l'accesso pubblico in modo che per la connessione al registro possano essere usati solo collegamenti privati. Per altre informazioni, vedere [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
