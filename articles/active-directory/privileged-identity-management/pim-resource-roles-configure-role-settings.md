---
title: Configurare le impostazioni dei ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come configurare le impostazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189736"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configurare le impostazioni dei ruoli delle risorse di Azure in PIM

Per configurare le impostazioni dei ruoli, occorre definire le impostazioni predefinite applicate alle assegnazioni nell'ambiente Privileged Identity Management (PIM). Per definire queste impostazioni per la propria risorsa, selezionare la scheda **Impostazioni dei ruoli** nel riquadro sinistro. È anche possibile scegliere il pulsante Impostazioni dei ruoli sulla barra delle azioni (in qualsiasi ruolo) per visualizzare le opzioni correnti.

## <a name="overview"></a>Panoramica

Con il flusso di lavoro di approvazione in Privileged Identity Management (PIM) per i ruoli delle risorse di Azure, gli amministratori possono proteggere ulteriormente o limitare l'accesso alle risorse critiche, ovvero possono richiedere l'approvazione per attivare le assegnazioni di ruolo. 

Il concetto di gerarchia delle risorse è specifico dei ruoli delle risorse di Azure. Questa gerarchia consente l'ereditarietà delle assegnazioni di ruolo da un oggetto risorsa padre verso il basso per tutte le risorse figlio all'interno del contenitore padre. 

Si supponga, ad esempio, che Luca, un amministratore delle risorse, usi PIM per assegnare Alice come membro idoneo al ruolo Proprietario della sottoscrizione di Contoso. Con questa assegnazione, Alice è una proprietaria idonea di tutti i contenitori di gruppi di risorse all'interno della sottoscrizione di Contoso. Alice è anche una proprietaria idonea di tutte le risorse (ad esempio, le macchine virtuali) all'interno di ogni gruppo di risorse della sottoscrizione. 

Si supponga che nella sottoscrizione di Contoso esistano tre gruppi di risorse: Test Fabrikam, Sviluppo Fabrikam e Prod Fabrikam. Ognuno di questi gruppi di risorse contiene una singola macchina virtuale.

Le impostazioni PIM vengono configurate per ogni ruolo di una risorsa. Diversamente dalle assegnazioni, queste impostazioni non vengono ereditate e si applicano esclusivamente al ruolo della risorsa. [Altre informazioni sulle assegnazioni idonee e la visibilità delle risorse](pim-resource-roles-eligible-visibility.md).

Continuando con l'esempio precedente: Luca usa PIM per richiedere che tutti i membri del ruolo Proprietario della sottoscrizione di Contoso richiedano l'approvazione per l'attivazione. Per poter proteggere le risorse nel gruppo di risorse Prod Fabrikam, Luca richiede anche l'approvazione per i membri del ruolo Proprietario per questa risorsa. L'approvazione per l'attivazione non è richiesta per i ruoli Proprietario nei gruppi Test Fabrikam e Sviluppo Fabrikam.

Quando Alice richiede l'attivazione del ruolo Proprietario per la sottoscrizione di Contoso, un responsabile approvazione deve approvare o rifiutare la richiesta prima che lei diventi attiva nel ruolo. Se Alice decide di [limitare l'ambito dell'attivazione](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) al gruppo di risorse Prod Fabrikam, un responsabile approvazione deve approvare o rifiutare anche questa richiesta, ma, se Alice decide di estendere l'ambito dell'attivazione anche a uno o entrambi i gruppi di risorse Test Fabrikam o Sviluppo Fabrikam, l'approvazione non è necessaria.

Il flusso di lavoro di approvazione potrebbe non essere necessario per tutti i membri di un ruolo. Si consideri uno scenario in cui l'organizzazione assume diversi collaboratori a tempo determinato che contribuiscano allo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure. L'amministratore delle risorse vuole che i dipendenti siano idonei all'accesso senza approvazione, ma che per i collaboratori a tempo determinato sia richiesta l'approvazione. Per configurare il flusso di lavoro di approvazione solo per i collaboratori a tempo determinato, è possibile creare un ruolo personalizzato con le stesse autorizzazioni del ruolo assegnato ai dipendenti. È possibile richiedere l'approvazione per attivare il ruolo personalizzato. [Altre informazioni sui ruoli personalizzati](pim-resource-roles-custom-role-policy.md).

Per configurare il flusso di lavoro di approvazione e specificare gli utenti che possono approvare o rifiutare le richieste, usare le procedure seguenti.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

1. Passare a PIM nel portale di Azure e selezionare una risorsa nell'elenco.

   ![Riquadro "Risorse di Azure" con una risorsa selezionata](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. Selezionare **Impostazioni dei ruoli** nel riquadro sinistro.

3. Cercare e selezionare un ruolo e quindi selezionare **Modifica** per modificare le impostazioni.

   ![Pulsante "Modifica" per il ruolo Operatore](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Nella sezione **Attivazione** selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

   ![Sezione "Attivazione" delle impostazioni dei ruoli](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Specificare i responsabili approvazione

Fare clic su **Seleziona responsabili approvazione** per aprire il riquadro **Selezionare un utente o un gruppo**.

>[!NOTE]
>È necessario selezionare almeno un utente o gruppo per aggiornare l'impostazione. Non esistono responsabili approvazione predefiniti.

Gli amministratori delle risorse possono aggiungere qualsiasi combinazione di utenti e gruppi all'elenco dei responsabili approvazione. 

![Riquadro "Selezionare un utente o gruppo" con un utente selezionato](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

La richiesta di approvazione non ha alcun impatto sulla procedura che deve seguire un membro per l'attivazione. [Vedere la procedura per l'attivazione di un ruolo](pim-resource-roles-activate-your-roles.md).

Se un membro richiede l'attivazione di un ruolo per cui è richiesta l'approvazione e il ruolo non è più necessario, il membro può annullare la richiesta in PIM.

Per annullare, passare a PIM e selezionare **Richieste personali**. Individuare la richiesta e selezionare **Annulla**.

![Riquadro "Richieste personali"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'autenticazione MFA (Multi-Factor Authentication) per i ruoli delle risorse di Azure in PIM](pim-resource-roles-require-mfa.md)
- [Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-alerts.md)
