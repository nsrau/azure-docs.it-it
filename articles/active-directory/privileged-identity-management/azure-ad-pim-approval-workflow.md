---
title: Flussi di lavoro di approvazione di Azure Privileged Identity Management | Documentazione Microsoft
description: Informazioni sui flussi di lavoro di approvazione di Privileged Identity Management (PIM)
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="approvals-preview"></a>Approvazioni (Anteprima)

## <a name="overview"></a>Panoramica

Con Approvazioni per Privileged Identity Management, è possibile configurare ruoli per richiedere l’approvazione per l’attivazione, e scegliere uno o più utenti o gruppi come responsabili approvazione con delega. Continuare a leggere per scoprire come configurare i ruoli e selezionare i responsabili approvazione.

>[!NOTE]
Tenere presente che questa funzionalità è ancora in fase di sviluppo e che è possibile che si verifichino degli errori. La funzionalità, inclusi il testo e le convenzioni di denominazione, sono soggetti a modifica e non devono essere considerati finali.


## <a name="key-terminology"></a>Terminologia chiave

*Eligible Role User (Utente con ruolo idoneo)*: un utente con ruolo idoneo è un utente dell’organizzazione assegnato come idoneo a un ruolo Azure AD (ruolo che richiede l’attivazione).

*Delegated Approver (Responsabile approvazione con delega)*: un responsabile approvazione con delega è una o più persone o gruppo all’interno di Azure AD che sono responsabili dell’approvazione delle richieste di l’attivazione del ruolo.

## <a name="scenarios"></a>Scenari

L’anteprima privata supporta i seguenti scenari:

**Come Amministratore dei ruoli con privilegi (PRA), è possibile:**

-   [Abilitare l’approvazione per ruoli specifici](#enable-approval-for-specific-roles)

-   [Specificare gli utenti e/o i gruppi approvatori per l’approvazione delle richieste](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Visualizzare la cronologia delle richieste e delle approvazioni per tutti i ruoli con privilegi](#view-request-and-approval-history-for-all-privileged-roles)

**Come responsabile approvazione designato, è possibile:**

-   [Visualizzare le approvazioni (richieste) in sospeso](#view-pending-approvals-requests)

-   [Approvare o rifiutare le richieste di elevazione del ruolo (singolarmente e/o in blocco)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Fornire una giustificazione per l’approvazione/il rifiuto](#provide-justification-for-my-approval/rejection) 

**Come Eligible Role User (Utente con ruolo idoneo), è possibile:**

-   [Richiedere l’attivazione di un ruolo che richiede approvazione](#request-activation-of-a-role-that-requires-approval)

-   [Visualizzare lo stato della richiesta da attivare](#view-the-status-of-your-request-to-activate)

-   [Completare l’attività in Azure AD se l’attivazione è stata approvata](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigazione

La navigazione è stata aggiornata per supportare le approvazioni

![](media/azure-ad-pim-approval-workflow/image001.png)

La pagina di destinazione predefinita fornisce un pratico accesso alle informazioni su PIM e alla nuova documentazione sulle approvazioni.

![](media/azure-ad-pim-approval-workflow/image002.png)

È stata aggiunta anche una nuova sezione per tutti gli utenti di PIM: ’My Audit History’ (Cronologia delle approvazioni personali). In questa sezione e è possibile trovare tutte le informazioni relative alla propria identità. Tutte le richieste in sospeso e completate, qualsiasi decisione presa sulle richieste risolte, e tutte le attivazioni precedenti dei ruoli sono incluse in un’unica pratica posizione.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Abilitare l’approvazione per ruoli specifici

Per abilitare l’approvazione per un ruolo specifico, selezionare prima Ruoli della directory nel riquadro di spostamento sinistro.

![](media/azure-ad-pim-approval-workflow/image004.png)

Cercare e selezionare le impostazioni nel riquadro di spostamento sinistro Ruoli della directory.

![](media/azure-ad-pim-approval-workflow/image006.png)

Selezionare i ruoli privilegiati:

![](media/azure-ad-pim-approval-workflow/image009.png)

Selezionare “Abilita” nella sezione Richiedi approvazione:

![](media/azure-ad-pim-approval-workflow/image011.png)

Una volta abilitato, il pannello si espande per mostrare i seguenti dettagli:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Se NON si specificano responsabili approvazione, i PRA diventano i responsabili approvazione predefiniti. Ai PRA verrà richiesto di approvare TUTTE le richieste di attivazione per il ruolo.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Specificare utenti e/o gruppi approvatori per approvare le richieste

Per delegare l’approvazione, fare clic sull’opzione “Selezionare responsabili approvazione”:

![](media/azure-ad-pim-approval-workflow/image015.png)

Quando viene caricato il pannello Selezionare responsabili approvazione, è possibile cercare un utente o un gruppo specifico utilizzando la barra di ricerca in alto, oppure effettuare una selezione dall’elenco già popolato, quindi fare clic su “Seleziona” al termine:

![](media/azure-ad-pim-approval-workflow/image017.png)

Nota: è possibile selezionare contemporaneamente più utenti o gruppi.

La selezione viene visualizzata nell’elenco dei responsabili approvazione mostrata di seguito:

![](media/azure-ad-pim-approval-workflow/image019.png)

Per rimuovere un responsabile approvazione, fare semplicemente clic sul pulsante Rimuovi vicino al nome.

Per aggiungere responsabili approvazione aggiuntivi, ripetere il processo.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Visualizzare la cronologia delle richieste e delle approvazioni per tutti i ruoli con privilegi

Per visualizzare la cronologia delle richieste e delle approvazioni per tutti i ruoli con privilegi, selezionare Cronologia controllo dal dashboard:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
È possibile ordinare i dati per azione e ricercare “L’attivazione è stata approvata”.

### <a name="view-pending-approvals-requests"></a>Visualizzare le approvazioni (richieste) in sospeso

In qualità di responsabile approvazione con delega riceverà notifiche e-mail quando una richiesta è in attesa di approvazione. Per visualizzare queste richieste nel portale PIM, dal dashboard (nella nuova navigazione) selezionare la scheda “Richieste di approvazione in sospeso” nella barra di navigazione sinistra.

![](media/azure-ad-pim-approval-workflow/image023.png)

Da qui, è possibile visualizzare un elenco delle richieste in attesa di approvazione:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Approvare o rifiutare le richieste di elevazione del ruolo (singolarmente e/o in blocco)

Selezionare le richieste che si desidera approvare o rifiutare, quindi fare clic sul pulsante della barra delle azioni che corrisponde alla decisione:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Fornire una giustificazione per l’approvazione/il rifiuto

Viene aperto un nuovo pannello in cui è possibile approvare o rifiutare più richieste con una sola operazione. Immettere una giustificazione per la decisione e fare su Approva (o Rifiuta) in fondo al pannello:

![](media/azure-ad-pim-approval-workflow/image029.png)

Al termine del processo di richiesta, il simbolo dello stato riflette la decisione presa (in questo esempio, la decisione è un’approvazione):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Richiedere l’attivazione di un ruolo che richiede l’approvazione

È possibile avviare la richiesta di attivazione di un ruolo che richiede approvazione dalla navigazione PIM precedente o dalla nuova navigazione, poichè il processo di attivazione del ruolo è rimasto invariato. Selezionare semplicemente il ruolo dall’elenco dei ruoli da attivare:

![](media/azure-ad-pim-approval-workflow/image033.png)

Se un ruolo con privilegi richiede la Multi-Factor Authentication, verrà richiesto di completare prima questa attività:

![](media/azure-ad-pim-approval-workflow/image035.png)

Al termine, fare clic su Attiva e fornire una giustificazione (se richiesta):

![](media/azure-ad-pim-approval-workflow/image037.png)

Verrà visualizzata una notifica che indica al richiedente che la richiesta è in attesa di approvazione:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Visualizzare lo stato della richiesta da attivare

Per visualizzare lo stato di una richiesta in sospeso da attivare, è necessario accedere dalla nuova navigazione. Dalla barra di navigazione sinistra selezionare la scheda “My Requests” (Richieste personali):

![](media/azure-ad-pim-approval-workflow/image041.png)

Per impostazione predefinita, lo stato della richiesta viene impostato su “In sospeso” ma è possibile alternare la visualizzazione tra tutte le richieste e quelle rifiutate.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Completare l’attività in Azure AD se l’attivazione è stata approvata

Una volta che la richiesta è stata approvata, il ruolo è attivo ed è possibile procedere con qualsiasi altra azione necessaria per il ruolo.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Passaggi successivi

La sua opinione è importante per noi. Può condividere i suoi commenti o suggerimenti facendo clic qui.
