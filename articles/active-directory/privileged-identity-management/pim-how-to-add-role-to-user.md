---
title: Assegnare ruoli di directory agli utenti che usano Azure AD Privileged Identity Management | Microsoft Docs
description: Informazioni su come assegnare ruoli di directory agli utenti che usano Azure Active Directory Privileged Identity Management e il portale di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621814"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Assegnare ruoli di directory agli utenti che usano Azure AD PIM

Con Azure Active Directory (Azure AD) un amministratore globale può rendere le assegnazioni di ruoli di directory **permanenti**. Queste assegnazioni di ruolo possono essere create usando il [portale di Azure](../users-groups-roles/directory-assign-admin-roles.md) o i [ comandi di PowerShell](/powershell/module/azuread#directory_roles).

Il servizio Azure AD Privileged Identity Management (PIM) consente anche agli amministratori di ruoli con privilegi di effettuare assegnazioni di ruoli di directory permanenti. Inoltre, gli amministratori di ruoli con privilegi possono rendere gli utenti **idonei** ai ruoli di directory. Un amministratore idoneo può attivare il ruolo quando serve, con autorizzazioni che scadono al termine delle operazioni. Per informazioni sui ruoli che è possibile gestire usando PIM, vedere [Ruolo amministrativo differente in Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Rendere un utente idoneo per un ruolo

Per rendere un utente idoneo per un ruolo di directory di Azure AD, eseguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Per informazioni su come concedere a un altro utente l'accesso per la gestione di PIM, vedere [Come concedere l'accesso a PIM](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure AD Privileged Identity Management**.

    Se ancora non è stato abilitato il servizio PIM nel portale di Azure, vedere [Introduzione ad Azure AD Privileged Identity Management](pim-getting-started.md).

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Ruolo (anteprima)** o su **Membri**.

    ![Ruoli della directory di Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Fare clic su **Aggiungi membro** per aprire Aggiungi membri gestiti.

1. Fare clic su **Selezionare un ruolo**, su un ruolo da gestire e quindi su **Seleziona**.

    ![Selezionare un ruolo](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Fare clic su **Selezione membri**, selezionare gli utenti da assegnare al ruolo, quindi fare clic su **Seleziona**.

    ![Selezionare un ruolo](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Nella finestra Aggiungi membri gestiti fare clic su **OK** per aggiungere l'utente al ruolo.

     Quando il ruolo è stato assegnato, l'utente selezionato verrà visualizzato nell'elenco dei membri come **idoneo** per il ruolo.

    ![Utente idoneo per un ruolo](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ora che l'utente è idoneo per il ruolo, è necessario comunicargli che può attivarlo in base alle istruzioni fornite in [Come attivare o disattivare un ruolo](pim-how-to-activate-role.md).

    Gli amministratori idonei devono registrarsi ad Azure Multi-Factor Authentication (MFA) durante l'attivazione. Se un utente non riesce a registrarsi al servizio Multi-Factor Authentication o usa un account Microsoft, in genere @outlook.com, è necessario rendere l'utente permanente in tutti i relativi ruoli.

## <a name="make-a-role-assignment-permanent"></a>Rendere permanente un'assegnazione di ruolo

Per impostazione predefinita, i nuovi utenti sono idonei solo per un ruolo della directory. Per rendere permanente un'assegnazione di ruolo, eseguire la procedura seguente:

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Fare clic su un ruolo **idoneo** che si vuole rendere permanente.

1. Fare clic su **Altro**, quindi su **Rendi permanente**.

    ![Rendere permanente l'assegnazione di ruolo](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Il ruolo viene elencato come **permanente**.

    ![Elenco di membri con modifica permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti dalle assegnazioni di ruoli, ma è necessario assicurarsi che sia sempre presente almeno un utente con ruolo di amministratore globale permanente. Se non si è certi se gli utenti necessitano ancora di assegnazioni di ruoli, è possibile [avviare una verifica di accesso per il ruolo](pim-how-to-start-security-review.md).

Per rimuovere un utente specifico da un ruolo della directory, eseguire la procedura seguente:

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Fare clic su un'assegnazione di ruolo da rimuovere.

1. Fare clic su **Altro** e quindi su **Rimuovi**.

    ![Rimuovere un ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Nel messaggio di conferma fare clic su **Sì**.

    ![Rimuovere un ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L'assegnazione di ruolo viene rimossa.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
