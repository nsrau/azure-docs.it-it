---
title: Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189805"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM

Per approvare o rifiutare una richiesta, è necessario essere un membro dell'elenco dei responsabili approvazione. 

1. In PIM selezionare **Approva richieste** dalla scheda nel menu a sinistra e individuare la richiesta.

   ![Riquadro "Approva richieste"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Selezionare la richiesta, fornire una giustificazione per la decisione e selezionare **Approva** o **Rifiuta**. La richiesta viene quindi risolta.

   ![Richiesta selezionata con informazioni dettagliate](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

Ecco alcuni aspetti delle notifiche del flusso di lavoro:

- Tutti i membri dell'elenco di responsabili approvazione ricevono una notifica tramite posta elettronica quando una richiesta per un ruolo è in attesa di revisione. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo membro dell'elenco che approva o rifiuta. 
- Quando un responsabile approvazione risponde alla richiesta, tutti i membri dell'elenco di responsabili approvazione ricevono notifica dell'azione. 
- Gli amministratori delle risorse ricevono notifica quando un membro approvato diventa attivo nel ruolo. 

>[!Note]
>Un amministratore delle risorse che ritiene che un membro approvato non deve essere attivo può rimuovere l'assegnazione di ruolo attivo in PIM. Anche se gli amministratori delle risorse non ricevono notifica delle richieste in sospeso a meno che non siano membri dell'elenco di responsabili approvazione, essi possono visualizzare e annullare le richieste in sospeso di tutti gli utenti visualizzandole in PIM. 

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste per i ruoli della directory di Azure AD in PIM](azure-ad-pim-approval-workflow.md)
- [Notifiche tramite posta elettronica in PIM](pim-email-notifications.md)
