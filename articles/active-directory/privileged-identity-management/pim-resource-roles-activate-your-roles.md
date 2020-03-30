---
title: Attivare i ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come attivare i ruoli di risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023139"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Attivare i ruoli delle risorse di Azure in Gestione identità con privilegi

Usare Privileged Identity Management (PIM) per consentire ai membri del ruolo idonei per le risorse di Azure di pianificare l'attivazione per una data e un'ora future. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori.

Questo articolo è destinato ai membri che devono attivare il ruolo di risorsa di Azure in Gestione delle identità con privilegi.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario assumere un ruolo di risorsa di Azure, è possibile richiedere l'attivazione usando l'opzione di esplorazione **dei ruoli** personali in Gestione identità con privilegi.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD . Per informazioni su come aggiungere il riquadro Gestione identità con privilegi al dashboard, vedere [Iniziare a usare Gestione identità con privilegi.](pim-getting-started.md)

1. Selezionare **Ruoli personali**.

    ![Pagina Ruoli personali con i ruoli che è possibile attivare](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selezionare **Ruoli delle risorse** di Azure per visualizzare un elenco dei ruoli delle risorse di Azure idonei.

   ![Pagina Ruoli personali - Ruoli delle risorse di AzureMy roles - Azure resource roles page](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Nell'elenco dei **ruoli delle risorse di Azure** trovare il ruolo da attivare.

    ![Ruoli delle risorse di Azure - Elenco ruoli idonei personali](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selezionare **Attiva** per aprire il riquadro Attiva.

1. Se il ruolo richiede l'autenticazione a più fattori, fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare l'identità con l'autenticazione a più fattori prima dell'attivazione del ruoloVerify my identity with MFA before role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Seleziona **Verifica la mia identità** e segui le istruzioni per fornire ulteriori verifiche di sicurezza.

    ![Schermata per fornire la verifica della sicurezza, ad esempio un codice PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se si desidera specificare un ambito ridotto, selezionare **Ambito** per aprire il riquadro Filtro risorse.

    È consigliabile richiedere solo l'accesso alle risorse necessarie. Nel riquadro Filtro della risorsa, è possibile specificare i gruppi di risorse o le risorse a cui è necessario accedere.

    ![Attiva - Riquadro Filtro risorse per specificare l'ambito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il membro verrà attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

    ![Completato il riquadro Attiva con ambito, ora di inizio, durata e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selezionare **Attiva**.

    Se il ruolo non richiede l'approvazione, viene attivato e aggiunto all'elenco di ruoli attivi. Se si desidera utilizzare il ruolo, seguire i passaggi nella sezione successiva.

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è in attesa di notifica di approvazione](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

In caso di ritardo dopo l'attivazione, seguire questi passaggi dopo l'attivazione per usare immediatamente i ruoli delle risorse di Azure.In any delay after activation, follow these steps after you activate to use your Azure resource roles immediately.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare Ruoli personali per visualizzare un elenco dei ruoli di Azure AD idonei e dei ruoli delle risorse di Azure.Select **My roles** to see a list of your eligible Azure AD roles and Azure resource roles.

1. Selezionare **Ruoli delle risorse di Azure**.

1. Selezionare la scheda **Ruoli attivi.**

1. Quando il ruolo è attivo, disconnettersi dal portale ed eseguire nuovamente l'accesso.

    Il ruolo dovrebbe ora essere disponibile per l'uso.

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Selezionare Richieste personali per visualizzare un elenco delle richieste del ruolo Azure AD e del ruolo risorsa di **Azure.Select My requests** to see a list of your Azure AD role and Azure resource role requests.

    ![Richieste personali - Pagina delle risorse di Azure che mostra le richieste in sospesoMy requests - Azure resource page showing your pending requests](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

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

    Quando si attiva un ruolo di risorsa di Azure, verranno visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile utilizzare questo collegamento per disconnettersi. Questo risolverà la maggior parte dei casi per ritardo di attivazione.

1. In Gestione identità con privilegi verificare di essere elencati come membri del ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in Gestione delle identità con privilegiExtend or renew Azure resource roles in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Attivare i ruoli di Azure AD in Gestione delle identità con privilegi](pim-how-to-activate-role.md)
