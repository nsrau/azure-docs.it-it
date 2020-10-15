---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711411"
---
| Proprietà | Descrizione |
|:--- |:---|
|**identity / type** | Tipo di autenticazione usato per effettuare la richiesta. Ad esempio: `OAuth`, `SAS Key`, `Account Key` o `Anonymous` |
|**identity / tokenHash**|Questo campo è solo per uso interno. |
|**authorization / action** | Azione assegnata alla richiesta. |
|**authorization / roleAssignmentId** | ID assegnazione di ruolo. Ad esempio: `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | ID definizione del ruolo. Ad esempio: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | ID dell'entità di sicurezza. Ad esempio: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | Tipo dell'entità di sicurezza. Ad esempio: `ServicePrincipal`. |
|**requester / appID** | ID applicazione OAuth (Open Authorization) usato come richiedente. <br> Ad esempio: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | Destinatari OAuth della richiesta. Ad esempio: `https://storage.azure.com`. |
|**requester / objectId** | ID oggetto OAuth del richiedente. In caso di autenticazione Kerberos, rappresenta l'identificatore di oggetto dell'utente autenticato Kerberos. Ad esempio: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | ID tenant OAuth dell'identità. Ad esempio: `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | Autorità emittente di token OAuth. Ad esempio: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | Nome dell'entità utente (UPN) del richiedente. Ad esempio: `someone@contoso.com`. |
|**requester / userName** | Questo campo è solo per uso interno.|