---
title: Assegnare i ruoli delle risorse di Azure in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2850f17c96ef031f9e1b8e11558ab369e4175b0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804292"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Assegnare i ruoli delle risorse di Azure in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) può gestire i ruoli predefiniti delle risorse di Azure, nonché i ruoli personalizzati, tra cui:

- Proprietario
- Amministratore accessi utente
- Collaboratore
- Amministrazione della protezione
- Gestore della sicurezza e altro ancora

> [!NOTE]
> Gli utenti o i membri di un gruppo assegnati ai ruoli Proprietario o Amministratore Accesso utenti e gli amministratori globali che abilitano la gestione delle sottoscrizioni in Azure AD sono amministratori di risorse. Questi amministratori possono assegnare ruoli, configurare le impostazioni dei ruoli e verificare l'accesso usando PIM per le risorse di Azure. Ciò significa che l'account non dispone dei diritti per gestire PIM per le risorse se l'utente non dispone di un ruolo di amministratore delle risorse. Visualizzare l'elenco dei [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Assegnare un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo delle risorse di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Per informazioni su come concedere a un altro accesso di amministratore per la gestione di PIM, vedere [Concedere l'accesso ad altri amministratori per gestire PIM](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure AD Privileged Identity Management**.

    Se PIM non è stato ancora avviato nel portale di Azure, andare al [Iniziare a usare PIM](pim-getting-started.md).

1. Fare clic su **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

    ![Elenco delle risorse di Azure da gestire](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Fare clic sulla risorsa da gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestione fare clic su **Ruoli** per visualizzare l'elenco di ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Fare clic su **Aggiungi membro** per aprire il riquadro Nuova assegnazione.

1. Fare clic su **Selezionare un ruolo** per aprire il riquadro Selezionare un ruolo.

    ![Riquadro Nuova assegnazione](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Fare clic su un ruolo da assegnare e quindi su **Seleziona**.

    Verrà visualizzato il riquadro Selezionare un membro o un gruppo.

1. Fare clic su un membro o un gruppo da assegnare al ruolo e quindi fare clic su **Seleziona**.

    ![Riquadro Selezionare un membro o un gruppo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Verrà aperto il riquadro Impostazioni di appartenenza.

1. Nell'elenco **Tipo di assegnazione** selezionare **Idoneo** o **Attivo**.

    ![Riquadro Impostazioni di appartenenza](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM per le risorse di Azure offre due tipi distinti di assegnazione:

    - Le assegnazioni di tipo **Idoneo** richiedono al membro del ruolo di eseguire un'azione per usare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - Le assegnazioni di tipo **Attivo** non richiedono al membro di eseguire alcuna azione per usare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Se l'assegnazione deve essere permanente (idoneità permanente o con assegnazione permanente), selezionare la casella di controllo **Idoneità permanente**.

    A seconda delle impostazioni dei ruoli, la casella di controllo potrebbero non essere visualizzata o potrebbe essere non modificabile.

1. Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare le caselle relative a data e ora di inizio e/o fine.

    ![Impostazioni di appartenenza a - data e ora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Al termine, fare clic su **Fine**.

    ![Nuova assegnazione - Aggiungi](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Per creare la nuova assegnazione di ruolo, fare clic su **Aggiungi**. Verrà visualizzata una notifica dello stato.

    ![Nuova assegnazione - Notifica](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa da gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestione fare clic su **Ruoli** per visualizzare l'elenco di ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure - Seleziona ruolo](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Fare clic sul ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

    Per informazioni sull'estensione di un'assegnazione di ruolo, vedere [Estendere o rinnovare i ruoli delle risorse di Azure in PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in PIM](pim-resource-roles-renew-extend.md)
- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli Azure AD in PIM](pim-how-to-add-role-to-user.md)
