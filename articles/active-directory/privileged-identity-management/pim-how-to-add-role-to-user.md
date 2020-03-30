---
title: Assegnare ruoli di Azure AD in PIM - Azure Active Directory Documenti Microsoft
description: Informazioni su come assegnare ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253273"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management

Con Azure Active Directory (Azure AD), un amministratore globale può effettuare assegnazioni di ruolo di amministratore di Azure AD **permanenti.** Queste assegnazioni di ruoli possono essere create usando il [portale di Azure](../users-groups-roles/directory-assign-admin-roles.md) o i [ comandi di PowerShell](/powershell/module/azuread#directory_roles).

Il servizio Azure AD Privileged Identity Management (PIM) consente inoltre agli amministratori dei ruoli con privilegi di effettuare assegnazioni di ruolo di amministratore permanente. Inoltre, gli amministratori dei ruoli con privilegi possono rendere gli utenti **idonei** per i ruoli di amministratore di Azure AD. Un amministratore idoneo può attivare il ruolo quando serve, con autorizzazioni che scadono al termine delle operazioni.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure.Start in November 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure resource roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Accedere al [portale](https://portal.azure.com/) di Azure con un utente che risieda il ruolo di amministratore del ruolo Privileged.Sign in to the Azure portal with a user who is in the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role role role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="assign-a-role"></a>Assegnare un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo di amministratore di Azure AD.

1. Accedere al portale di [Azure](https://portal.azure.com/) con un utente membro del ruolo Amministratore ruolo [con privilegi.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire la gestione delle identità con privilegi, vedere [Concedere l'accesso ad altri amministratori per gestire La gestione delle identità con privilegi](pim-how-to-give-access-to-pim.md).

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Ruoli** per visualizzare l'elenco dei ruoli per le autorizzazioni di Azure AD.

    ![Ruoli di Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selezionare **Aggiungi membro** per aprire la pagina **Nuova assegnazione.**

1. Selezionare **Selezionare un ruolo** per aprire la pagina Selezionare un ruolo.

    ![Riquadro Nuova assegnazione](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selezionare un ruolo da assegnare e quindi fare clic su **Seleziona**.

1. Selezionare un membro a cui si desidera assegnare al ruolo e quindi selezionare **Seleziona**.

1. Nell'elenco **Tipo di assegnazione** del riquadro **Impostazioni di appartenenza** selezionare **Idoneo** o **Attivo**.

    - Le assegnazioni **idonee** richiedono al membro del ruolo di eseguire un'azione per utilizzare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - **Le** assegnazioni attive non richiedono al membro di eseguire alcuna azione per utilizzare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Se l'assegnazione deve essere permanente (permanentemente idonea o assegnata in modo permanente), selezionare la casella di controllo **Permanente.**

    A seconda delle impostazioni dei ruoli, la casella di controllo potrebbero non essere visualizzata o potrebbe essere non modificabile.

1. Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare le caselle relative a data e ora di inizio e/o fine. Al termine, selezionare **Fine**.

    ![Impostazioni di appartenenza a - data e ora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Per creare la nuova assegnazione di ruolo, selezionare **Aggiungi**. Verrà visualizzata una notifica dello stato.

    ![Nuova assegnazione - Notifica](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Ruoli** per visualizzare l'elenco dei ruoli per Azure AD.

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Rendere un utente idoneo per un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo di amministratore di Azure AD.

1. Selezionare **Ruoli** o **Membri**.

    ![Ruoli di Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selezionare **Aggiungi membro** per aprire **Aggiungi membri gestiti**.

1. Selezionare **Selezionare un ruolo,** selezionare un ruolo da gestire e quindi **selezionare Seleziona**.

    ![Selezionare un ruolo](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selezionare **Seleziona membri**, selezionare gli utenti che si desidera assegnare al ruolo e quindi **selezionare Seleziona**.

    ![Selezionare un ruolo](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. In **Aggiungi membri gestiti**selezionare **OK** per aggiungere l'utente al ruolo.

1. Nell'elenco dei ruoli selezionare il ruolo appena assegnato per visualizzare l'elenco dei membri.

     Quando il ruolo è stato assegnato, l'utente selezionato verrà visualizzato nell'elenco dei membri con il valore **Idoneo** per il ruolo.

    ![Utente idoneo per un ruolo](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ora che l'utente è idoneo per il ruolo, informarlo di poterlo attivare in base alle istruzioni in Attivare i ruoli di [Azure AD in Gestione identità con privilegi](pim-how-to-activate-role.md).

    Gli amministratori idonei devono registrarsi ad Azure Multi-Factor Authentication (MFA) durante l'attivazione. Se un utente non può registrarsi per l'autenticazione a più fattori o utilizza un account Microsoft (ad @outlook.comesempio ), è necessario renderli permanenti in tutti i ruoli.

## <a name="make-a-role-assignment-permanent"></a>Rendere permanente un'assegnazione di ruolo

Per impostazione predefinita, i nuovi utenti sono *idonei* solo per un ruolo di amministratore di Azure AD. Per rendere permanente un'assegnazione di ruolo, eseguire la procedura seguente:

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare un ruolo **idoneo** che si desidera rendere permanente.

1. Selezionare **Altro** e quindi **Crea perm**.

    ![Rendere permanente l'assegnazione di ruolo](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Il ruolo viene elencato come **permanente**.

    ![Elenco di membri con modifica permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti dalle assegnazioni di ruolo, ma assicurarsi che sia sempre presente almeno un utente che sia un amministratore globale permanente. Se non si è certi se gli utenti necessitano ancora di assegnazioni di ruoli, è possibile [avviare una verifica di accesso per il ruolo](pim-how-to-start-security-review.md).

Seguire questi passaggi per rimuovere un utente specifico da un ruolo di amministratore di Azure AD.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare un'assegnazione di ruolo da rimuovere.

1. Selezionare **Altro** e quindi **Rimuovi**.

    ![Rimuovere un ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Nel messaggio che chiede di confermare selezionare **Sì**.

    ![Rimuovere un ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L'assegnazione di ruolo viene rimossa.

## <a name="authorization-error-when-assigning-roles"></a>Errore di autorizzazione durante l'assegnazione di ruoli

Se di recente è stato abilitato Gestione identità con privilegi per una sottoscrizione e viene visualizzato un errore di autorizzazione quando si tenta di rendere un utente idoneo per un ruolo di amministratore di Azure AD, è possibile che l'entità servizio MS-PIM non disponga ancora delle autorizzazioni appropriate. L'entità servizio MS-PIM deve disporre del ruolo [Amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) per assegnare ruoli ad altri utenti. Anziché attendere l'assegnazione automatica di questo ruolo a MS-PIM, è possibile assegnarlo manualmente.

Per assegnare il ruolo Amministratore Accesso utenti all'entità servizio MS-PIM per una sottoscrizione, seguire questa procedura.

1. Accedere al portale di Azure come amministratore globale.

1. Scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

1. Scegliere **Assegnazioni di ruolo** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controllare se all'entità servizio **MS-PIM** è assegnato il ruolo **Amministratore Accesso utenti**.

1. In caso negativo, scegliere **Aggiungi un'assegnazione di ruolo** per aprire il riquadro **Aggiungi un'assegnazione di ruolo**.

1. Nell'elenco a discesa **Ruolo** selezionare **Amministratore Accesso utenti**.

1. Nell'elenco **Seleziona** trovare e selezionare l'entità servizio **MS-PIM**.

   ![Riquadro Aggiungi assegnazione ruolo - Aggiungi autorizzazioni per l'entità servizio MS-PIMAdd role assignment pane - Add permissions for MS-PIM service principal](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità servizio MS-PIM viene assegnato il ruolo Amministratore Accesso utenti nell'ambito della sottoscrizione.

   ![Pagina di controllo di accesso che mostra l'assegnazione del ruolo amministratore di accesso utente per l'entità servizio MS-PIMAccess control page showing User access admin role assignment for the MS-PIM service principal](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo di amministratore di Azure AD in Gestione delle identità con privilegiConfigure Azure AD admin role settings in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)