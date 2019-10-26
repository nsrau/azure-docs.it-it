---
title: Approva o rifiuta le richieste per i ruoli delle risorse di Azure in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: c436a529ebaf15a3024f935c1b28327230da0ac0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895891"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Approva o rifiuta le richieste per i ruoli delle risorse di Azure in Privileged Identity Management

Con Privileged Identity Management (PIM) in Azure Active Directory (Azure AD), è possibile configurare i ruoli in modo da richiedere l'approvazione per l'attivazione e scegliere gli utenti o i gruppi dell'organizzazione Azure AD come responsabili approvazione delegati. Si consiglia di selezionare due o più responsabili approvazione per ogni ruolo per ridurre il carico di lavoro per l'amministratore del ruolo con privilegi. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per i ruoli delle risorse di Azure.

## <a name="view-pending-requests"></a>Visualizzazione delle richieste in sospeso

In qualità di responsabile approvazione con delega si riceverà una notifica di posta elettronica quando una richiesta è in attesa di approvazione. È possibile visualizzare le richieste in sospeso in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Approva richieste**.

    ![Richieste di approvazione-pagina delle risorse di Azure che mostra la richiesta di Revisione](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Nella sezione **Requests for role activations** (Richieste di attivazioni di ruoli) verrà visualizzato un elenco di richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Trovare e selezionare la richiesta che si desidera approvare. Viene visualizzata una pagina approva o nega.

    ![Approva le richieste-approva o nega riquadro con i dettagli e la casella di giustificazione](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **approva**. Si riceverà una notifica di Azure relativa all'approvazione.

    ![Approva notifica che indica che la richiesta è stata approvata](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negare le richieste

1. Trovare e selezionare la richiesta che si desidera negare. Viene visualizzata una pagina approva o nega.

    ![Approva le richieste-approva o nega riquadro con i dettagli e la casella di giustificazione](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Immettere la giustificazione aziendale nella casella **giustificazione** .

1. Selezionare **Nega**. Viene visualizzata una notifica con la negazione.

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

Ecco alcune informazioni sulle notifiche del flusso di lavoro:

- Gli approvatori ricevono una notifica tramite posta elettronica quando una richiesta di un ruolo è in attesa di verifica. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo responsabile approvazione che approva o rifiuta.
- Quando un responsabile approvazione risponde alla richiesta, tutti gli approvatori ricevono una notifica dell'azione.
- Gli amministratori delle risorse ricevono una notifica quando un utente approvato diventa attivo nel proprio ruolo.

>[!Note]
>Un amministratore di risorse che ritiene che un utente approvato non sia attivo può rimuovere l'assegnazione di ruolo attiva in Privileged Identity Management. Sebbene gli amministratori delle risorse non ricevano notifiche di richieste in sospeso a meno che non siano un responsabile approvazione, possono visualizzare e annullare le richieste in sospeso per tutti gli utenti visualizzando richieste in sospeso in Privileged Identity Management.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md)
- [Approva o rifiuta le richieste per i ruoli di Azure AD in Privileged Identity Management](azure-ad-pim-approval-workflow.md)
