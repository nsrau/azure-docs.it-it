---
title: Gestire l'accesso per gli utenti esterni nella gestione dei diritti di Azure AD - Azure Active Directory
description: Informazioni sulle impostazioni che è possibile specificare per gestire l'accesso per gli utenti esterni nella gestione dei diritti di Azure Active Directory.Learn about the settings you can specify to govern access for external users in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128688"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Gestire l'accesso per gli utenti esterni nella gestione dei diritti di Azure ADGovern access for external users in Azure AD entitlement management

La gestione dei diritti di Azure AD utilizza [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) per collaborare con persone esterne all'organizzazione in un'altra directory. Con Azure AD B2B, gli utenti esterni eseguono l'autenticazione nella home directory, ma hanno una rappresentazione nella directory. La rappresentazione nella directory consente all'utente di essere assegnato l'accesso alle risorse.

In questo articolo vengono descritte le impostazioni che è possibile specificare per gestire l'accesso per gli utenti esterni.

## <a name="how-entitlement-management-can-help"></a>In che modo la gestione dei diritti può essere d'aiuto

Quando si usa l'esperienza di invito [B2B di Azure AD,](../b2b/what-is-b2b.md) è necessario conoscere già gli indirizzi di posta elettronica degli utenti guest esterni che si vuole inserire nella directory delle risorse e utilizzare. Questo funziona alla grande quando si lavora su un progetto più piccolo o a breve termine e si conoscono già tutti i partecipanti, ma questo è più difficile da gestire se si dispone di un sacco di utenti con cui si desidera lavorare o se i partecipanti cambiano nel tempo.  Ad esempio, è possibile che si stia lavorando con un'altra organizzazione e che si disponga di un punto di contatto con tale organizzazione, ma nel tempo anche altri utenti di tale organizzazione avranno bisogno dell'accesso.

Con la gestione dei diritti, è possibile definire un criterio che consente agli utenti delle organizzazioni specificate di essere in grado di richiedere autonomamente un pacchetto di accesso. È possibile specificare se è necessaria l'approvazione e una data di scadenza per l'accesso. Se è necessaria l'approvazione, è inoltre possibile invitare uno o più utenti dell'organizzazione esterna alla directory e designarli come responsabili approvazione, poiché è probabile che sappiano quali utenti esterni dell'organizzazione necessitano dell'accesso. Dopo aver configurato il pacchetto di accesso, è possibile inviare il collegamento del pacchetto di accesso alla persona di contatto (sponsor) presso l'organizzazione esterna. Tale contatto può essere condiviso con altri utenti dell'organizzazione esterna e può utilizzare questo collegamento per richiedere il pacchetto di accesso. Anche gli utenti di tale organizzazione che sono già stati invitati nella directory possono utilizzare tale collegamento.

Quando una richiesta viene approvata, la gestione dei diritti eseguirà il provisioning dell'utente con l'accesso necessario, che può includere l'invito dell'utente se non è già presente nella directory. Azure AD creerà automaticamente un account guest B2B per loro. Si noti che un amministratore potrebbe aver precedentemente limitato le organizzazioni consentite per la collaborazione, impostando un [elenco B2B consentire o negare](../b2b/allow-deny-list.md) per consentire o bloccare gli inviti ad altre organizzazioni.  Se l'utente non è consentito dall'elenco Consenti o Blocca, non verrà invitato.

Poiché non si desidera che l'accesso dell'utente esterno duri per sempre, specificare una data di scadenza nei criteri, ad esempio 180 giorni. Dopo 180 giorni, se l'accesso non viene esteso, la gestione dei diritti rimuoverà tutti gli accessi associati al pacchetto di accesso. Per impostazione predefinita, se l'utente invitato tramite la gestione dei diritti non dispone di altre assegnazioni del pacchetto di accesso, quando perde rà l'ultima assegnazione, l'accesso dell'account guest verrà bloccato per 30 giorni e successivamente rimosso. Ciò impedisce la proliferazione di account non necessari. Come descritto nelle sezioni seguenti, queste impostazioni sono configurabili.

## <a name="how-access-works-for-external-users"></a>Funzionamento dell'accesso per gli utenti esterni

Il diagramma e i passaggi seguenti forniscono una panoramica del modo in cui agli utenti esterni viene concesso l'accesso a un pacchetto di accesso.

![Diagramma che mostra il ciclo di vita degli utenti esterni](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Aggiungere [un'organizzazione connessa](entitlement-management-organization.md) per la directory o il dominio di Azure AD con cui si vuole collaborare.

1. Creare un pacchetto di accesso nella directory che include un criterio [per gli utenti non nella directory](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Si invia un collegamento al [portale My Access](entitlement-management-access-package-settings.md) al contatto presso l'organizzazione esterna che possono condividere con i propri utenti per richiedere il pacchetto di accesso.

1. Un utente esterno **(il richiedente A** in questo esempio) usa il collegamento al portale My Access per [richiedere l'accesso](entitlement-management-request-access.md) al pacchetto di accesso. La modalità di accesso dell'utente dipende dal tipo di autenticazione della directory o del dominio definito nell'organizzazione connessa.

1. Un approvatore [approva la richiesta](entitlement-management-request-approve.md) (o la richiesta viene approvata automaticamente).

1. La richiesta passa allo [stato di consegna.](entitlement-management-process.md)

1. Utilizzando il processo di invito B2B, viene creato un account utente guest nella directory **(richiedente A (ospite)** in questo esempio). Se viene definito un [elenco Consenti o Rifiuta,](../b2b/allow-deny-list.md) verrà applicata l'impostazione dell'elenco.

1. All'utente guest viene assegnato l'accesso a tutte le risorse nel pacchetto di accesso. L'applicazione di modifiche in Azure AD e ad altri Microsoft Online Services o applicazioni SaaS connesse può richiedere del tempo. Per ulteriori informazioni, vedere [Quando vengono applicate le modifiche.](entitlement-management-access-package-resources.md#when-changes-are-applied)

1. L'utente esterno riceve un messaggio di posta elettronica che indica che l'accesso è stato [recapitato.](entitlement-management-process.md)

1. Per accedere alle risorse, l'utente esterno può fare clic sul collegamento nel messaggio di posta elettronica o tentare di accedere direttamente alle risorse della directory per completare il processo di invito.

1. A seconda delle impostazioni dei criteri, con il passare del tempo, l'assegnazione del pacchetto di accesso per l'utente esterno scade e l'accesso dell'utente esterno viene rimosso.

1. A seconda del ciclo di vita delle impostazioni degli utenti esterni, quando l'utente esterno non ha più alcuna assegnazione del pacchetto di accesso, all'utente esterno viene impedito di accedere e l'account utente guest viene rimosso dalla directory.

## <a name="settings-for-external-users"></a>Impostazioni per utenti esterni

Per garantire che gli utenti esterni all'organizzazione possano richiedere l'accesso ai pacchetti e ottenere l'accesso alle risorse in tali pacchetti di accesso, è necessario verificare alcune impostazioni.

### <a name="enable-catalog-for-external-users"></a>Abilitare il catalogo per gli utenti esterniEnable catalog for external users

- Per impostazione predefinita, quando si crea un [nuovo catalogo](entitlement-management-catalog-create.md), è abilitato per consentire agli utenti esterni di richiedere pacchetti di accesso nel catalogo. Assicurarsi che **Abilitato per gli utenti esterni** sia impostato su **Sì**.

    ![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2B di Azure ADConfigure your Azure AD B2B external collaboration settings

- Consentire agli ospiti di invitare altri ospiti nella tua directory significa che gli inviti degli ospiti possono verificarsi al di fuori della gestione dei diritti. Ti consigliamo di **impostare che gli ospiti possono invitare** su **No** per consentire solo inviti adeguatamente governati.
- Se si utilizza l'elenco Consenti B2B, è necessario assicurarsi che qualsiasi dominio che si desidera collaborare utilizzando la gestione dei diritti venga aggiunto all'elenco. In alternativa, se si utilizza l'elenco di negazione B2B, è necessario assicurarsi che qualsiasi dominio con cui si desidera collaborare non venga aggiunto all'elenco.
- Se si crea un criterio di gestione dei diritti per **Tutti gli utenti** (Tutte le organizzazioni connesse e i nuovi utenti esterni), tutte le impostazioni b2B consentono o negano l'elenco avranno la precedenza. Pertanto, assicurarsi di includere i domini che si intende includere in questo criterio nell'elenco Consenti se si utilizza uno ed escluderli dall'elenco di connessioni rifiutate se si utilizza un elenco di elementi non consentiti.
- Se si desidera creare un criterio di gestione dei diritti che includa **Tutti gli utenti** (Tutte le organizzazioni connesse, ovvero tutti i nuovi utenti esterni), è necessario abilitare prima l'autenticazione monouso del passcode tramite posta elettronica per la directory. Per ulteriori informazioni, consultate [Autenticazione monouso (anteprima) tramite posta elettronica.](../b2b/one-time-passcode.md#opting-in-to-the-preview)
- Per altre informazioni sulle impostazioni di collaborazione esterna B2B di Azure AD, vedere [Abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare ospiti.](../b2b/delegate-invitations.md)

    ![Impostazioni di collaborazione esterna di Azure ADAzure AD external collaboration settings](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Esaminare i criteri di accesso condizionaleReview your Conditional Access policies

- Assicurarsi di escludere gli ospiti da qualsiasi criteri di accesso condizionale che i nuovi utenti guest non saranno in grado di soddisfare in quanto ciò impedirà loro di accedere alla directory. Ad esempio, è probabile che gli ospiti non dispongano di un dispositivo registrato, non si trovano in una posizione nota e non si desidera eseguire nuovamente la registrazione per l'autenticazione a più fattori (MFA), pertanto l'aggiunta di questi requisiti in un criterio di accesso condizionale impedirà agli ospiti di utilizzare il diritto Gestione. Per altre informazioni, vedere [Quali sono le condizioni in Accesso condizionale di Azure Active Directory?](../conditional-access/concept-conditional-access-conditions.md).

    ![Impostazioni di esclusione dei criteri di accesso condizionale di Azure ADAzure AD Conditional Access policy exclude settings](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Rivedere le impostazioni di condivisione esterna di SharePoint Online

- Se si desidera includere siti di SharePoint Online nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'impostazione di condivisione esterna a livello di organizzazione sia impostata su **Chiunque** (gli utenti non richiedono l'accesso) o **guest nuovi ed esistenti** (gli ospiti devono accedere o fornire un codice di verifica). Per ulteriori informazioni, consultate [Attivare o disattivare la condivisione esterna.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Se si desidera limitare qualsiasi condivisione esterna al di fuori della gestione dei diritti, è possibile impostare l'impostazione di condivisione esterna su **Ospiti esistenti**. Quindi, solo i nuovi utenti invitati tramite la gestione dei diritti saranno in grado di accedere a questi siti. Per ulteriori informazioni, consultate [Attivare o disattivare la condivisione esterna.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Assicurarsi che le impostazioni a livello di sito consentano l'accesso guest (le stesse selezioni di opzioni elencate in precedenza). Per ulteriori informazioni, consultate [Attivare o disattivare la condivisione esterna per un sito.](https://docs.microsoft.com/sharepoint/change-external-sharing-site)

### <a name="review-your-office-365-group-sharing-settings"></a>Esaminare le impostazioni di condivisione dei gruppi di Office 365

- Se si desidera includere gruppi di Office 365 nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'opzione Consenti agli utenti di **aggiungere nuovi guest all'organizzazione** sia impostata su **Attivato** per consentire l'accesso guest. Per ulteriori informazioni, vedere [Gestire l'accesso guest ai gruppi di Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Se si desidera che gli utenti esterni siano in grado di accedere al sito di SharePoint Online e alle risorse associate a un gruppo di Office 365, assicurarsi di attivare la condivisione esterna di SharePoint Online. Per ulteriori informazioni, consultate [Attivare o disattivare la condivisione esterna.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Per informazioni su come impostare i criteri guest per i gruppi di Office 365 a livello di directory in PowerShell, vedere [Esempio: Configurare i criteri Guest per i gruppi a livello](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)di directory.

### <a name="review-your-teams-sharing-settings"></a>Rivedere le impostazioni di condivisione di Teams

- Se si desidera includere Teams nei pacchetti di accesso per gli utenti esterni, assicurarsi che l'opzione **Consenti accesso guest in Microsoft Teams** sia impostata su **Attivato** per consentire l'accesso guest. Per ulteriori informazioni, vedere [Configurare l'accesso guest nell'interfaccia](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)di amministrazione di Microsoft Teams.

## <a name="manage-the-lifecycle-of-external-users"></a>Gestire il ciclo di vita degli utenti esterni

È possibile selezionare cosa accade quando un utente esterno, invitato nella directory tramite l'approvazione di un pacchetto di accesso, non dispone più di assegnazioni di pacchetti di accesso. Ciò può verificarsi se l'utente rinuncia a tutte le assegnazioni del pacchetto di accesso o alla scadenza dell'ultima assegnazione del pacchetto di accesso. Per impostazione predefinita, quando un utente esterno non dispone più di assegnazioni di pacchetti di accesso, viene loro impedito di accedere alla directory. Dopo 30 giorni, il loro account utente guest viene rimosso dalla directory.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **Gestione dei diritti,** fare clic su **Impostazioni.**

1. Fare clic su **Edit**.

    ![Impostazioni per la gestione del ciclo di vita degli utenti esterni](./media/entitlement-management-external-users/settings-external-users.png)

1. Nella sezione **Gestisci il ciclo di vita degli utenti esterni** selezionare le diverse impostazioni per gli utenti esterni.

1. Quando un utente esterno perde l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera impedire loro di accedere a questa directory, impostare **l'utente esterno Impedisci l'accesso a questa directory** su **Sì**.

    > [!NOTE]
    > Se a un utente viene impedito di accedere a questa directory, l'utente non sarà in grado di richiedere nuovamente il pacchetto di accesso o richiedere un accesso aggiuntivo in questa directory. Non configurare il blocco dell'accesso se successivamente dovranno richiedere l'accesso ad altri pacchetti di accesso.

1. Quando un utente esterno perde l'ultima assegnazione a tutti i pacchetti di accesso, se si desidera rimuovere il proprio account utente guest in questa directory, impostare **Rimuovi utente esterno** su **Sì**.

    > [!NOTE]
    > La gestione dei diritti rimuove solo gli account invitati tramite la gestione dei diritti. Si noti inoltre che a un utente verrà impedito di accedere e rimuovere da questa directory anche se tale utente è stato aggiunto alle risorse in questa directory che non hanno accesso alle assegnazioni dei pacchetti. Se il guest era presente in questa directory prima di ricevere le assegnazioni dei pacchetti di accesso, rimarrà. Tuttavia, se l'ospite è stato invitato tramite un'assegnazione del pacchetto di accesso e dopo essere stato invitato è stato assegnato anche a un sito di OneDrive for Business o SharePoint Online, verrà comunque rimosso.

1. Se si desidera rimuovere l'account utente guest in questa directory, è possibile impostare il numero di giorni prima che venga rimosso. Se si desidera rimuovere l'account utente guest non appena perdono l'ultima assegnazione a qualsiasi pacchetto di accesso, impostare **Numero di giorni prima di rimuovere l'utente esterno da questa directory su** **0**.

1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere un'organizzazione connessa](entitlement-management-organization.md)
- [Per gli utenti non presente nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Risolvere i problemi](entitlement-management-troubleshoot.md)
