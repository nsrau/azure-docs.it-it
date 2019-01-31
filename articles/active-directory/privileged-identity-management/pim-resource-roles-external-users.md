---
title: Invitare utenti esterni e assegnare i ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come invitare utenti esterni e assegnare i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3cfb792b9c9befcbc2ee869ef5b31903e5c10f9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188610"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Invitare utenti esterni e assegnare i ruoli delle risorse di Azure in PIM

Azure Activity Directory (Azure AD) business-to-business (B2B) è un insieme di funzionalità di Azure AD che consente alle organizzazioni di collaborare con utenti e fornitori esterni utilizzando qualsiasi account. Quando si combinano B2B e Azure AD Privileged Identity Management (PIM), è possibile continuare ad applicare i requisiti di conformità e governance a utenti esterni. Ad esempio, è possibile utilizzare queste funzionalità PIM per le risorse Azure con utenti esterni:

- Assegnare l'accesso a specifiche risorse di Azure
- Abilitare l'accesso JIT alla VM
- Specificare una data di fine e durata dell'assegnazione
- Richiedere l'autenticazione a più fattori per l'assegnazione o l'attivazione attiva
- Eseguire verifiche di accesso
- Usare gli avvisi e log di controllo

Questo articolo descrive come invitare un utente esterno alla propria directory e come gestire l'accesso alle risorse di Azure utilizzando PIM.

## <a name="when-would-you-invite-external-users"></a>In quale occasione è opportuno invitare utenti esterni?

Ecco qualche esempio di scenario in cui si potrebbero invitare utenti esterni alla propria directory:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di una grande organizzazione che utilizza a livello locale Active Directory Federation Services di accedere all'applicazione di spesa.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Come funziona la collaborazione esterna attraverso B2B?

Quando si usa B2B, è possibile invitare un utente esterno nella propria directory. L'utente esterno appare nella directory, ma non dispone di credenziali associate. Ogni volta che un utente esterno deve essere autenticato, deve essere autenticato nella sua home directory e non nella directory principale. Questo significa che se l'utente esterno non ha più accesso alla propria home directory, perde automaticamente l'accesso alla directory principale. Ad esempio, se l'utente esterno lascia l'organizzazione, perde automaticamente l'accesso a qualsiasi risorsa che è stata condivisa con lui nella directory senza che si debba fare nulla. Per altre informazioni su B2B, vedere [Che cos'è l'accesso utente guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B e utente esterno](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Controllo delle impostazioni di collaborazione esterna

Per assicurarsi che è possibile invitare utenti esterni alla directory, è necessario controllare le impostazioni di collaborazione esterna.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Fare clic su **Azure Active Directory** > **Impostazioni utente**.

1. Fare clic su **Gestisci le impostazioni di collaborazione esterna**.

    ![Impostazioni di collaborazione esterna](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Assicurarsi che l'opzione **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare** sia impostata su **Sì**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Invitare un utente esterno e assegnare un ruolo

Usando PIM, è possibile invitare un utente esterno e renderlo idoneo per un ruolo di risorsa di Azure proprio come un utente membro.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) o [Amministratore account utente](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

1. Fare clic sulla risorsa da gestire, ad esempio una risorsa, un gruppo di risorse, una sottoscrizione o un gruppo di gestione.

    È necessario impostare l'ambito solo su ciò di cui ha bisogno l'utente esterno.

1. In Gestione fare clic su **Ruoli** per visualizzare l'elenco di ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Fare clic sul ruolo minimo di cui l'utente ha bisogno.

    ![Ruolo selezionato](./media/pim-resource-roles-external-users/selected-role.png)

1. Nella pagina del ruolo, fare clic su **Aggiungi membro** per aprire il riquadro Nuova assegnazione.

1. Fare clic su **Selezionare un membro o un gruppo**.

    ![Selezionare un membro o un gruppo](./media/pim-resource-roles-external-users/select-member-group.png)

1. Per invitare un utente esterno, fare clic su **Invita**.

    ![Invitare un utente guest](./media/pim-resource-roles-external-users/invite-guest.png)

1. Dopo aver specificato un utente esterno, fare clic su **Invita**.

    L'utente esterno deve essere aggiunto come membro selezionato.

1. Nel riquadro Selezionare un membro o un gruppo, fare clic su **Seleziona**.

1. Nel riquadro Impostazioni di appartenenza, selezionare il tipo di assegnazione e la durata.

    ![Impostazioni di appartenenza](./media/pim-resource-roles-external-users/membership-settings.png)

1. Per completare l'assegnazione, fare clic su **Fine** e quindi su **Aggiungi**.

    L'assegnazione di ruolo utente esterno apparirà nell’elenco dei ruoli.

    ![Assegnazione di ruolo per utenti esterni](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Attivazione del ruolo come utente esterno

Come utente esterno, è necessario accettare l'invito per la directory di Azure AD e possibilmente attivare il ruolo.

1. Aprire il messaggio di posta elettronica con l'invito alla directory. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica](./media/pim-resource-roles-external-users/email-invite.png)

1. Fare clic sul collegamento **Configurazione iniziale** nel messaggio di posta elettronica.

1. Dopo aver verificato le autorizzazioni, fare clic su **Accetta**.

    ![Verifica le autorizzazioni](./media/pim-resource-roles-external-users/invite-accept.png)

1. Potrebbe essere richiesto di accettare le condizioni per l’utilizzo e specificare se si vuole restare connessi.

    Il portale di Azure si apre. Se si è idonei solo per un ruolo, non si avrà accesso alle risorse.

1. Per attivare il ruolo, aprire il messaggio di posta elettronica con il collegamento di attivazione del ruolo. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Fare clic su **Attiva il ruolo** per aprire i ruoli idonei in PIM.

    ![Ruoli personali - Idoneo](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. In Azione, fare clic sul collegamento **Attiva**.

    A seconda delle impostazioni di ruolo, è necessario specificare alcune informazioni per attivare il ruolo.

1. Dopo aver specificato le impostazioni per il ruolo, fare clic su **Attiva** per attivare il ruolo.

    ![Attiva il ruolo](./media/pim-resource-roles-external-users/activate-role.png)

    A meno che l'amministratore non debba approvare la richiesta, si ottiene accesso alle risorse specificate.

## <a name="view-activity-for-an-external-user"></a>Visualizza attività per un utente esterno

Esattamente come un utente membro, è possibile visualizzare i log di controllo per tenere traccia delle operazioni eseguite dagli utenti esterni.

1. Come amministratore, aprire PIM e selezionare la risorsa che è stata condivisa.

1. Fare clic su **Controllo delle risorse** per visualizzare l'attività per tale risorsa. Di seguito viene riportato un esempio di attività per un gruppo di risorse.

    ![Controllo delle risorse](./media/pim-resource-roles-external-users/audit-resource.png)

1. Per visualizzare l'attività per l'utente esterno, fare clic su **Azure Active Directory** > **Utenti** > Utente esterno.

1. Fare clic su **Log di controllo** per visualizzare i log di controllo per la directory. Se necessario, è possibile specificare i filtri.

    ![Directory Audit (Controllo directory)](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli della directory di Azure AD in PIM](pim-how-to-add-role-to-user.md)
- [Che cos'è l'accesso utente guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md)
