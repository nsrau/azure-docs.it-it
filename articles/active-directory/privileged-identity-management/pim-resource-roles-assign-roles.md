---
title: Assegnare ruoli delle risorse di Azure in Gestione identità con privilegi - Azure Active Directory Documenti Microsoft
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266559"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) può gestire i ruoli delle risorse di Azure predefiniti, nonché i ruoli personalizzati, inclusi (ma non limitati a):Azure Active Directory (Azure AD) Privileged Identity Management (PIM) can manage the built-in Azure resource roles, as well as custom roles, including (but not limited to):

- Proprietario
- Amministratore accessi utente
- Collaboratore
- Amministrazione della protezione
- Gestore della sicurezza SQL

> [!NOTE]
> Gli utenti o i membri di un gruppo assegnati ai ruoli di sottoscrizione Proprietario o Amministratore di accesso utenti e gli amministratori globali di Azure AD che abilitano la gestione delle sottoscrizioni in Azure AD dispongono delle autorizzazioni di amministratore delle risorse per impostazione predefinita. Questi amministratori possono assegnare ruoli, configurare le impostazioni dei ruoli e rivedere l'accesso usando la gestione delle identità con privilegi per le risorse di Azure.These administrators can assign roles, configure role settings, and review access using Privileged Identity Management for Azure resources. Un utente non può gestire La gestione delle identità con privilegi per le risorse senza le autorizzazioni di amministratore delle risorse. Visualizzare l'elenco dei [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Assegnare un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo delle risorse di Azure.

1. Accedere al portale di [Azure](https://portal.azure.com/) con un utente membro del ruolo Amministratore ruolo [con privilegi.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire la gestione delle identità con privilegi, vedere [Concedere l'accesso ad altri amministratori per gestire La gestione delle identità con privilegi](pim-how-to-give-access-to-pim.md).

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

    ![Elenco delle risorse di Azure da gestire](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selezionare la risorsa che si desidera gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestisci selezionare Ruoli per visualizzare l'elenco dei ruoli per le risorse di Azure.Under Manage, select **Roles** to see the list of roles for Azure resources.

    ![Ruoli delle risorse di Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selezionare **Aggiungi membro** per aprire il riquadro Nuova assegnazione.

1. Selezionare **Selezionare un ruolo** per aprire il riquadro Selezionare un ruolo.

    ![Riquadro Nuova assegnazione](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selezionare un ruolo da assegnare e quindi fare clic su **Seleziona**.

    Verrà visualizzato il riquadro Selezionare un membro o un gruppo.

1. Selezionare un membro o un gruppo che si desidera assegnare al ruolo e quindi fare clic su **Seleziona**.

    ![Riquadro Selezionare un membro o un gruppo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Verrà aperto il riquadro Impostazioni di appartenenza.

1. Nell'elenco **Tipo di assegnazione** selezionare **Idoneo** o **Attivo**.

    ![Riquadro Impostazioni di appartenenza](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    La gestione delle identità con privilegi per le risorse di Azure offre due tipi di assegnazione distinti:Privileged Identity Management for Azure resources provides two distinct assignment types:

    - Le assegnazioni **idonee** richiedono al membro del ruolo di eseguire un'azione per utilizzare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - **Le** assegnazioni attive non richiedono al membro di eseguire alcuna azione per utilizzare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Se l'assegnazione deve essere permanente (permanentemente idonea o assegnata in modo permanente), selezionare la casella di controllo **Permanente.**

    A seconda delle impostazioni dei ruoli, la casella di controllo potrebbero non essere visualizzata o potrebbe essere non modificabile.

1. Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare le caselle relative a data e ora di inizio e/o fine.

    ![Impostazioni di appartenenza a - data e ora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Al termine, selezionare **Fine**.

    ![Nuova assegnazione - Aggiungi](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Per creare la nuova assegnazione di ruolo, selezionare **Aggiungi**. Verrà visualizzata una notifica dello stato.

    ![Nuova assegnazione - Notifica](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa che si desidera gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestisci selezionare Ruoli per visualizzare l'elenco dei ruoli per le risorse di Azure.Under Manage, select **Roles** to see the list of roles for Azure resources.

    ![Ruoli delle risorse di Azure - Seleziona ruolo](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

    Per informazioni sull'estensione di un'assegnazione di ruolo, vedere Estendere o rinnovare i ruoli delle risorse di [Azure in Gestione identità con privilegi.](pim-resource-roles-renew-extend.md)

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Gestione delle identità con privilegiExtend or renew Azure resource roles in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
