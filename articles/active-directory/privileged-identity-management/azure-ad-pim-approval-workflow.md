---
title: Approvare o rifiutare le richieste per i ruoli di Azure AD in PIM - Azure AD Documenti Microsoft
description: Informazioni su come approvare o rifiutare le richieste per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049008"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Approvare o rifiutare le richieste per i ruoli di Azure AD in Gestione delle identità con privilegiApprove or deny requests for Azure AD roles in Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile configurare i ruoli per richiedere l'approvazione per l'attivazione e scegliere uno o più utenti o gruppi come approvatori delegati. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli di Azure.At for november 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Accedere al [portale](https://portal.azure.com/) di Azure con un utente che risieda il ruolo di amministratore del ruolo Privileged.Sign in to the Azure portal with a user who is in the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role role role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per i ruoli di Azure AD.

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di approvatore delegato, si riceverà una notifica tramite posta elettronica quando una richiesta di ruolo di Azure AD è in attesa di approvazione. È possibile visualizzare queste richieste in sospeso in Gestione identità privilegiate.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Approva richieste**.

    ![Approvare le richieste - pagina che mostra la richiesta di esaminare i ruoli di Azure ADApprove requests - page showing request to review Azure AD roles](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Nella sezione **Requests for role activations** (Richieste di attivazioni di ruoli) verrà visualizzato un elenco di richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Individuare e selezionare la richiesta che si desidera approvare. Viene visualizzata una pagina di approvazione o rifiuto.

    ![Approva richieste - approva o rifiuta riquadro con dettagli e casella di giustificazione](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Nella casella **Giustificazione** immettere la motivazione aziendale.

1. Selezionare **Approva**. Si riceverà una notifica di Azure dell'approvazione.

    ![Notifica di approvazione che mostra che la richiesta è stata approvata](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Rifiutare le richieste

1. Individuare e selezionare la richiesta che si desidera rifiutare. Viene visualizzata una pagina di approvazione o rifiuto.

    ![Approva richieste - approva o rifiuta riquadro con dettagli e casella di giustificazione](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Nella casella **Giustificazione** immettere la motivazione aziendale.

1. Selezionare **Nega**. Viene visualizzata una notifica con la negazione.

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

Ecco alcune informazioni sulle notifiche del flusso di lavoro:

- I responsabili approvazione ricevono una notifica tramite posta elettronica quando una richiesta per un ruolo è in attesa di revisione. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo approvatore che approva o nega.
- Quando un approvatore risponde alla richiesta, tutti gli approvatori ricevono una notifica dell'azione.
- Gli amministratori globali e gli amministratori dei ruoli con privilegi ricevono una notifica quando un utente approvato diventa attivo nel proprio ruolo.

>[!NOTE]
>Un amministratore globale o un amministratore di ruolo con privilegi che ritiene che un utente approvato non deve essere attivo può rimuovere l'assegnazione di ruolo attiva in Gestione identità privilegiate. Anche se gli amministratori non vengono informati delle richieste in sospeso a meno che non siano un approvatore, possono visualizzare e annullare le richieste in sospeso per tutti gli utenti visualizzando le richieste in sospeso in Gestione identità privilegiate.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di approvatore delegato, si riceverà una notifica tramite posta elettronica quando una richiesta di ruolo di Azure AD è in attesa di approvazione. È possibile visualizzare queste richieste in sospeso in Gestione identità privilegiate.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Approva richieste**.

    ![Ruoli di Azure AD - Approvare le richiesteAzure AD roles - Approve requests](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    È possibile visualizzare un elenco delle richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Selezionare le richieste da approvare e quindi fare clic su **Approva** per aprire il riquadro Approva le richieste selezionate.

    ![Approvare l'elenco delle richieste con l'opzione Approva evidenziata](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Digitare un motivo nella casella **Motivo dell'approvazione**.

    ![Riquadro Approva richieste selezionate con un motivo di approvazione](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Fare clic su **Approva**.

    L'icona di stato verrà aggiornata per indicare l'approvazione.

    ![Riquadro Approva richieste selezionate dopo aver fatto clic sul pulsante Approva](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Rifiutare le richieste

1. Selezionare le richieste da rifiutare e quindi fare clic su **Nega** per aprire il riquadro Rifiuta le richieste selezionate.

    ![Approvare l'elenco delle richieste con l'opzione Nega evidenziata](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Digitare un motivo nella casella **Motivo del rifiuto**.

    ![Riquadro Nega richieste selezionate con un motivo di negazione](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selezionare **Nega**.

    L'icona di stato verrà aggiornata per indicare il rifiuto.

---

## <a name="next-steps"></a>Passaggi successivi

- [Notifiche di posta elettronica in Gestione identità con privilegi](pim-email-notifications.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Gestione delle identità con privilegiApprove or deny requests for Azure resource roles in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
