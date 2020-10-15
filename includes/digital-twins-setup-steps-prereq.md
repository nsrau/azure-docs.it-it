---
author: baanders
description: Panoramica del file di inclusione e delle autorizzazioni nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009656"
---
>[!NOTE]
>Queste operazioni devono essere completate da un utente con la possibilità di gestire sia le risorse che l'accesso degli utenti nella sottoscrizione di Azure. Sebbene alcuni passaggi possano essere completati con autorizzazioni inferiori, sarà necessario collaborare con tali autorizzazioni per configurare completamente un'istanza utilizzabile. Per altre informazioni, vedere la sezione [*prerequisiti: autorizzazioni necessarie*](#prerequisites-permission-requirements) di seguito.

La configurazione completa per una nuova istanza di Azure Digital Twins è costituita da tre parti:
1. **Creazione dell'istanza**
2. **Impostazione delle autorizzazioni di accesso utente**: gli utenti di Azure devono avere il ruolo *proprietario (anteprima) di Azure Digital gemelli* nell'istanza di Azure Digital Twins per poterlo gestire e i relativi dati. In questo passaggio, come proprietario/amministratore della sottoscrizione di Azure, questo ruolo verrà assegnato alla persona che gestirà l'istanza di Azure Digital gemelli. Potrebbe trattarsi di un utente o di un altro utente nell'organizzazione.
3. **Impostazione delle autorizzazioni di accesso per le applicazioni client**: è normale scrivere un'applicazione client che verrà usata per interagire con l'istanza. Per consentire all'app client di accedere ai dispositivi gemelli digitali di Azure, è necessario configurare una *registrazione dell'app* in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) che l'applicazione client utilizzerà per l'autenticazione all'istanza.

Per continuare, sarà necessaria una sottoscrizione di Azure. È possibile configurarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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