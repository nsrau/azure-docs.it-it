---
title: Approva le richieste di attivazione per i membri e i proprietari del gruppo in Privileged Identity Management-Azure AD
description: Informazioni su come approvare o negare le richieste per i gruppi assegnabili ai ruoli in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536988"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Approva le richieste di attivazione per i membri e i proprietari del gruppo di accesso con privilegi (anteprima)

Con Privileged Identity Management (PIM) in Azure Active Directory (Azure AD), è possibile configurare membri e proprietari del gruppo di accesso con privilegi per richiedere l'approvazione per l'attivazione e scegliere gli utenti o i gruppi dell'organizzazione Azure AD come responsabili approvazione delegati. Si consiglia di selezionare due o più responsabili approvazione per ogni gruppo per ridurre il carico di lavoro per l'amministratore del ruolo con privilegi. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per i ruoli delle risorse di Azure.

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di responsabile approvazione con delega si riceverà una notifica di posta elettronica quando una richiesta è in attesa di approvazione. È possibile visualizzare le richieste in sospeso in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Approva richieste**.

    ![Richieste di approvazione-pagina delle risorse di Azure che mostra la richiesta di Revisione](./media/groups-approval-workflow/groups-select-request.png)

    Nella sezione **Requests for role activations** (Richieste di attivazioni di ruoli) verrà visualizzato un elenco di richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Trovare e selezionare la richiesta che si desidera approvare e selezionare **approva**.

    ![Screenshot che mostra la pagina "approva richieste" con i pulsanti "approva" e "conferma" evidenziati.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **Conferma**. Una notifica di Azure viene generata dall'approvazione.

## <a name="deny-requests"></a>Rifiutare le richieste

1. Trovare e selezionare la richiesta che si desidera negare e selezionare **Nega**.

    ![Approva le richieste-approva o nega riquadro con i dettagli e la casella di giustificazione](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **Conferma**. Una notifica di Azure viene generata dalla negazione.

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

Ecco alcune informazioni sulle notifiche del flusso di lavoro:

- Gli approvatori ricevono una notifica tramite posta elettronica quando la revisione di una richiesta di assegnazione di un gruppo è in sospeso. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo responsabile approvazione che approva o rifiuta.
- Quando un responsabile approvazione risponde alla richiesta, tutti gli approvatori ricevono una notifica dell'azione.

>[!Note]
>Un amministratore che ritiene che un utente approvato non sia attivo può rimuovere l'assegnazione del gruppo attivo in Privileged Identity Management. Sebbene gli amministratori delle risorse non ricevano notifiche di richieste in sospeso a meno che non siano un responsabile approvazione, possono visualizzare e annullare le richieste in sospeso per tutti gli utenti visualizzando richieste in sospeso in Privileged Identity Management.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare le assegnazioni di gruppo in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md)
- [Approva o rifiuta le richieste per le assegnazioni di gruppo in Privileged Identity Management](azure-ad-pim-approval-workflow.md)
