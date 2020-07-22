---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5aaf534e08b3184668c334c86b957dd872fd0dd9
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86509846"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I modelli di Image Builder per macchine virtuali devono usare un collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Controlla i modelli di Image Builder per macchine virtuali per i quali non è configurata una rete virtuale. Quando non è configurata una rete virtuale, viene creato e usato un indirizzo IP pubblico che può esporre le risorse direttamente a Internet e aumentare la superficie di attacco potenziale. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
