---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: a1b12a72434034ecc6cbe527cc3de6454e4293a0
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381837"
---
|Nome |Descrizione |Effetto/i |Versione |GitHub |
|---|---|---|---|---|
|[I registri Container devono essere crittografati con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controlla i registri Container per cui non è abilitata la crittografia con chiavi gestite dal cliente. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere: https://aka.ms/acr/CMK. |Audit, Disabled |1.0.0-preview |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[I registri Container non devono consentire l'accesso alla rete senza restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controlla i registri Container per cui non sono configurate regole di rete (IP o rete virtuale) e che consentono l'accesso a tutte le reti per impostazione predefinita. I registri Container con almeno una regola IP/firewall o una rete virtuale configurata saranno considerati conformi. Per altre informazioni sulle regole di rete del registro Container, vedere: https://aka.ms/acr/vnet. |Audit, Disabled |1.0.0-preview |[Collegamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
