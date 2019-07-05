---
title: Assegnare i ruoli di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1760d0e0bd356a05d84c07eda005e0526da5d13
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476516"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Assegnare i ruoli di Azure AD in PIM

Con Azure Active Directory (Azure AD), un amministratore globale può rendere **permanente** le assegnazioni di ruolo di amministratore di Azure AD. Queste assegnazioni di ruoli possono essere create usando il [portale di Azure](../users-groups-roles/directory-assign-admin-roles.md) o i [ comandi di PowerShell](/powershell/module/azuread#directory_roles).

Il servizio di Azure AD Privileged Identity Management (PIM) consente anche gli amministratori dei ruoli con privilegi rendere le assegnazioni di ruolo di amministratore permanente. Inoltre, gli amministratori dei ruoli con privilegi possono rendere gli utenti **idonei** per i ruoli di amministratore di Azure AD. Un amministratore idoneo può attivare il ruolo quando serve, con autorizzazioni che scadono al termine delle operazioni.

## <a name="make-a-user-eligible-for-a-role"></a>Rendere un utente idoneo per un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo di amministratore di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Per informazioni su come concedere a un altro accesso di amministratore per la gestione di PIM, vedere [Concedere l'accesso ad altri amministratori per gestire PIM](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure AD Privileged Identity Management**.

    Se PIM non è stato ancora avviato nel portale di Azure, andare al [Iniziare a usare PIM](pim-getting-started.md).

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Ruoli** o **Membri**.

    ![Ruoli di Azure AD con i ruoli e membri opzioni di menu evidenziata](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Fare clic su **Aggiungi membro** per aprire Aggiungi membri gestiti.

1. Fare clic su **Selezionare un ruolo**, su un ruolo da gestire e quindi su **Seleziona**.

    ![Selezionare un riquadro ruolo con l'elenco di ruoli di Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Fare clic su **Selezione membri**, selezionare gli utenti da assegnare al ruolo, quindi fare clic su **Seleziona**.

    ![Selezionare il riquadro membri in cui è possibile selezionare un utente](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Nella finestra Aggiungi membri gestiti fare clic su **OK** per aggiungere l'utente al ruolo.

1. Nell'elenco dei ruoli predefiniti fare clic sul ruolo assegnato per visualizzare l'elenco dei membri.

     Quando il ruolo è stato assegnato, l'utente selezionato verrà visualizzato nell'elenco dei membri con il valore **Idoneo** per il ruolo.

    ![I membri di un ruolo sono elencati insieme al relativo stato di attivazione](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ora che l'utente è idoneo per il ruolo, è necessario comunicargli che può attivarlo seguendo le istruzioni in [attiva il mio ruolo di Azure AD in PIM](pim-how-to-activate-role.md).

    Gli amministratori idonei devono registrarsi ad Azure Multi-Factor Authentication (MFA) durante l'attivazione. Se un utente non riesce a registrarsi al servizio Multi-Factor Authentication o usa un account Microsoft, in genere @outlook.com, è necessario rendere l'utente permanente in tutti i relativi ruoli.

## <a name="make-a-role-assignment-permanent"></a>Rendere permanente un'assegnazione di ruolo

Per impostazione predefinita, i nuovi utenti sono idonei solo per un ruolo di amministratore di Azure AD. Per rendere permanente un'assegnazione di ruolo, eseguire la procedura seguente:

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Membri**.

    ![I membri di ruoli di Azure AD - elenco che mostra lo stato di attivazione e ruolo](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Fare clic su un ruolo **idoneo** che si vuole rendere permanente.

1. Fare clic su **Altro**, quindi su **Rendi permanente**.

    ![Riquadro con l'elenco di un utente idoneo per un ruolo con le altre opzioni di menu Apri](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Il ruolo viene elencato come **permanente**.

    ![Elenco dei membri che mostra lo stato di attivazione e ruolo che si trova ora permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti dalle assegnazioni di ruoli, ma è necessario assicurarsi che sia sempre presente almeno un utente con ruolo di amministratore globale permanente. Se non si è certi se gli utenti necessitano ancora di assegnazioni di ruoli, è possibile [avviare una verifica di accesso per il ruolo](pim-how-to-start-security-review.md).

Seguire questi passaggi per rimuovere un utente specifico da un ruolo di amministratore di Azure AD.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Membri**.

    ![Elenco dei membri di ruoli di Azure AD - stat di attivazione e ruolo visualizzazione](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Fare clic su un'assegnazione di ruolo da rimuovere.

1. Fare clic su **Altro** e quindi su **Rimuovi**.

    ![Riquadro con l'elenco di un utente che ha un ruolo permanente con le altre opzioni di menu Apri](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Nel messaggio di conferma fare clic su **Sì**.

    ![Messaggio che chiede se si desidera rimuovere membri dal ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L'assegnazione di ruolo viene rimossa.

## <a name="authorization-error-when-assigning-roles"></a>Errore di autorizzazione durante l'assegnazione di ruoli

Se è stato abilitato di recente PIM per una sottoscrizione e viene visualizzato un errore di autorizzazione quando si tenta di rendere un utente idoneo per un ruolo di amministratore di Azure AD, è possibile che all'entità servizio di MS-PIM non dispone ancora delle autorizzazioni appropriate. Per assegnare ruoli ad altri utenti, l'entità servizio MS-PIM deve avere il ruolo [Amministratore Accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator). Anziché attendere l'assegnazione automatica di questo ruolo a MS-PIM, è possibile assegnarlo manualmente.

Per assegnare il ruolo Amministratore Accesso utenti all'entità servizio MS-PIM per una sottoscrizione, seguire questa procedura.

1. Accedere al portale di Azure come amministratore globale.

1. Scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)** .

1. Scegliere **Assegnazioni di ruolo** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controllare se all'entità servizio **MS-PIM** è assegnato il ruolo **Amministratore Accesso utenti**.

1. In caso negativo, scegliere **Aggiungi un'assegnazione di ruolo** per aprire il riquadro **Aggiungi un'assegnazione di ruolo**.

1. Nell'elenco a discesa **Ruolo** selezionare **Amministratore Accesso utenti**.

1. Nell'elenco **Seleziona** trovare e selezionare l'entità servizio **MS-PIM**.

   ![Aggiungi riquadro di assegnazione di ruolo - aggiungere autorizzazioni per entità di servizio MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità servizio MS-PIM viene assegnato il ruolo Amministratore Accesso utenti nell'ambito della sottoscrizione.

   ![Pannello di controllo (IAM) di accesso che mostra di assegnazione di ruolo per MS-PIM amministratore accesso utenti](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni di ruolo amministratore di Azure AD in PIM](pim-how-to-change-default-settings.md)
- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
