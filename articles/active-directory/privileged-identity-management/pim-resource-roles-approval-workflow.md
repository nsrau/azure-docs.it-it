---
title: Approvare le richieste per i ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021977"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Gestione delle identità con privilegiApprove or deny requests for Azure resource roles in Privileged Identity Management

Con Privileged Identity Management (PIM) in Azure Active Directory (Azure AD), è possibile configurare i ruoli per richiedere l'approvazione per l'attivazione e scegliere gli utenti o i gruppi dell'organizzazione di Azure AD come approvatori delegati. È consigliabile selezionare due o più approvatori per ogni ruolo per ridurre il carico di lavoro per l'amministratore del ruolo con privilegi. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per i ruoli delle risorse di Azure.

## <a name="view-pending-requests"></a>Visualizzare le richieste in sospeso

In qualità di responsabile approvazione con delega si riceverà una notifica di posta elettronica quando una richiesta è in attesa di approvazione. È possibile visualizzare queste richieste in sospeso in Gestione identità privilegiate.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Approva richieste**.

    ![Approvare le richieste - Pagina Risorse di Azure che mostra la richiesta di revisioneApprove requests - Azure resources page showing request to review](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Nella sezione **Requests for role activations** (Richieste di attivazioni di ruoli) verrà visualizzato un elenco di richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approvare le richieste

1. Individuare e selezionare la richiesta che si desidera approvare. Viene visualizzata una pagina di approvazione o rifiuto.

    ![Approva richieste - approva o rifiuta riquadro con dettagli e casella di giustificazione](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Nella casella **Giustificazione** immettere la motivazione aziendale.

1. Selezionare **Approva**. Si riceverà una notifica di Azure dell'approvazione.

    ![Notifica di approvazione che mostra che la richiesta è stata approvata](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- Gli amministratori delle risorse ricevono una notifica quando un utente approvato diventa attivo nel proprio ruolo.

>[!Note]
>Un amministratore delle risorse che ritiene che un utente approvato non debba essere attivo può rimuovere l'assegnazione di ruolo attiva in Gestione identità con privilegi. Anche se gli amministratori delle risorse non vengono informati delle richieste in sospeso a meno che non siano un approvatore, possono visualizzare e annullare le richieste in sospeso per tutti gli utenti visualizzando le richieste in sospeso in Gestione identità con privilegi.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Gestione delle identità con privilegiExtend or renew Azure resource roles in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notifiche di posta elettronica in Gestione identità con privilegi](pim-email-notifications.md)
- [Approvare o rifiutare le richieste per i ruoli di Azure AD in Gestione delle identità con privilegiApprove or deny requests for Azure AD roles in Privileged Identity Management](azure-ad-pim-approval-workflow.md)
