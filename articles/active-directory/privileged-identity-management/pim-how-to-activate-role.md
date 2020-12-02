---
title: Attivare i ruoli di Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come attivare ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 079dcdc9d1667809779489c5d1126bc85cafb881
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511321"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Attivare i ruoli di Azure AD in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) semplifica il modo in cui le aziende gestiscono l'accesso con privilegi alle risorse in Azure AD e in altri Servizi online Microsoft come Microsoft 365 o Microsoft Intune.  

Se è stato reso *idoneo* per un ruolo amministrativo, è necessario *attivare* l'assegnazione di ruolo quando è necessario eseguire azioni con privilegi. Ad esempio, se talvolta si gestiscono le funzionalità di Microsoft 365, è possibile che gli amministratori dei ruoli con privilegi dell'organizzazione non rendano un amministratore globale permanente, perché questo ruolo influisca anche su altri servizi. Al contrario, si renderebbe idonei per Azure AD ruoli come amministratore di Exchange Online. È possibile richiedere l'attivazione del ruolo quando tali privilegi risulteranno necessari; si avrà il controllo amministrativo per un periodo di tempo predeterminato.

Questo articolo è per gli amministratori che devono attivare il proprio ruolo di Azure AD in Privileged Identity Management.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con il ruolo di [amministratore dei ruoli con privilegi](../roles/permissions-reference.md#privileged-role-administrator) .
1. Aprire **Azure ad Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

    [![Selezionare Azure AD Privileged Identity Management >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Attivare un ruolo per la nuova versione

Quando è necessario assumere un ruolo di Azure AD, è possibile richiedere l'attivazione aprendo **ruoli personali** in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**. Per informazioni su come aggiungere il riquadro Privileged Identity Management al dashboard, vedere iniziare a [usare Privileged Identity Management](pim-getting-started.md).

1. Selezionare **ruoli personali** e quindi selezionare **Azure ad ruoli** per visualizzare un elenco dei ruoli Azure ad idonei.

    ![Pagina ruoli personali che mostra i ruoli che è possibile attivare](./media/pim-how-to-activate-role/my-roles.png)

1. Nell'elenco **ruoli Azure ad** individuare il ruolo che si desidera attivare.

    ![Ruoli di Azure AD-elenco dei ruoli idonei](./media/pim-how-to-activate-role/activate-link.png)

1. Selezionare **Activate (attiva** ) per aprire il riquadro Activate (attiva).

    ![Ruoli di Azure AD: la pagina di attivazione contiene la durata e l'ambito](./media/pim-how-to-activate-role/activate-page.png)

1. Selezionare **verifica aggiuntiva richiesta**"* * e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza. È necessario eseguire l'autenticazione solo una volta per sessione.

    ![Schermata per fornire la verifica della sicurezza, ad esempio un codice PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Dopo la funzionalità di autenticazione a più fattori, selezionare **Activate (attiva) prima di procedere**.

    ![Verificare la mia identità con l'autenticazione a più fattori prima dell'attivazione del ruolo](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Se si desidera specificare un ambito ridotto, selezionare **ambito** per aprire il riquadro filtro. Nel riquadro filtro è possibile specificare le risorse Azure AD a cui è necessario accedere. È consigliabile richiedere l'accesso al minor numero di risorse necessarie.

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il ruolo Azure AD viene attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

1. Selezionare **Attiva**.

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è una notifica di approvazione in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Visualizzare lo stato delle richieste per la nuova versione

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **richieste personali** per visualizzare un elenco delle richieste di ruolo di Azure ad e di risorse di Azure.

    ![Richieste personali-pagina Azure AD che mostra le richieste in sospeso](./media/pim-how-to-activate-role/my-requests-page.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="cancel-a-pending-request-for-new-version"></a>Annulla una richiesta in sospeso per la nuova versione

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **richieste personali**.

1. Per il ruolo che si desidera annullare, selezionare il collegamento **Annulla** .

    Quando si seleziona Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco di richieste con azione di annullamento evidenziato](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Risoluzione dei problemi per la nuova versione

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Privileged Identity Management, è possibile che l'attivazione non venga propagata immediatamente a tutti i portali che richiedono il ruolo privilegiato. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione viene ritardata, è necessario eseguire le operazioni seguenti.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

1. In Privileged Identity Management verificare di essere elencati come membri del ruolo.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Attivare un ruolo (versione precedente)

Quando è necessario assumere un ruolo di Azure AD, è possibile richiedere l'attivazione usando l'opzione di navigazione **ruoli personali** in Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**. Per informazioni su come aggiungere il riquadro Privileged Identity Management al dashboard, vedere iniziare a [usare Privileged Identity Management](pim-getting-started.md).

1. Selezionare **Azure ad ruoli**.

1. Selezionare **ruoli personali** per visualizzare un elenco dei ruoli di Azure ad idonei.

    ![Ruoli di Azure AD-ruoli personali che mostrano l'elenco dei ruoli idonei o attivi](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Individuare un ruolo che si desidera attivare.

    ![Ruoli di Azure AD-elenco dei ruoli idonei che mostra il collegamento di attivazione](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Selezionare **Activate (attiva** ) per aprire il riquadro dei dettagli di attivazione del ruolo.

1. Se il ruolo richiede l'autenticazione a più fattori, selezionare **verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare il riquadro identità con l'autenticazione a più fattori prima dell'attivazione del ruolo](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Selezionare **verifica l'identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Pagina di verifica aggiuntiva di sicurezza che chiede come contattare l'utente](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Selezionare **Activate (attiva** ) per aprire il riquadro attivazione.

    ![Riquadro attivazione per specificare l'ora di inizio, la durata, il ticket e il motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata.

1. Specificare la durata dell'attivazione.

1. Nella casella **Motivo dell'attivazione**, immettere il motivo della richiesta di attivazione. Alcuni ruoli richiedono di specificare un numero di ticket.

    ![Riquadro attivazione completato con un'ora di inizio, una durata, un ticket e un motivo personalizzati](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Selezionare **Attiva**.

    Se il ruolo non richiede l'approvazione, viene visualizzato un riquadro **stato attivazione** che visualizza lo stato dell'attivazione.

    ![Pagina relativa allo stato di attivazione che mostra le tre fasi di attivazione](./media/pim-how-to-activate-role/activation-status.png)

    Una volta completate tutte le fasi, selezionare **il collegamento per la** disconnessione per disconnettersi dal portale di Azure. Quando si accede al portale, è ora possibile usare il ruolo.

    Se il [ruolo richiede l'approvazione](./azure-ad-pim-approval-workflow.md) per l'attivazione, viene visualizzata una notifica di Azure nell'angolo superiore destro del browser che informa che la richiesta è in attesa di approvazione.

## <a name="view-the-status-of-your-requests-previous-version"></a>Visualizzare lo stato delle richieste (versione precedente)

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **richieste personali** per visualizzare un elenco delle richieste.

    ![Ruoli di Azure AD-elenco richieste personali](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Disattivare un ruolo (versione precedente)

Un ruolo attivato si disattiva automaticamente quando viene raggiunto il limite di tempo (durata idonea).

Se si completano le attività dell'amministratore prima del raggiungimento del limite di tempo, è possibile disattivare un ruolo manualmente in Azure AD Privileged Identity Management.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **ruoli personali**.

1. Selezionare **ruoli attivi** per visualizzare l'elenco dei ruoli attivi.

1. Individuare il ruolo che si sta utilizzando e quindi selezionare **Disattiva**.

## <a name="cancel-a-pending-request-previous-version"></a>Annulla una richiesta in sospeso (versione precedente)

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **richieste personali**.

1. Per il ruolo che si desidera annullare, selezionare il pulsante **Annulla** .

    Quando si seleziona **Annulla**, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco richieste personali con il pulsante Annulla evidenziato](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Risoluzione dei problemi (versione precedente)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in Privileged Identity Management, è possibile che l'attivazione non venga propagata immediatamente a tutti i portali che richiedono il ruolo privilegiato. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione viene ritardata, è necessario eseguire le operazioni seguenti.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

    Quando si attiva un ruolo di Azure AD, vengono visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile usare questo collegamento per la disconnessione. Questo consente di risolvere la maggior parte dei casi per il ritardo di attivazione.

1. In Privileged Identity Management verificare di essere elencati come membri del ruolo.

 ---

## <a name="next-steps"></a>Passaggi successivi

- [Attivare i ruoli di Azure AD in Privileged Identity Management](pim-how-to-activate-role.md)
