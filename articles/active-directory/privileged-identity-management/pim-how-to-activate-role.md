---
title: Attivare i ruoli di Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come attivare ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756295"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Attivare i ruoli di Azure AD in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) semplifica la gestione aziendale dell'accesso con privilegi alle risorse in Azure AD e in altri servizi online Microsoft, ad esempio Office 365 o Microsoft Intune.  

Se un ruolo amministrativo è stato impostato come idoneo, è possibile attivare il ruolo quando è necessario eseguire delle azioni con privilegi. Se ad esempio si gestiscono occasionalmente le funzionalità di Office 365, è possibile che gli amministratori dei ruoli con privilegi dell'organizzazione non configurino l'utente come amministratore globale permanente perché tale ruolo interessa anche altri servizi. L'utente è invece considerato idoneo per ruoli di Azure AD, ad esempio amministratore di Exchange Online. È possibile richiedere l'attivazione del ruolo quando tali privilegi risulteranno necessari; si avrà il controllo amministrativo per un periodo di tempo predeterminato.

Questo articolo è per gli amministratori che devono attivare il proprio ruolo di Azure AD in Privileged Identity Management.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario assumere un ruolo di Azure AD, è possibile richiedere l'attivazione usando l'opzione di navigazione **ruoli personali** in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**. Per informazioni su come aggiungere il riquadro Privileged Identity Management al dashboard, vedere iniziare a [usare Privileged Identity Management](pim-getting-started.md).

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **ruoli personali** per visualizzare un elenco dei ruoli Azure ad idonei.

    ![Ruoli di Azure AD-ruoli personali che mostrano l'elenco dei ruoli idonei o attivi](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Individuare un ruolo che si desidera attivare.

    ![Ruoli di Azure AD-elenco dei ruoli idonei che mostra il collegamento di attivazione](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Fare clic su **Attiva** per aprire il riquadro dei dettagli di attivazione del ruolo.

1. Se il ruolo richiede l'autenticazione a più fattori (MFA), fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare il riquadro identità con l'autenticazione a più fattori prima dell'attivazione del ruolo](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Fare clic su **Verifica la mia identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Pagina di verifica aggiuntiva di sicurezza che chiede come contattare l'utente](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Fare clic su **Attiva** per aprire il riquadro di attivazione.

    ![Riquadro attivazione per specificare l'ora di inizio, la durata, il ticket e il motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata.

1. Specificare la durata dell'attivazione.

1. Nella casella **Motivo dell'attivazione**, immettere il motivo della richiesta di attivazione. Alcuni ruoli richiedono di specificare un numero di ticket.

    ![Riquadro attivazione completato con un'ora di inizio, una durata, un ticket e un motivo personalizzati](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Fare clic su **Attiva**.

    Se il ruolo non richiede l'approvazione, viene visualizzato un riquadro **stato attivazione** che visualizza lo stato dell'attivazione.

    ![Pagina relativa allo stato di attivazione che mostra le tre fasi di attivazione](./media/pim-how-to-activate-role/activation-status.png)

    Una volta completate tutte le fasi, fare clic **sul collegamento di disconnessione per** disconnettersi dal portale di Azure. Quando si accede al portale, è ora possibile usare il ruolo.

    Se il [ruolo richiede l'approvazione](./azure-ad-pim-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è una notifica di approvazione in sospeso](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Richieste personali** per visualizzare un elenco delle richieste.

    ![Ruoli di Azure AD-elenco richieste personali](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Disattivare un ruolo

Un ruolo attivato si disattiva automaticamente quando viene raggiunto il limite di tempo (durata idonea).

Se si completano le attività dell'amministratore prima del raggiungimento del limite di tempo, è possibile disattivare un ruolo manualmente in Azure AD Privileged Identity Management.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Ruoli personali**.

1. Fare clic su **Ruoli attivi** per visualizzare l'elenco di ruoli attivi.

1. Individuare il ruolo da non usare ulteriormente e quindi fare clic su **Disattiva**.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Richieste personali**.

1. Per il ruolo che si vuole annullare, scegliere il pulsante **Annulla**.

    Quando si fa clic su Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco richieste personali con il pulsante Annulla evidenziato](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Privileged Identity Management, è possibile che l'attivazione non venga propagata immediatamente a tutti i portali che richiedono il ruolo privilegiato. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione viene ritardata, è necessario eseguire le operazioni seguenti.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

    Quando si attiva un ruolo di Azure AD, vengono visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile usare questo collegamento per la disconnessione. Questo consente di risolvere la maggior parte dei casi per il ritardo di attivazione.

1. In Privileged Identity Management verificare di essere elencati come membri del ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Attiva i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-activate-your-roles.md)
