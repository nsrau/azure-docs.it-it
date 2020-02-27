---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: 3569f570653207404d6d65e3ed3eab6bb12729f5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495321"
---
|Nome |Descrizione |Effetto/i |Versione |Source (Sorgente) |
|---|---|---|---|
|[I registri Container devono essere crittografati con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controlla i registri Container per cui non è abilitata la crittografia con chiavi gestite dal cliente. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere: https://aka.ms/acr/CMK. |Audit, Disabled |1.0.0-preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[I registri Container non devono consentire l'accesso alla rete senza restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controlla i registri Container per cui non sono configurate regole di rete (IP o rete virtuale) e che consentono l'accesso a tutte le reti per impostazione predefinita. I registri Container con almeno una regola IP/firewall o una rete virtuale configurata saranno considerati conformi. Per altre informazioni sulle regole di rete del registro Container, vedere: https://aka.ms/acr/vnet. |Audit, Disabled |1.0.0-preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
