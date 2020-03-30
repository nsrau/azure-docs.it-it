---
title: Attivare i ruoli di Azure AD in PIM - Azure Active Directory Documenti Microsoft
description: Informazioni su come attivare i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499153"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Attivare i ruoli di Azure AD in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) semplifica la gestione aziendale dell'accesso con privilegi alle risorse in Azure AD e in altri servizi online Microsoft, ad esempio Office 365 o Microsoft Intune.  

Se un ruolo amministrativo è stato impostato come idoneo, è possibile attivare il ruolo quando è necessario eseguire delle azioni con privilegi. Se ad esempio si gestiscono occasionalmente le funzionalità di Office 365, è possibile che gli amministratori dei ruoli con privilegi dell'organizzazione non configurino l'utente come amministratore globale permanente perché tale ruolo interessa anche altri servizi. L'utente è invece considerato idoneo per ruoli di Azure AD, ad esempio amministratore di Exchange Online. È possibile richiedere l'attivazione del ruolo quando tali privilegi risulteranno necessari; si avrà il controllo amministrativo per un periodo di tempo predeterminato.

Questo articolo è destinato agli amministratori che devono attivare il ruolo di Azure AD in Gestione delle identità con privilegi.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure.Start in November 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure resource roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Sign in to the [Azure portal](https://portal.azure.com/) with the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario assumere un ruolo di Azure AD, è possibile richiedere l'attivazione usando l'opzione di esplorazione **dei ruoli** personali in Gestione identità con privilegi.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD . Per informazioni su come aggiungere il riquadro Gestione identità con privilegi al dashboard, vedere [Iniziare a usare Gestione identità con privilegi.](pim-getting-started.md)

1. Selezionare **Ruoli personali**e quindi Ruoli **di Azure AD** per visualizzare un elenco dei ruoli di Azure AD idonei.

    ![Pagina Ruoli personali con i ruoli che è possibile attivare](./media/pim-how-to-activate-role/my-roles.png)

1. Nell'elenco **Ruoli di Azure AD** individuare il ruolo che si vuole attivare.

    ![Ruoli di Azure AD - Elenco di ruoli idonei](./media/pim-how-to-activate-role/activate-link.png)

1. Selezionare **Attiva** per aprire il riquadro Attiva.

    ![Ruoli di Azure AD - la pagina di attivazione contiene durata e ambitoAzure AD roles - activation page contains duration and scope](./media/pim-how-to-activate-role/activate-page.png)

1. Se il ruolo richiede l'autenticazione a più fattori, fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare l'identità con l'autenticazione a più fattori prima dell'attivazione del ruoloVerify my identity with MFA before role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Seleziona **Verifica la mia identità** e segui le istruzioni per fornire ulteriori verifiche di sicurezza.

    ![Schermata per fornire la verifica della sicurezza, ad esempio un codice PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se si desidera specificare un ambito ridotto, selezionare **Ambito** per aprire il riquadro dei filtri. Nel riquadro dei filtri è possibile specificare le risorse di Azure AD a cui è necessario accedere. È consigliabile richiedere l'accesso solo alle risorse necessarie.

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il ruolo di Azure AD verrà attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

1. Selezionare **Attiva**.

    Se il ruolo non richiede l'approvazione, viene attivato e aggiunto all'elenco di ruoli attivi. Se si desidera utilizzare il ruolo, seguire i passaggi nella sezione successiva.

    ![Completato il riquadro Attiva con ambito, ora di inizio, durata e motivo](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è in attesa di notifica di approvazione](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

In caso di ritardo dopo l'attivazione, seguire questi passaggi dopo l'attivazione per usare immediatamente i ruoli di Azure AD.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare Ruoli personali per visualizzare un elenco dei ruoli di Azure AD idonei e dei ruoli delle risorse di Azure.Select **My roles** to see a list of your eligible Azure AD roles and Azure resource roles.

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare la scheda **Ruoli attivi.**

1. Quando il ruolo è attivo, disconnettersi dal portale ed eseguire nuovamente l'accesso.

    Il ruolo dovrebbe ora essere disponibile per l'uso.

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare Richieste personali per visualizzare un elenco delle richieste del ruolo Azure AD e del ruolo risorsa di **Azure.Select My requests** to see a list of your Azure AD role and Azure resource role requests.

    ![Richieste personali - Pagina di Azure AD che mostra le richieste in sospesoMy requests - Azure AD page showing your pending requests](./media/pim-how-to-activate-role/my-requests-page.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **Richieste personali**.

1. Per il ruolo che si desidera annullare, selezionare il collegamento **Annulla.**

    Quando si seleziona Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco richieste con l'azione Annulla evidenziata](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Risolvere i problemi

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Gestione identità privilegiate, l'attivazione potrebbe non propagarsi immediatamente a tutti i portali che richiedono il ruolo con privilegi. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione è ritardata, ecco cosa dovresti fare.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

    Quando si attiva un ruolo di Azure AD, verranno visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile utilizzare questo collegamento per disconnettersi. Questo risolverà la maggior parte dei casi per ritardo di attivazione.

1. In Gestione identità con privilegi verificare di essere elencati come membri del ruolo.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario assumere un ruolo di Azure AD, è possibile richiedere l'attivazione usando l'opzione di spostamento **dei ruoli** personali in Gestione identità con privilegi.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD . Per informazioni su come aggiungere il riquadro Gestione identità con privilegi al dashboard, vedere [Iniziare a usare Gestione identità con privilegi.](pim-getting-started.md)

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Ruoli personali** per visualizzare un elenco dei ruoli di Azure AD idonei.

    ![Ruoli di Azure AD - Elenco ruoli personali che mostra noto o attivo](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Individuare un ruolo che si desidera attivare.

    ![Ruoli di Azure AD - Elenco di ruoli idonei con il collegamento Attiva](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Fare clic su **Attiva** per aprire il riquadro dei dettagli di attivazione del ruolo.

1. Se il ruolo richiede l'autenticazione a più fattori (MFA), fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare il riquadro dell'identità con l'autenticazione a più fattori prima dell'attivazione del ruoloVerify my identity pane with MFA before](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Fare clic su **Verifica la mia identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Pagina di verifica della sicurezza aggiuntiva che chiede come contattarti](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Fare clic su **Attiva** per aprire il riquadro di attivazione.

    ![Riquadro di attivazione per specificare l'ora di inizio, la durata, il ticket e il motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata.

1. Specificare la durata dell'attivazione.

1. Nella casella **Motivo dell'attivazione**, immettere il motivo della richiesta di attivazione. Alcuni ruoli richiedono di specificare un numero di ticket.

    ![Riquadro Attivazione completata con un'ora di inizio, una durata, un ticket e un motivo personalizzati](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Fare clic su **Attiva**.

    Se il ruolo non richiede l'approvazione, viene visualizzato un riquadro **Stato attivazione** che visualizza lo stato dell'attivazione.

    ![Pagina di stato dell'attivazione che mostra le tre fasi di attivazione](./media/pim-how-to-activate-role/activation-status.png)

    Al termine di tutte le fasi, fare clic sul collegamento Disconnetti per disconnettersi dal portale di Azure.Once all the stages are complete, click the **Disciout** link to sign out from the Azure portal. Quando si accede nuovamente al portale, è ora possibile utilizzare il ruolo.

    Se il [ruolo richiede l'approvazione](./azure-ad-pim-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica di Azure per informare che la richiesta è in attesa di approvazione.

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Richieste personali** per visualizzare un elenco delle richieste.

    ![Ruoli di Azure AD - Elenco richieste personaliAzure AD roles - My requests list](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

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

   ![Elenco delle mie richieste con il pulsante Annulla evidenziato](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Risolvere i problemi

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Gestione identità privilegiate, l'attivazione potrebbe non propagarsi immediatamente a tutti i portali che richiedono il ruolo con privilegi. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione è ritardata, ecco cosa dovresti fare.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

    Quando si attiva un ruolo di Azure AD, verranno visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile utilizzare questo collegamento per disconnettersi. Questo risolverà la maggior parte dei casi per ritardo di attivazione.

1. In Gestione identità con privilegi verificare di essere elencati come membri del ruolo.

 ---

## <a name="next-steps"></a>Passaggi successivi

- [Attivare i ruoli di Azure AD in Gestione delle identità con privilegi](pim-how-to-activate-role.md)
