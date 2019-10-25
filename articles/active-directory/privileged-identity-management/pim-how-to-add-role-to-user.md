---
title: Assegnare ruoli Azure AD in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809208"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Assegnare ruoli di Azure AD in Privileged Identity Management

Con Azure Active Directory (Azure AD), un amministratore globale può effettuare assegnazioni **permanenti** di ruoli di amministratore Azure ad. Queste assegnazioni di ruoli possono essere create usando il [portale di Azure](../users-groups-roles/directory-assign-admin-roles.md) o i [ comandi di PowerShell](/powershell/module/azuread#directory_roles).

Il servizio Azure AD Privileged Identity Management (PIM) consente inoltre agli amministratori dei ruoli con privilegi di effettuare assegnazioni di ruolo di amministratore permanenti. Inoltre, gli amministratori dei ruoli con privilegi possono rendere gli utenti **idonei** per i ruoli di amministratore Azure ad. Un amministratore idoneo può attivare il ruolo quando serve, con autorizzazioni che scadono al termine delle operazioni.

## <a name="make-a-user-eligible-for-a-role"></a>Rendere un utente idoneo per un ruolo

Seguire questa procedura per rendere un utente idoneo per un ruolo di amministratore Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire Privileged Identity Management, vedere [concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure AD Privileged Identity Management**.

    Se non è ancora stato avviato Privileged Identity Management nella portale di Azure, vedere [iniziare a usare Privileged Identity Management](pim-getting-started.md).

1. Selezionare **Azure ad ruoli**.

1. Selezionare **ruoli** o **membri**.

    ![Azure AD ruoli con le opzioni di menu ruoli e membri evidenziato](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selezionare **Aggiungi membro** per aprire Aggiungi membri gestiti.

1. Selezionare **selezionare un ruolo**, selezionare un ruolo che si vuole gestire e quindi selezionare **Seleziona**.

    ![Selezionare un riquadro del ruolo che elenca Azure AD ruoli](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selezionare **Seleziona membri**, selezionare gli utenti che si desidera assegnare al ruolo, quindi selezionare **Seleziona**.

    ![Riquadro Seleziona membri in cui è possibile selezionare un utente](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. In Aggiungi membri gestiti selezionare **OK** per aggiungere l'utente al ruolo.

1. Nell'elenco dei ruoli selezionare il ruolo appena assegnato per visualizzare l'elenco dei membri.

     Quando il ruolo è stato assegnato, l'utente selezionato verrà visualizzato nell'elenco dei membri con il valore **Idoneo** per il ruolo.

    ![I membri di un ruolo sono elencati insieme al relativo stato di attivazione](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ora che l'utente è idoneo per il ruolo, indicare che può attivarlo in base alle istruzioni riportate in [attivare i ruoli di Azure ad in Privileged Identity Management](pim-how-to-activate-role.md).

    Gli amministratori idonei devono registrarsi ad Azure Multi-Factor Authentication (MFA) durante l'attivazione. Se un utente non riesce a registrarsi al servizio Multi-Factor Authentication o usa un account Microsoft, in genere @outlook.com, è necessario rendere l'utente permanente in tutti i relativi ruoli.

## <a name="make-a-role-assignment-permanent"></a>Rendere permanente un'assegnazione di ruolo

Per impostazione predefinita, i nuovi utenti sono idonei solo per un ruolo di amministratore Azure AD. Per rendere permanente un'assegnazione di ruolo, eseguire la procedura seguente:

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Membri**.

    ![Azure AD Roles-elenco membri che mostra lo stato del ruolo e dell'attivazione](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare un ruolo **idoneo** che si desidera rendere permanente.

1. Selezionare **altro** e quindi fare clic su **Rendi Perm**.

    ![Riquadro che elenca un utente idoneo per un ruolo con altre opzioni di menu aperte](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Il ruolo viene elencato come **permanente**.

    ![Elenco dei membri che mostra lo stato di attivazione e di ruolo che ora è permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti dalle assegnazioni di ruoli, ma è necessario assicurarsi che sia sempre presente almeno un utente con ruolo di amministratore globale permanente. Se non si è certi se gli utenti necessitano ancora di assegnazioni di ruoli, è possibile [avviare una verifica di accesso per il ruolo](pim-how-to-start-security-review.md).

Seguire questa procedura per rimuovere un utente specifico da un ruolo di amministratore Azure AD.

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Membri**.

    ![Azure AD Roles-elenco di membri che mostra il ruolo e l'attivazione STAT](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare l'assegnazione di ruolo che si desidera rimuovere.

1. Selezionare **altro** e quindi **Rimuovi**.

    ![Riquadro che elenca un utente con un ruolo permanente con le opzioni di menu altro aperte](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Quando viene richiesto di confermare l'azione, selezionare **Sì**.

    ![Messaggio in cui viene chiesto se si desidera rimuovere il membro dal ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L'assegnazione di ruolo viene rimossa.

## <a name="authorization-error-when-assigning-roles"></a>Errore di autorizzazione durante l'assegnazione di ruoli

Scenario: un proprietario attivo o un amministratore di accesso utente per una risorsa di Azure può visualizzare la risorsa all'interno Privileged Identity Management ma non può eseguire alcuna azione, ad esempio la creazione di un'assegnazione idonea o la visualizzazione di un elenco di assegnazioni di ruolo dal pagina Panoramica risorse. Ognuna di queste azioni comporta un errore di autorizzazione.

Per assegnare i ruoli, all'entità servizio MS-PIM deve essere assegnato il [ruolo amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) nel controllo degli accessi in base al ruolo di Azure per l'accesso alle risorse di Azure (in contrapposizione ai ruoli di amministrazione di Azure ad). Anziché attendere l'assegnazione automatica di questo ruolo a MS-PIM, è possibile assegnarlo manualmente.

La procedura seguente consente di assegnare il ruolo amministratore accesso utenti all'entità servizio MS-PIM per una sottoscrizione.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale nell'organizzazione Azure ad.

1. Scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)** .

1. Scegliere **Assegnazioni di ruolo** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controllare se all'entità servizio **MS-PIM** è assegnato il ruolo **Amministratore Accesso utenti**.

1. In caso negativo, scegliere **Aggiungi un'assegnazione di ruolo** per aprire il riquadro **Aggiungi un'assegnazione di ruolo**.

1. Nell'elenco a discesa **Ruolo** selezionare **Amministratore Accesso utenti**.

1. Nell'elenco **Seleziona** trovare e selezionare l'entità servizio **MS-PIM**.

   ![Riquadro Aggiungi assegnazione ruolo-aggiungere autorizzazioni per l'entità servizio MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità servizio MS-PIM viene assegnato il ruolo Amministratore Accesso utenti nell'ambito della sottoscrizione.

   ![Pannello controllo di accesso (IAM) che mostra l'assegnazione del ruolo amministratore accesso utenti per MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo amministratore Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
