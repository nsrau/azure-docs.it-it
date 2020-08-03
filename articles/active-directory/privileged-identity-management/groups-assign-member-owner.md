---
title: Assegnare proprietari e membri idonei per i gruppi di accesso con privilegi-Azure Active Directory
description: Informazioni su come assegnare i proprietari idonei o i membri di un gruppo assegnabile al ruolo in Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1dcc98366e37455f462fe1a0740aa161201912f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506225"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Assegnare idoneità per un gruppo di accesso con privilegi (anteprima) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire l'idoneità e l'attivazione di assegnazioni ai gruppi di accesso con privilegi in Azure AD. È possibile assegnare l'idoneità a membri o proprietari del gruppo.

>[!NOTE]
>Ogni utente idoneo per l'appartenenza o la proprietà di un gruppo di accesso con privilegi deve disporre di una licenza Azure AD Premium P2. Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Assegnare un proprietario o un membro di un gruppo

Attenersi alla seguente procedura per rendere un utente idoneo a essere un membro o un proprietario di un gruppo di accesso con privilegi.

1. Accedere a [Privileged Identity Management](https://portal.azure.com/) nel portale di Azure con autorizzazioni ruolo [amministratore ruolo con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire Privileged Identity Management, vedere [concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **accesso con privilegi (anteprima)**.

1. È possibile cercare un nome di gruppo e usare il **tipo di gruppo** per filtrare l'elenco per selezionare il gruppo che si vuole gestire.

    ![Elenco di gruppi di accesso con privilegi da gestire](./media/groups-assign-member-owner/privileged-access-list.png)

1. In **Gestisci**selezionare **assegnazioni**.

1. Selezionare **Aggiungi assegnazioni**.

    ![Riquadro Nuova assegnazione](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selezionare i membri o i proprietari che si desidera rendere idonei per il gruppo di accesso con privilegi.

    ![Riquadro Selezionare un membro o un gruppo](./media/groups-assign-member-owner/add-assignments.png)

1. Selezionare **Avanti** per impostare la durata dell'appartenenza o della proprietà.

    ![Riquadro Selezionare un membro o un gruppo](./media/groups-assign-member-owner/assignment-duration.png)

1. Nell'elenco **Tipo di assegnazione** selezionare **Idoneo** o **Attivo**. I gruppi di accesso con privilegi forniscono due tipi di assegnazione distinti:

    - Le assegnazioni **idonee** richiedono che il membro del ruolo esegua un'azione per usare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - Le assegnazioni **attive** non richiedono che il membro esegua alcuna azione per usare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Se l'assegnazione deve essere permanente (idonea o assegnata in modo permanente), selezionare la casella di controllo **permanente** . A seconda delle impostazioni dell'organizzazione, la casella di controllo potrebbe non essere visualizzata o potrebbe non essere modificabile.

1. Al termine, selezionare **assegna**.

1. Per creare la nuova assegnazione di ruolo, selezionare **Aggiungi**. Verrà visualizzata una notifica dello stato.

    ![Nuova assegnazione - Notifica](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **accesso con privilegi (anteprima)**.

1. È possibile cercare un nome di gruppo e usare il **tipo di gruppo** per filtrare l'elenco per selezionare il gruppo che si vuole gestire.

    ![Elenco di gruppi di accesso con privilegi da gestire](./media/groups-assign-member-owner/privileged-access-list.png)

1. In **Gestisci**selezionare **assegnazioni**.

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

    Per informazioni sull'estensione di un'assegnazione di ruolo, vedere [estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
