---
title: Assegnare i ruoli delle risorse di Azure in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 985342b19baad8b9210e985c9c7dfb9482708a0c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023772"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Assegnare i ruoli delle risorse di Azure in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) può gestire i ruoli predefiniti delle risorse di Azure, nonché i ruoli personalizzati, tra cui:

- Proprietario
- Amministratore accessi utente
- Collaboratore
- Amministrazione della protezione
- Gestore della sicurezza SQL

> [!NOTE]
> Utenti o membri di un gruppo assegnato ai ruoli di sottoscrizione di proprietario o amministratore accesso utenti e Azure AD amministratori globali che abilitano la gestione delle sottoscrizioni in Azure AD hanno le autorizzazioni di amministratore delle risorse per impostazione predefinita. Questi amministratori possono assegnare ruoli, configurare le impostazioni dei ruoli ed esaminare l'accesso usando Privileged Identity Management per le risorse di Azure. Un utente non può gestire Privileged Identity Management per le risorse senza autorizzazioni di amministratore delle risorse. Visualizzare l'elenco dei [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Assegnare un ruolo

Seguire questi passaggi per rendere un utente idoneo per un ruolo delle risorse di Azure.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire Privileged Identity Management, vedere [concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Usare il filtro delle risorse per trovare le risorse gestite che si sta cercando.

    ![Elenco delle risorse di Azure da gestire](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selezionare la risorsa che si vuole gestire per aprire la pagina Panoramica risorse.

1. In **Gestisci**selezionare **ruoli** per visualizzare l'elenco dei ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selezionare **Aggiungi assegnazioni** per aprire il riquadro **Aggiungi assegnazioni** .

1. Selezionare **selezionare un ruolo** per aprire la pagina **selezionare un ruolo** .

    ![Riquadro Nuova assegnazione](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selezionare un ruolo da assegnare e quindi fare clic su **Seleziona**.

    Viene visualizzato il riquadro **selezionare un membro o un gruppo** .

1. Selezionare un membro o un gruppo che si desidera assegnare al ruolo, quindi fare clic su **Seleziona**.

    ![Riquadro Selezionare un membro o un gruppo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Nella scheda **Impostazioni** selezionare **idoneo** o **attivo**nell'elenco **tipo di assegnazione** .

    ![Riquadro Impostazioni di appartenenza](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management per le risorse di Azure fornisce due tipi di assegnazione distinti:

    - Le assegnazioni **idonee** richiedono che il membro del ruolo esegua un'azione per usare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - Le assegnazioni **attive** non richiedono che il membro esegua alcuna azione per usare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Per specificare una durata di assegnazione specifica, modificare le date e le ore di inizio e di fine.

1. Al termine, selezionare **assegna**.

1. Una volta creata la nuova assegnazione di ruolo, viene visualizzata una notifica di stato.

    ![Nuova assegnazione - Notifica](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Selezionare la risorsa che si vuole gestire per aprire la relativa pagina di panoramica.

1. In **Gestisci**selezionare **ruoli** per visualizzare l'elenco dei ruoli per le risorse di Azure.

    ![Ruoli delle risorse di Azure - Seleziona ruolo](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

    Per informazioni sull'estensione di un'assegnazione di ruolo, vedere [estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
