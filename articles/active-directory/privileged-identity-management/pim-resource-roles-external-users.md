---
title: Invitare utenti Guest e assegnare i ruoli delle risorse di Azure in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come invitare utenti guest esterni e assegnare i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0afec1d6eded25a2d9b2389c950e2e21e06e0d54
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307071"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Invitare utenti guest e assegnare i ruoli delle risorse di Azure in PIM

Azure Active Directory (Azure AD) business-to-business (B2B) è un set di funzionalità all'interno di Azure AD che consente alle organizzazioni di collaborare con fornitori tramite qualsiasi account e utenti guest esterni (guest). Quando si combinano B2B con Azure AD Privileged Identity Management (PIM), è possibile continuare a si applicano i requisiti di conformità e governance per guest. Ad esempio, è possibile utilizzare queste funzionalità PIM per le attività di identità di Azure con gli utenti Guest:

- Assegnare l'accesso a specifiche risorse di Azure
- Abilitare l'accesso JIT alla VM
- Specificare una data di fine e durata dell'assegnazione
- Richiedere l'autenticazione a più fattori per l'assegnazione o l'attivazione attiva
- Eseguire verifiche di accesso
- Usare gli avvisi e log di controllo

Questo articolo descrive come invitare un utente guest nell'organizzazione e gestire l'accesso alle risorse di Azure con Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando si sarebbe invitare utenti guest?

Ecco alcuni scenari di esempio quando è possibile invitare utenti guest nell'organizzazione:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di una grande organizzazione che utilizza a livello locale Active Directory Federation Services di accedere all'applicazione di spesa.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Qual è la collaborazione B2B con guest lavoro?

Quando si usa collaborazione B2B, è possibile invitare un utente esterno all'organizzazione come guest. Il guest viene visualizzato sia nella propria organizzazione, ma il guest non dispone di credenziali è associate. Ogni volta che un utente guest deve essere autenticato, devono essere autenticate nella propria organizzazione e non all'interno dell'organizzazione. Ciò significa che se il guest non ha più accesso alla propria organizzazione, sono anche perdere l'accesso all'organizzazione. Ad esempio, se il guest lascia l'organizzazione, vengono automaticamente perdere l'accesso a tutte le risorse che è condiviso con loro in Azure AD senza dover eseguire alcuna operazione. Per altre informazioni su B2B, vedere [Che cos'è l'accesso utente guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B e guest](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Controllare le impostazioni di collaborazione di guest

Per assicurarsi che si possono invitare utenti guest nella propria organizzazione, è necessario controllare le impostazioni di collaborazione di guest.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Fare clic su **Azure Active Directory** > **Impostazioni utente**.

1. Fare clic su **Gestisci le impostazioni di collaborazione esterna**.

    ![Impostazioni di collaborazione esterna](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Assicurarsi che l'opzione **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare** sia impostata su **Sì**.

## <a name="invite-a-guest-and-assign-a-role"></a>Invitare un utente guest e assegnare un ruolo

Usare PIM, è possibile invitare un utente guest e renderli idonei per un ruolo di risorse di Azure esattamente come un utente membro.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente è membro del [amministratori dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) o [Amministratore utenti](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) ruolo.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

1. Fare clic sulla risorsa da gestire, ad esempio una risorsa, un gruppo di risorse, una sottoscrizione o un gruppo di gestione.

    È necessario impostare l'ambito al quale l'utente guest deve solo.

1. In Gestione fare clic su **Ruoli** per visualizzare l'elenco di ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Fare clic sul ruolo minimo di cui l'utente ha bisogno.

    ![Ruolo selezionato](./media/pim-resource-roles-external-users/selected-role.png)

1. Nella pagina del ruolo, fare clic su **Aggiungi membro** per aprire il riquadro Nuova assegnazione.

1. Fare clic su **Selezionare un membro o un gruppo**.

    ![Selezionare un membro o un gruppo](./media/pim-resource-roles-external-users/select-member-group.png)

1. Per invitare un utente guest, fare clic su **invitare**.

    ![Invitare un utente guest](./media/pim-resource-roles-external-users/invite-guest.png)

1. Dopo aver selezionato un utente guest, fare clic su **invitare**.

    Il guest deve essere aggiunti come membro selezionato.

1. Nel **selezionare un membro o un gruppo** riquadro, fare clic su **seleziona**.

1. Nel **delle impostazioni di appartenenza** riquadro, selezionare il tipo di assegnazione e la durata.

    ![Impostazioni di appartenenza](./media/pim-resource-roles-external-users/membership-settings.png)

1. Per completare l'assegnazione, fare clic su **Fine** e quindi su **Aggiungi**.

    L'assegnazione di ruolo guest verrà visualizzato nell'elenco dei ruoli.

    ![Assegnazione di ruolo per utente guest](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Attivazione del ruolo come guest

Come utente esterno, è necessario accettare l'invito all'organizzazione di Azure AD e possibilmente attivare il ruolo.

1. Aprire il messaggio di posta elettronica con l'invito. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica](./media/pim-resource-roles-external-users/email-invite.png)

1. Fare clic sul collegamento **Configurazione iniziale** nel messaggio di posta elettronica.

1. Dopo aver verificato le autorizzazioni, fare clic su **Accetta**.

    ![Verifica le autorizzazioni](./media/pim-resource-roles-external-users/invite-accept.png)

1. Potrebbe essere richiesto di accettare le condizioni d'uso e specificare se si vuole restare connessi in.

    Il portale di Azure si apre. Se si è idonei solo per un ruolo, non si avrà accesso alle risorse.

1. Per attivare il ruolo, aprire il messaggio di posta elettronica con il collegamento di attivazione del ruolo. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Fare clic su **Attiva il ruolo** per aprire i ruoli idonei in PIM.

    ![Ruoli personali - idonei](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. In Azione, fare clic sul collegamento **Attiva**.

    A seconda delle impostazioni di ruolo, è necessario specificare alcune informazioni per attivare il ruolo.

1. Dopo aver specificato le impostazioni per il ruolo, fare clic su **Attiva** per attivare il ruolo.

    ![Attiva il ruolo](./media/pim-resource-roles-external-users/activate-role.png)

    A meno che l'amministratore non debba approvare la richiesta, si ottiene accesso alle risorse specificate.

## <a name="view-activity-for-a-guest"></a>Visualizza attività per un utente guest

Esattamente come un utente membro, è possibile visualizzare i log di controllo per tenere traccia delle operazioni eseguite dagli utenti guest.

1. Come amministratore, aprire PIM e selezionare la risorsa che è stata condivisa.

1. Fare clic su **Controllo delle risorse** per visualizzare l'attività per tale risorsa. Di seguito viene riportato un esempio di attività per un gruppo di risorse.

    ![Controllo delle risorse](./media/pim-resource-roles-external-users/audit-resource.png)

1. Per visualizzare l'attività per l'utente guest, fare clic su **Azure Active Directory** > **utenti** > nome guest.

1. Fare clic su **log di controllo** per visualizzare i log di controllo per l'organizzazione. Se necessario, è possibile specificare i filtri.

    ![controllo dell'organizzazione](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di amministratore di Azure AD in PIM](pim-how-to-add-role-to-user.md)
- [Che cos'è l'accesso utente guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md)
