---
title: Governare l'accesso per gli utenti esterni in Azure AD gestione dei diritti-Azure Active Directory
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9107471448a58dc7866fb2cd6052abf168437d2b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174171"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Governare l'accesso per gli utenti esterni in Azure AD gestione dei diritti

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

1. Si [aggiunge un'organizzazione connessa](entitlement-management-organization.md) per la directory Azure ad o il dominio con cui si vuole collaborare.

1. Si crea un pacchetto di accesso nella directory che include un criterio [per gli utenti non inclusi nella directory](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Si invia un [collegamento del portale di accesso personale](entitlement-management-access-package-settings.md) al contatto dell'organizzazione esterna che può condividere con gli utenti per richiedere il pacchetto di accesso.

1. Un utente esterno (**richiedente** in questo esempio) usa il collegamento portale di accesso personale per [richiedere l'accesso](entitlement-management-request-access.md) al pacchetto di accesso. La modalità di accesso dell'utente dipende dal tipo di autenticazione della directory o del dominio definito nell'organizzazione connessa.

1. Un responsabile approvazione [approva la richiesta](entitlement-management-request-approve.md) (oppure la richiesta viene approvata automaticamente).

1. La richiesta passa allo [stato](entitlement-management-process.md)recapito.

1. Utilizzando il processo di invito B2B, viene creato un account utente guest nella directory (**richiedente a (Guest)** in questo esempio). Se viene definito un elenco di indirizzi [consentiti o negati](../b2b/allow-deny-list.md) , verrà applicata l'impostazione dell'elenco.

1. All'utente guest viene assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Il tempo necessario per apportare modifiche in Azure AD e ad altri Microsoft Online Services o applicazioni SaaS connesse potrebbe richiedere del tempo. Per ulteriori informazioni, vedere [quando vengono applicate le modifiche](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. L'utente esterno riceve un messaggio di posta elettronica che indica che l'accesso è stato [recapitato](entitlement-management-process.md).

1. Per accedere alle risorse, l'utente esterno può fare clic sul collegamento nel messaggio di posta elettronica o tentare di accedere direttamente a una delle risorse della directory per completare il processo di invito.

1. A seconda delle impostazioni dei criteri, quando passa il tempo, l'assegnazione del pacchetto di accesso per l'utente esterno scade e viene rimosso l'accesso dell'utente esterno.

1. A seconda del ciclo di vita delle impostazioni degli utenti esterni, quando l'utente esterno non dispone più di assegnazioni di pacchetti di accesso, l'utente esterno viene bloccato dall'accesso e l'account utente guest viene rimosso dalla directory.

## <a name="settings-for-external-users"></a>Impostazioni per gli utenti esterni

Per assicurarsi che gli utenti esterni all'organizzazione possano richiedere pacchetti di accesso e ottenere l'accesso alle risorse in questi pacchetti di accesso, è necessario verificare che alcune impostazioni siano configurate correttamente.

### <a name="enable-catalog-for-external-users"></a>Abilita catalogo per utenti esterni

- Per impostazione predefinita, quando si crea un [nuovo catalogo](entitlement-management-catalog-create.md), questo viene abilitato per consentire agli utenti esterni di richiedere i pacchetti di accesso nel catalogo. Verificare che **abilitato per utenti esterni** sia impostato su **Sì**.

    ![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna Azure AD B2B

- Consentire ai guest di invitare altri guest nella propria directory significa che gli inviti Guest possono verificarsi al di fuori della gestione dei diritti. Si consiglia di impostare i **Guest come invitati** a **No** per consentire solo gli inviti regolati correttamente.
- Se si usa l'elenco Consenti B2B, è necessario assicurarsi che tutti i domini con cui si vuole collaborare con la gestione dei diritti siano aggiunti all'elenco. In alternativa, se si usa l'elenco di negazione B2B, è necessario assicurarsi che tutti i domini con cui si vuole collaborare non vengano aggiunti all'elenco.
- Se si creano criteri di gestione dei diritti per **tutti gli utenti** (tutte le organizzazioni connesse e tutti i nuovi utenti esterni), tutte le impostazioni di Consenti o nega elenco B2B avranno la precedenza. Quindi, assicurarsi di includere i domini che si intende includere in questo criterio nell'elenco Consenti se ne viene usato uno ed escluderli dall'elenco di negazione se si usa un elenco di accesso negato.
- Se si vuole creare un criterio di gestione dei diritti che includa **tutti gli utenti** (tutte le organizzazioni connesse e tutti i nuovi utenti esterni), è necessario prima di tutto abilitare la posta elettronica per la password una sola volta per la directory. Per ulteriori informazioni, vedere la pagina relativa all' [autenticazione del codice di posta elettronica monouso (anteprima)](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Per altre informazioni su Azure AD impostazioni di collaborazione esterna B2B, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](../b2b/delegate-invitations.md).

    ![Azure AD impostazioni di collaborazione esterna](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Esaminare i criteri di accesso condizionale

- Assicurarsi di escludere i Guest dai criteri di accesso condizionale che i nuovi utenti guest non saranno in grado di soddisfare poiché in questo modo non saranno in grado di accedere alla directory. È possibile, ad esempio, che Guest non disponga di un dispositivo registrato, che non si trovi in una posizione nota e che non si desideri ripetere la registrazione per multi-factor authentication, quindi l'aggiunta di questi requisiti in un criterio di accesso condizionale impedisce ai guest di usare il diritto gestione. Per altre informazioni, vedere [quali sono le condizioni in Azure Active Directory l'accesso condizionale?](../conditional-access/conditions.md).

    ![Azure AD impostazioni di esclusione dei criteri di accesso condizionale](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Esaminare le impostazioni di condivisione esterna di SharePoint Online

- Se si desidera includere i siti di SharePoint Online nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'impostazione di condivisione esterna a livello di organizzazione sia impostata su **chiunque** (gli utenti non necessitano dell'accesso) o su **Guest nuovi ed esistenti** (i Guest devono firmare in o fornire un codice di verifica). Per altre informazioni, vedere [attivare o disattivare la condivisione esterna](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Se si vuole limitare la condivisione esterna al di fuori della gestione dei diritti, è possibile impostare l'impostazione di condivisione esterna su **Guest esistenti**. Quindi, solo i nuovi utenti invitati tramite la gestione dei diritti saranno in grado di accedere a questi siti. Per altre informazioni, vedere [attivare o disattivare la condivisione esterna](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Assicurarsi che le impostazioni a livello di sito consentano l'accesso Guest (le stesse opzioni selezionate come indicato in precedenza). Per ulteriori informazioni, vedere [attivare o disattivare la condivisione esterna per un sito](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Esaminare le impostazioni di condivisione del gruppo Office 365

- Se si desidera includere i gruppi di Office 365 nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'impostazione Consenti **agli utenti di aggiungere nuovi Guest all'organizzazione** sia impostata **su on** per consentire l'accesso guest. Per ulteriori informazioni, vedere [Manage Guest Access to Office 365 groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-guest-access-to-office-365-groups).

- Se si vuole che gli utenti esterni siano in grado di accedere al sito di SharePoint Online e alle risorse associate a un gruppo di Office 365, assicurarsi di attivare la condivisione esterna di SharePoint Online. Per altre informazioni, vedere [attivare o disattivare la condivisione esterna](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Per informazioni su come impostare i criteri Guest per i gruppi di Office 365 a livello di directory in PowerShell, vedere [esempio: configurare i criteri Guest per i gruppi a livello di directory](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Esaminare le impostazioni di condivisione dei team

- Se si desidera includere i team nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'accesso **consentito ai guest in Microsoft teams** sia impostato **su on** per consentire l'accesso guest. Per altre informazioni, vedere [configurare l'accesso guest nell'interfaccia di amministrazione di Microsoft teams](https://docs.microsoft.com/microsoftteams/set-up-guests#configure-guest-access-in-the-microsoft-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Gestire il ciclo di vita degli utenti esterni

È possibile selezionare cosa accade quando un utente esterno, che è stato invitato alla directory tramite una richiesta di pacchetto di accesso approvata, non ha più assegnazioni di pacchetti di accesso. Questo problema può verificarsi se l'utente cede tutte le assegnazioni dei pacchetti di accesso o la scadenza dell'ultima assegnazione del pacchetto di accesso. Per impostazione predefinita, quando un utente esterno non dispone più di assegnazioni di pacchetti di accesso, l'accesso alla directory viene bloccato. Dopo 30 giorni, l'account utente guest viene rimosso dalla directory.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **gestione dei diritti** , fare clic su **Impostazioni**.

1. Fare clic su **Modifica**.

    ![Impostazioni per gestire il ciclo di vita degli utenti esterni](./media/entitlement-management-external-users/settings-external-users.png)

1. Nella sezione **Gestisci il ciclo di vita degli utenti esterni** selezionare le impostazioni diverse per gli utenti esterni.

1. Dopo che un utente esterno ha perso l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera impedire l'accesso a questa directory, impostare il **blocco dell'utente esterno dall'accesso a questa directory** su **Sì**.

    > [!NOTE]
    > Se a un utente viene impedito l'accesso a questa directory, l'utente non sarà in grado di ririchiedere il pacchetto di accesso o di richiedere ulteriore accesso in questa directory. Non configurare il blocco dell'accesso se successivamente dovranno richiedere l'accesso ad altri pacchetti di accesso.

1. Quando un utente esterno perde l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera rimuovere l'account utente guest in questa directory, impostare **Rimuovi utente esterno** su **Sì**.

    > [!NOTE]
    > La gestione dei diritti rimuove solo gli account che sono stati invitati tramite la gestione dei diritti. Si noti inoltre che a un utente verrà impedito l'accesso e la rimozione da questa directory anche se tale utente è stato aggiunto alle risorse di questa directory che non erano in grado di accedere alle assegnazioni dei pacchetti. Se il Guest era presente in questa directory prima di ricevere le assegnazioni dei pacchetti di accesso, rimarranno. Tuttavia, se il Guest è stato invitato attraverso un'assegnazione del pacchetto di accesso e dopo essere stato invitato è stato assegnato anche a un sito di OneDrive for business o SharePoint Online, questi verranno comunque rimossi.

1. Se si desidera rimuovere l'account utente guest in questa directory, è possibile impostare il numero di giorni prima della rimozione. Se si desidera rimuovere l'account utente Guest non appena si perde l'ultima assegnazione a tutti i pacchetti di accesso, impostare il **numero di giorni prima di rimuovere l'utente esterno da questa directory** a **0**.

1. Fare clic su **Salva**

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere un'organizzazione connessa](entitlement-management-organization.md)
- [Per gli utenti che non si trovino nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Risoluzione dei problemi](entitlement-management-troubleshoot.md)
