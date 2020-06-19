---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0ba2284775d97c51db52b474c4c42ddaab29c514
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709417"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I registri Container devono essere crittografati con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controlla i registri Container per cui non è abilitata la crittografia con chiavi gestite dal cliente. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[I registri Container non devono consentire l'accesso alla rete senza restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controlla i registri Container per cui non sono configurate regole di rete (IP o rete virtuale) e che consentono l'accesso a tutte le reti per impostazione predefinita. I registri Container con almeno una regola IP/firewall o una rete virtuale configurata saranno considerati conformi. Per altre informazioni sulle regole di rete del registro Container, vedere: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[I registri contenitori devono usare collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Registri Container di controllo che non includono almeno una connessione a endpoint privato approvata. I client in una rete virtuale possono accedere in modo sicuro alle risorse con connessioni a endpoint privati tramite collegamenti privati. Per altre informazioni, vedere [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
