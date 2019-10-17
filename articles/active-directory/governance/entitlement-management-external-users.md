---
title: Governare l'accesso per gli utenti esterni in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni sulle impostazioni che è possibile specificare per gestire l'accesso per gli utenti esterni nella gestione dei diritti Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcf4a0272e21a1fba3cf9adbd9158492e4318578
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452991"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Governare l'accesso per gli utenti esterni in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure AD gestione dei diritti utilizza [Azure ad B2B (business to business)](../b2b/what-is-b2b.md) per collaborare con persone esterne all'organizzazione in un'altra directory. Con Azure AD B2B, gli utenti esterni eseguono l'autenticazione alla propria home directory, ma hanno una rappresentazione nella directory. La rappresentazione nella directory consente all'utente di assegnare l'accesso alle risorse.

Questo articolo descrive le impostazioni che è possibile specificare per gestire l'accesso per gli utenti esterni.

## <a name="how-entitlement-management-can-help"></a>Come può essere utile la gestione dei diritti

Quando si usa l'esperienza di invito [B2B di Azure ad](../b2b/what-is-b2b.md) , è necessario conoscere già gli indirizzi di posta elettronica degli utenti Guest esterni da inserire nella directory delle risorse e usare. Si tratta di un'ottima soluzione quando si lavora su un progetto più piccolo o a breve termine e si conoscono già tutti i partecipanti, ma ciò è più difficile da gestire se si dispone di molti utenti con cui si desidera lavorare o se i partecipanti cambiano nel tempo.  È possibile, ad esempio, che si stia lavorando a un'altra organizzazione e che sia presente un punto di contatto con l'organizzazione, ma nel tempo saranno necessari anche altri utenti dell'organizzazione.

Con la gestione dei diritti è possibile definire un criterio che consenta agli utenti delle organizzazioni di specificare di richiedere autonomamente un pacchetto di accesso. È possibile specificare se l'approvazione è obbligatoria e una data di scadenza per l'accesso. Se è richiesta l'approvazione, è anche possibile invitare uno o più utenti dell'organizzazione esterna alla directory e designarli come responsabili approvazione, dal momento che è probabile che gli utenti esterni dell'organizzazione debbano accedere. Dopo aver configurato il pacchetto di accesso, è possibile inviare il collegamento del pacchetto di accesso al personale del contatto (sponsor) presso l'organizzazione esterna. Tale contatto può essere condiviso con altri utenti nell'organizzazione esterna e può utilizzare questo collegamento per richiedere il pacchetto di accesso. Anche gli utenti dell'organizzazione che sono già stati invitati alla directory possono usare tale collegamento.

Quando una richiesta viene approvata, la gestione dei diritti effettuerà il provisioning dell'utente con l'accesso necessario, che può includere l'invito dell'utente se non è già presente nella directory. Azure AD creerà automaticamente un account Guest B2B. Si noti che un amministratore potrebbe avere limitato in precedenza le organizzazioni a cui è consentita la collaborazione, impostando un [elenco Consenti o nega B2B](../b2b/allow-deny-list.md) per consentire o bloccare gli inviti ad altre organizzazioni.  Se l'utente non è consentito dall'elenco Consenti o blocca, non verrà invitato.

Poiché non si desidera che l'accesso dell'utente esterno venga utilizzato per l'ultima volta, è necessario specificare una data di scadenza nel criterio, ad esempio 180 giorni. Dopo 180 giorni, se l'accesso non viene esteso, la gestione dei diritti eliminerà tutti gli accessi associati al pacchetto di accesso. Per impostazione predefinita, se l'utente invitato attraverso la gestione dei diritti non dispone di altre assegnazioni di pacchetti di accesso, quando perde l'ultima assegnazione, l'account Guest verrà bloccato per 30 giorni e successivamente rimosso. In questo modo si impedisce la proliferazione di account non necessari. Come descritto nelle sezioni seguenti, queste impostazioni sono configurabili.

## <a name="how-access-works-for-external-users"></a>Funzionamento dell'accesso per gli utenti esterni

Nel diagramma e nei passaggi seguenti viene fornita una panoramica del modo in cui agli utenti esterni viene concesso l'accesso a un pacchetto di accesso.

![Diagramma che mostra il ciclo di vita degli utenti esterni](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Si crea un pacchetto di accesso nella directory che include un criterio [per gli utenti non inclusi nella directory](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Si invia un [collegamento del portale di accesso personale](entitlement-management-access-package-settings.md) al contatto dell'organizzazione esterna che può condividere con gli utenti per richiedere il pacchetto di accesso.

1. Un utente esterno (**richiedente** in questo esempio) usa il collegamento portale di accesso personale per [richiedere l'accesso](entitlement-management-request-access.md) al pacchetto di accesso.

1. Un responsabile approvazione [approva la richiesta](entitlement-management-request-approve.md) (oppure la richiesta viene approvata automaticamente).

1. La richiesta passa allo [stato](entitlement-management-process.md)recapito.

1. Utilizzando il processo di invito B2B, viene creato un account utente guest nella directory (**richiedente a (Guest)** in questo esempio). Se viene definito un elenco di indirizzi [consentiti o negati](../b2b/allow-deny-list.md) , verrà applicata l'impostazione dell'elenco.

1. All'utente guest viene assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Il tempo necessario per apportare modifiche in Azure AD e ad altri Microsoft Online Services o applicazioni SaaS connesse potrebbe richiedere del tempo. Per ulteriori informazioni, vedere [quando vengono applicate le modifiche](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. L'utente esterno riceve un messaggio di posta elettronica che indica che l'accesso è stato [recapitato](entitlement-management-process.md).

1. Per accedere alle risorse, l'utente esterno può fare clic sul collegamento nel messaggio di posta elettronica o tentare di accedere direttamente a una delle risorse della directory per completare il processo di invito.

1. A seconda delle impostazioni dei criteri, quando passa il tempo, l'assegnazione del pacchetto di accesso per l'utente esterno scade e viene rimosso l'accesso dell'utente esterno.

1. A seconda del ciclo di vita delle impostazioni degli utenti esterni, quando l'utente esterno non dispone più di assegnazioni di pacchetti di accesso, l'utente esterno viene bloccato dall'accesso e l'account utente guest viene rimosso dalla directory.

## <a name="manage-the-lifecycle-of-external-users"></a>Gestire il ciclo di vita degli utenti esterni

È possibile selezionare cosa accade quando un utente esterno, che è stato invitato alla directory tramite una richiesta di pacchetto di accesso approvata, non ha più assegnazioni di pacchetti di accesso. Questo problema può verificarsi se l'utente cede tutte le assegnazioni dei pacchetti di accesso o la scadenza dell'ultima assegnazione del pacchetto di accesso. Per impostazione predefinita, quando un utente esterno non dispone più di assegnazioni di pacchetti di accesso, l'accesso alla directory viene bloccato. Dopo 30 giorni, l'account utente guest viene rimosso dalla directory.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **gestione dei diritti** , fare clic su **Impostazioni**.

1. Fare clic su **Modifica**.

    ![Impostazioni per gestire il ciclo di vita degli utenti esterni](./media/entitlement-management-external-users/settings-external-users.png)

1. Nella sezione **Gestisci il ciclo di vita degli utenti esterni** selezionare le impostazioni diverse per gli utenti esterni.

1. Dopo che un utente esterno ha perso l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera impedire l'accesso a questa directory, impostare il **blocco dell'utente esterno dall'accesso a questa directory** su **Sì**.

1. Quando un utente esterno perde l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera rimuovere l'account utente guest nella directory, impostare **Rimuovi utente esterno** su **Sì**.

    > [!NOTE]
    > La gestione dei diritti rimuove solo gli account che sono stati invitati tramite la gestione dei diritti. Si noti inoltre che un utente verrà bloccato dall'accesso e rimosso dalla directory anche se tale utente è stato aggiunto alle risorse nella directory che non erano in grado di accedere alle assegnazioni dei pacchetti. Se il Guest è presente nella directory prima di ricevere le assegnazioni dei pacchetti di accesso, rimarrà. Tuttavia, se il Guest è stato invitato attraverso un'assegnazione del pacchetto di accesso e dopo essere stato invitato è stato assegnato anche a un sito di OneDrive for business o SharePoint Online, questi verranno comunque rimossi.

1. Se si desidera rimuovere l'account utente guest nella directory, è possibile impostare il numero di giorni prima della rimozione. Se si desidera rimuovere l'account utente Guest non appena si perde l'ultima assegnazione a tutti i pacchetti di accesso, impostare il **numero di giorni prima di rimuovere l'utente esterno da questa directory** a **0**.

1. Fare clic su **Salva**

## <a name="enable-a-catalog-for-external-users"></a>Abilitare un catalogo per utenti esterni

Quando si crea un [nuovo catalogo](entitlement-management-catalog-create.md), è disponibile un'impostazione che consente agli utenti di directory esterne di richiedere pacchetti di accesso nel catalogo. Se non si vuole che gli utenti esterni dispongano delle autorizzazioni per richiedere i pacchetti di accesso nel catalogo, impostare **abilitato per utenti esterni** su **No**.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

![Nuovo riquadro Catalogo](./media/entitlement-management-shared/new-catalog.png)

È anche possibile modificare questa impostazione dopo aver creato il catalogo.

![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Passaggi successivi

- [Per gli utenti che non si trovino nella directory](entitlement-management-access-package-create.md#for-users-not-in-your-directory)
- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
- [Delega e ruoli](entitlement-management-delegate.md)
