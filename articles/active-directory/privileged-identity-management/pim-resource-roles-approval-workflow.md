---
title: Privileged Identity Management - Flusso di lavoro di approvazione per i ruoli delle risorse di Azure| Microsoft Docs
description: Descrive il processo del flusso di lavoro di approvazione per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - Ruoli delle risorse - Approvazione

Con il flusso di lavoro di approvazione in PIM per i ruoli delle risorse di Azure, gli amministratori possono proteggere ulteriormente o limitare l'accesso alle risorse critiche richiedendo l'approvazione per attivare le assegnazioni di ruolo. Il concetto di gerarchia delle risorse è specifico dei ruoli delle risorse di Azure. Questa gerarchia consente l'ereditarietà delle assegnazioni di ruolo da un oggetto risorsa padre verso il basso per tutte le risorse figlio subordinate all'interno del contenitore padre. 

Si supponga, ad esempio, che Luca, un amministratore delle risorse, usi PIM per assegnare Alice come membro idoneo al ruolo Proprietario della sottoscrizione di Contoso. Con questa assegnazione Alice diventa anche un proprietario idoneo di tutti i contenitori di gruppi di risorse all'interno della sottoscrizione di Contoso e di tutte le risorse (ad esempio, le macchine virtuali) contenute all'interno di ogni gruppo di risorse, della sottoscrizione. Si supponga che nella sottoscrizione di Contoso esistano tre gruppi di risorse: Test Fabrikam, Sviluppo Fabrikam e Prod Fabrikam. Ognuno di questi gruppi di risorse contiene una singola macchina virtuale.

Le impostazioni di PIM vengono configurate per ogni ruolo di una risorsa e, diversamente dalle assegnazioni, queste impostazioni non vengono ereditate e si applicano esclusivamente al ruolo della risorsa. [Altre informazioni sulle assegnazioni idonee e la visibilità delle risorse.](pim-resource-roles-eligible-visibility.md)

Riprendendo l'esempio precedente, Luca usa PIM per richiedere che tutti i membri del ruolo Proprietario della sottoscrizione di Contoso richiedano l'approvazione per l'attivazione. Per proteggere le risorse contenute nel gruppo di risorse Prod Fabrikam, Luca richiede l'approvazione per i membri del ruolo Proprietario anche per questa risorsa. L'approvazione per l'attivazione non è richiesta per i ruoli Proprietario nei gruppi Test Fabrikam e Sviluppo Fabrikam.

Quando Alice richiede l'attivazione del ruolo Proprietario per la sottoscrizione di Contoso, un responsabile approvazione deve approvare o rifiutare la richiesta prima che lei diventi attiva nel ruolo. Inoltre, se Alice decide di [limitare l'ambito dell'attivazione](pim-resource-roles-activate-your-roles.md#just-enough-administration) al gruppo di risorse Prod Fabrikam, un responsabile approvazione deve approvare o rifiutare anche questa richiesta. Tuttavia, se Alice decide di estendere l'ambito dell'attivazione anche a uno o entrambi i gruppi di risorse Test Fabrikam o Sviluppo Fabrikam, l'approvazione non sarà necessaria.

Il flusso di lavoro di approvazione potrebbe non essere necessario per tutti i membri di un ruolo. Si consideri uno scenario in cui l'organizzazione assume diversi collaboratori a tempo determinato che contribuiscano allo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure. L'amministratore delle risorse vuole che i dipendenti siano idonei all'accesso senza approvazione, ma che per i collaboratori a tempo determinato sia richiesta l'approvazione. Per configurare il flusso di lavoro di approvazione solo per i collaboratori a tempo determinato, è possibile creare un ruolo personalizzato con le stesse autorizzazioni del ruolo assegnato ai dipendenti e richiedere l'approvazione per attivare il ruolo personalizzato. [Altre informazioni sui ruoli personalizzati](pim-resource-roles-custom-role-policy.md).

Seguire questa procedura per configurare il flusso di lavoro di approvazione e specificare gli utenti che possono approvare o rifiutare le richieste.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Passare a PIM nel portale di Azure e selezionare una risorsa nell'elenco.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

Dal menu di spostamento a sinistra selezionare **Impostazioni dei ruoli**.

Cercare e selezionare un ruolo, quindi fare clic su **Modifica** per modificare le impostazioni.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Nella sezione Attivazione selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Specificare i responsabili approvazione

Fare clic su **Seleziona responsabili approvazione** per aprire la schermata di selezione.

>[!NOTE]
>È necessario selezionare almeno un utente o gruppo per aggiornare l'impostazione. Non esistono responsabili approvazione predefiniti.

Gli amministratori delle risorse possono aggiungere qualsiasi combinazione di utenti e gruppi all'elenco dei responsabili approvazione. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

La richiesta di approvazione non ha alcun impatto sulla procedura che deve seguire un membro per l'attivazione. [Vedere la procedura per l'attivazione di un ruolo](pim-resource-roles-activate-your-roles.md).

Se un membro richiede l'attivazione di un ruolo per cui è richiesta l'approvazione e il ruolo non è più necessario, il membro può annullare la richiesta in PIM.

Per annullare, passare a PIM e selezionare "Richieste personali". Individuare la richiesta e fare clic su "Annulla".

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Approvare o rifiutare una richiesta

Per approvare o rifiutare una richiesta, è necessario essere un membro dell'elenco dei responsabili approvazione. In PIM selezionare "Approva richieste" dalla scheda nel menu di spostamento a sinistra e individuare la richiesta.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Selezionare la richiesta, fornire una giustificazione per la decisione e selezionare Approva o Rifiuta, risolvendo così la richiesta.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notifiche del flusso di lavoro

- Tutti i membri dell'elenco di responsabili approvazione ricevono una notifica tramite posta elettronica quando una richiesta per un ruolo è in attesa di revisione. Le notifiche tramite posta elettronica includono un collegamento diretto alla richiesta, dove il responsabile approvazione può approvare o rifiutare.
- Le richieste vengono risolte dal primo membro dell'elenco che approva o rifiuta. 
- Quando un responsabile approvazione risponde alla richiesta, tutti i membri dell'elenco di responsabili approvazione ricevono notifica dell'azione. 
- Gli amministratori delle risorse ricevono notifica quando un membro approvato diventa attivo nel ruolo. 

>[!Note]
>Nel caso un amministratore delle risorse ritenga che il membro approvato non deve essere attivo, può rimuovere l'assegnazione di ruolo attivo in PIM. Anche se gli amministratori delle risorse non ricevono notifica delle richieste in sospeso a meno che non siano membri dell'elenco di responsabili approvazione, essi possono visualizzare e annullare le richieste in sospeso di tutti gli utenti visualizzandole in PIM. 

## <a name="next-steps"></a>Passaggi successivi

[Applicare le impostazioni di PIM a gruppi univoci di utenti](pim-resource-roles-custom-role-policy.md)
