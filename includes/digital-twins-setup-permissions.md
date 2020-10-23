---
author: baanders
description: file di inclusione per le autorizzazioni prerequisiti nell'installazione di dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205489"
---
## <a name="prerequisites-permission-requirements"></a>Prerequisiti: requisiti di autorizzazione

Per poter completare tutti i passaggi descritti in questo articolo, è necessario disporre di un [ruolo nella sottoscrizione](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) con le autorizzazioni seguenti:
* Creare e gestire le risorse di Azure
* Gestire l'accesso utente alle risorse di Azure (incluse le autorizzazioni di concessione e delega)

I ruoli comuni che soddisfano questo requisito sono *proprietario*, *amministratore account*o la combinazione di *amministratore accesso utenti* e *collaboratore*. Per una spiegazione completa dei ruoli e delle autorizzazioni, incluse le autorizzazioni incluse con altri ruoli, vedere [*ruoli di amministratore della sottoscrizione classica, ruoli di Azure e ruoli di Azure ad*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) nella documentazione relativa a RBAC di Azure.

Per visualizzare il ruolo nella sottoscrizione, visitare la [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nella portale di Azure (è possibile usare questo collegamento o cercare le *sottoscrizioni* con la barra di ricerca del portale). Cercare il nome della sottoscrizione in uso e visualizzarne il ruolo nella colonna *ruolo personale* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Visualizzazione della pagina sottoscrizioni nella portale di Azure, che mostra l'utente come proprietario" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Se si rileva che il valore è *collaboratore*o un altro ruolo che non ha le autorizzazioni necessarie descritte in precedenza, è possibile contattare l'utente nella sottoscrizione *che dispone di queste* autorizzazioni, ad esempio un proprietario della sottoscrizione o un amministratore dell'account, e procedere in uno dei modi seguenti:
* Richiedere il completamento dei passaggi descritti in questo articolo per conto dell'utente
* Richiedere l'elevazione del ruolo nella sottoscrizione, in modo che si disponga delle autorizzazioni per continuare. Il fatto che sia appropriato dipende dall'organizzazione e dal ruolo al suo interno.