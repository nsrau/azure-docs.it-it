---
title: Assegnare ruoli delle risorse di Azure ai guest in PIM - Azure AD Documenti Microsoft
description: Informazioni su come invitare utenti guest esterni e assegnare ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021947"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Invitare utenti guest e assegnare ruoli delle risorse di Azure in Gestione identità con privilegiInvite guest users and assign Azure resource roles in Privileged Identity Management

Gli utenti guest di Azure Active Directory (Azure AD) fanno parte delle funzionalità di collaborazione business-to-business (B2B) in Azure AD in modo che sia possibile gestire utenti guest esterni e fornitori come guest in Azure AD. Quando si combina la collaborazione B2B con Azure AD Privileged Identity Management (PIM), è possibile estendere i requisiti di conformità e governance agli ospiti. Ad esempio, è possibile usare queste funzionalità di gestione delle identità con privilegi per le attività di identità di Azure con i guest:For example, you can use these Privileged Identity Management features for Azure identity tasks with guests:

- Assegnare l'accesso a specifiche risorse di Azure
- Abilitare l'accesso JIT alla VM
- Specificare una data di fine e durata dell'assegnazione
- Richiedere l'autenticazione a più fattori per l'assegnazione o l'attivazione attivaRequire multi-factor authentication on active assignment or activation
- Eseguire verifiche di accesso
- Usare gli avvisi e log di controllo

Questo articolo descrive come invitare un ospite dell'organizzazione e gestirne l'accesso alle risorse di Azure usando Gestione identità con privilegi.

## <a name="when-would-you-invite-guests"></a>Quando inviteresti gli ospiti?

Di seguito sono riportati alcuni esempi di quando è possibile invitare gli ospiti nell'organizzazione:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di una grande organizzazione che utilizza a livello locale Active Directory Federation Services di accedere all'applicazione di spesa.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Come funziona la collaborazione con gli ospiti B2B?

Quando si utilizza la collaborazione B2B, è possibile invitare un utente esterno all'organizzazione come ospite. Il guest può essere gestito come utente nell'organizzazione, ma un guest deve essere autenticato nell'organizzazione principale e non nell'organizzazione di Azure AD. Ciò significa che se l'ospite non ha più accesso all'organizzazione principale, perde anche l'accesso all'organizzazione. Ad esempio, se il guest lascia l'organizzazione, perde automaticamente l'accesso alle risorse condivise con loro in Azure AD senza che sia necessario eseguire alcuna operazione. Per altre informazioni sulla collaborazione B2B, vedere [Che cos'è l'accesso degli utenti guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagramma che mostra la modalità di autenticazione di un utente guest nella home directory](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Controllare le impostazioni di collaborazione degli ospiti

Per assicurarti di poter invitare ospiti nella tua organizzazione, devi controllare le impostazioni di collaborazione degli ospiti.

1. Accedere al portale di [Azure](https://portal.azure.com/).

1. Selezionare**Impostazioni utente** **di Azure Active Directory** > .

1. Selezionare **Gestisci impostazioni di collaborazione esterna**.

    ![Pagina Impostazioni di collaborazione esterna con le impostazioni di restrizione di autorizzazioni, inviti e collaborazione](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Assicurarsi che **l'opzione Amministratori e utenti con il ruolo invitatore guest possa invitare** sia impostata su **Sì**.

## <a name="invite-a-guest-and-assign-a-role"></a>Invitare un ospite e assegnare un ruolo

Usando La gestione delle identità con privilegi, è possibile invitare un guest e renderlo idoneo per un ruolo di risorsa di Azure.Using Privileged Identity Management, you can invite a guest and make them eligible for an Azure resource role.

1. Accedere al portale di [Azure](https://portal.azure.com/) con un utente membro del ruolo Amministratore ruolo [con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) o del ruolo Amministratore ruolo [utente.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

1. Selezionare la risorsa che si desidera gestire, ad esempio una risorsa, un gruppo di risorse, una sottoscrizione o un gruppo di gestione.

    È necessario impostare l'ambito solo su ciò di cui l'ospite ha bisogno.

1. In Gestisci selezionare Ruoli per visualizzare l'elenco dei ruoli per le risorse di Azure.Under Manage, select **Roles** to see the list of roles for Azure resources.

    ![Elenco dei ruoli delle risorse di Azure che mostra il numero di utenti attivi e idoneiAzure resources roles list showing number of users that are active and eligible](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selezionare il ruolo minimo necessario per l'utente.

    ![Pagina del ruolo selezionata che elenca i membri correnti di tale ruolo](./media/pim-resource-roles-external-users/selected-role.png)

1. Nella pagina del ruolo selezionare **Aggiungi membro** per aprire il riquadro Nuova assegnazione.

1. Fare clic su **Selezionare un membro o un gruppo**.

    ![Nuova assegnazione: selezionare un riquadro membro o gruppo per elencare utenti e gruppi insieme a un'opzione Invita](./media/pim-resource-roles-external-users/select-member-group.png)

1. Per invitare un ospite, fare clic su **Invita**.

    ![Invitare una pagina ospite con caselle per inserire un indirizzo di posta elettronica e specificare un messaggio personale](./media/pim-resource-roles-external-users/invite-guest.png)

1. Dopo aver selezionato un ospite, fare clic su **Invita**.

    L'ospite deve essere aggiunto come membro selezionato.

1. Nel riquadro **Selezionare un membro o un gruppo** fare clic su **Seleziona**.

1. Nel riquadro **Impostazioni di appartenenza** selezionare il tipo di assegnazione e la durata.

    ![Nuova assegnazione - Pagina Impostazioni appartenenza con opzioni per specificare il tipo di assegnazione, la data di inizio e la data di fine](./media/pim-resource-roles-external-users/membership-settings.png)

1. Per completare l'assegnazione, selezionare **Fine** e quindi **Aggiungi**.

    L'assegnazione di ruolo guest verrà visualizzata nell'elenco dei ruoli.

    ![Pagina del ruolo che elenca l'ospite come idoneo](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Attivare il ruolo come ospite

Se si è un utente esterno, è necessario accettare l'invito a essere un guest nell'organizzazione di Azure AD ed eventualmente attivare l'assegnazione di ruolo.

1. Aprire l'e-mail con l'invito. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica con nome della directory, messaggio personale e collegamento Guida introduttiva](./media/pim-resource-roles-external-users/email-invite.png)

1. Selezionare il collegamento **Inizia** nell'e-mail.

1. Dopo aver verificato le autorizzazioni, fare clic su **Accetta**.

    ![Pagina Rivedi le autorizzazioni in un browser con un elenco di autorizzazioni che l'organizzazione desidera esaminare](./media/pim-resource-roles-external-users/invite-accept.png)

1. Potrebbe essere richiesto di accettare le condizioni per l'utilizzo e specificare se si desidera mantenere l'accesso. Nel portale di Azure, se si è *idonei* per un ruolo, non sarà ancora possibile accedere alle risorse.

1. Per attivare l'assegnazione di ruolo, aprire l'e-mail con il collegamento attiva ruolo. Il messaggio di posta elettronica sarà simile al seguente:

    ![Messaggio di posta elettronica che indica che si è idonei per un ruolo con un collegamento Attiva ruolo](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selezionare **Attiva ruolo** per aprire i ruoli idonei in Gestione identità con privilegi.

    ![Pagina Ruoli personali in Gestione identità privilegiate che elenca i ruoli idonei](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. In Azione selezionare il collegamento **Attiva.**

    A seconda delle impostazioni di ruolo, è necessario specificare alcune informazioni per attivare il ruolo.

1. Dopo aver specificato le impostazioni per il ruolo, fare clic su **Attiva** per attivare il ruolo.

    ![Attivare l'ambito e le opzioni di elenco delle pagine per specificare l'ora di inizio, la durata e il motivo](./media/pim-resource-roles-external-users/activate-role.png)

    A meno che l'amministratore non debba approvare la richiesta, si ottiene accesso alle risorse specificate.

## <a name="view-activity-for-a-guest"></a>Visualizzare l'attività per un ospite

È possibile visualizzare i registri di controllo per tenere traccia delle operazioni eseguite da ospiti.

1. Come amministratore, aprire Gestione identità con privilegi e selezionare la risorsa condivisa.

1. Selezionare **Controllo risorsa** per visualizzare l'attività per la risorsa. Di seguito viene riportato un esempio di attività per un gruppo di risorse.

    ![Risorse di Azure - Pagina di controllo delle risorse che elenca l'ora, il richiedente e l'azioneAzure resources - Resource audit page listing the time, requestor, and action](./media/pim-resource-roles-external-users/audit-resource.png)

1. Per visualizzare l'attività per il guest, selezionare*Nome guest* **Azure Active Directory** > **Users** > .

1. Selezionare **Log di controllo** per visualizzare i log di controllo per l'organizzazione. Se necessario, è possibile specificare i filtri.

    ![Registri di controllo della directory che elencano la data, la destinazione, l'avvio da e l'attività](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di amministratore di Azure AD in Gestione identità con privilegiAssign Azure AD admin roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Che cos'è l'accesso degli utenti guest in Collaborazione B2B di Azure AD?](../b2b/what-is-b2b.md)
