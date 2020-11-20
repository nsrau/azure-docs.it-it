---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002164"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I dashboard condivisi non devono avere riquadri Markdown con contenuto inline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Non consente la creazione di un dashboard condiviso con contenuto inline nei riquadri Markdown e impone che il contenuto venga archiviato come file Markdown ospitato online. Se si usa il contenuto inline nel riquadro Markdown, non è possibile gestire la crittografia del contenuto. Configurando il proprio spazio di archiviazione, è possibile crittografare, crittografare in modo doppio e persino importare chiavi personalizzate. L'abilitazione di questo criterio consente agli utenti di usare 2020-09-01-Preview o versione successiva dell'API REST dei dashboard condivisi. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
