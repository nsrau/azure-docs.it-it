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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534421"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Assegnare idoneità per un gruppo di accesso con privilegi (anteprima) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire l'idoneità e l'attivazione di assegnazioni ai gruppi di accesso con privilegi in Azure AD. È possibile assegnare l'idoneità a membri o proprietari del gruppo.

>[!NOTE]
>Ogni utente idoneo per l'appartenenza o la proprietà di un gruppo di accesso con privilegi deve disporre di una licenza Azure AD Premium P2. Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Assegnare un proprietario o un membro di un gruppo

Attenersi alla seguente procedura per rendere un utente idoneo a essere un membro o un proprietario di un gruppo di accesso con privilegi.

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore globale o proprietario del gruppo.
1. Selezionare **gruppi** e quindi selezionare il gruppo assegnabile al ruolo che si desidera gestire. È possibile cercare o filtrare l'elenco.

    ![trovare un gruppo assegnabile ai ruoli da gestire in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Aprire il gruppo e selezionare **accesso con privilegi (anteprima)**.

    ![Apri l'esperienza Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selezionare **Aggiungi assegnazioni**.

    ![Riquadro Nuova assegnazione](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selezionare i membri o i proprietari che si desidera rendere idonei per il gruppo di accesso con privilegi.

    ![Screenshot che mostra la pagina "Aggiungi assegnazioni" con il riquadro "selezionare un membro o un gruppo" aperto e il pulsante "Seleziona" evidenziato.](./media/groups-assign-member-owner/add-assignments.png)

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

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore globale o proprietario del gruppo.
1. Selezionare **gruppi** e quindi selezionare il gruppo assegnabile al ruolo che si desidera gestire. È possibile cercare o filtrare l'elenco.

    ![trovare un gruppo assegnabile ai ruoli da gestire in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Aprire il gruppo e selezionare **accesso con privilegi (anteprima)**.

    ![Apri l'esperienza Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

    Per informazioni sull'estensione di un'assegnazione di ruolo, vedere [estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
