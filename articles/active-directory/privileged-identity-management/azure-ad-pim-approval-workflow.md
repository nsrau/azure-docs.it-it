---
title: Approva o rifiuta le richieste per i ruoli di Azure AD in PIM-Azure AD | Microsoft Docs
description: Informazioni su come approvare o negare le richieste per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 0b39336591e9939d0e5200304cbeced2d9831979
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498760"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Approva o rifiuta le richieste per i ruoli di Azure AD in Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile configurare i ruoli in modo da richiedere l'approvazione per l'attivazione e scegliere uno o più utenti o gruppi come responsabili approvazione delegati. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente appartenente al ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Aprire **Azure AD Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Attenersi alla procedura descritta in questo articolo per approvare o negare le richieste di Azure AD ruoli.

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di responsabile approvazione delegata, si riceverà una notifica di posta elettronica quando una richiesta di ruolo Azure AD è in attesa dell'approvazione. È possibile visualizzare le richieste in sospeso in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Approva richieste**.

    ![Approva richieste-pagina che mostra la richiesta di revisione dei ruoli Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Nella sezione **Requests for role activations** (Richieste di attivazioni di ruoli) verrà visualizzato un elenco di richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Trovare e selezionare la richiesta che si desidera approvare. Viene visualizzata una pagina approva o nega.

    ![Approva le richieste-approva o nega riquadro con i dettagli e la casella di giustificazione](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **approva**. Si riceverà una notifica di Azure relativa all'approvazione.

    ![Approva notifica che indica che la richiesta è stata approvata](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Rifiutare le richieste

1. Trovare e selezionare la richiesta che si desidera negare. Viene visualizzata una pagina approva o nega.

    ![Approva le richieste-approva o nega riquadro con i dettagli e la casella di giustificazione](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **Nega**. Viene visualizzata una notifica con la negazione.

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

Ecco alcune informazioni sulle notifiche del flusso di lavoro:

- Gli approvatori ricevono una notifica tramite posta elettronica quando una richiesta di un ruolo è in attesa di verifica. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo responsabile approvazione che approva o rifiuta.
- Quando un responsabile approvazione risponde alla richiesta, tutti gli approvatori ricevono una notifica dell'azione.
- Gli amministratori globali e gli amministratori dei ruoli con privilegi ricevono una notifica quando un utente approvato diventa attivo nel proprio ruolo.

>[!NOTE]
>Un amministratore globale o un amministratore del ruolo con privilegi che ritiene che un utente approvato non debba essere attivo può rimuovere l'assegnazione di ruolo attiva in Privileged Identity Management. Sebbene gli amministratori non ricevano le richieste in sospeso, a meno che non siano un responsabile approvazione, possono visualizzare e annullare tutte le richieste in sospeso per tutti gli utenti visualizzando richieste in sospeso in Privileged Identity Management.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di responsabile approvazione delegata, si riceverà una notifica di posta elettronica quando una richiesta di ruolo Azure AD è in attesa dell'approvazione. È possibile visualizzare le richieste in sospeso in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Approva richieste**.

    ![Ruoli di Azure AD-approva le richieste](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    È possibile visualizzare un elenco delle richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Selezionare le richieste da approvare e quindi fare clic su **Approva** per aprire il riquadro Approva le richieste selezionate.

    ![Approva l'elenco di richieste con l'opzione approva evidenziata](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Digitare un motivo nella casella **Motivo dell'approvazione**.

    ![Approva il riquadro delle richieste selezionate con un motivo dell'approvazione](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Fare clic su **Approve** (Approva).

    L'icona di stato verrà aggiornata per indicare l'approvazione.

    ![Approva il riquadro delle richieste selezionate dopo aver fatto clic sul pulsante approva](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Rifiutare le richieste

1. Selezionare le richieste da rifiutare e quindi fare clic su **Nega** per aprire il riquadro Rifiuta le richieste selezionate.

    ![Approva elenco richieste con l'opzione Nega evidenziata](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Digitare un motivo nella casella **Motivo del rifiuto**.

    ![Nega il riquadro delle richieste selezionate con un motivo di negazione](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selezionare **Nega**.

    L'icona di stato verrà aggiornata per indicare il rifiuto.

---

## <a name="next-steps"></a>Passaggi successivi

- [Notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md)
- [Approva o rifiuta le richieste per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
