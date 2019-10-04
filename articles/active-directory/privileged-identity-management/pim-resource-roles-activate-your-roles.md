---
title: Attivare i ruoli delle risorse di Azure in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come attivare i ruoli di risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804346"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Attivare i ruoli di risorse di Azure in PIM

Usando Azure Active Directory (Azure AD) Privileged Identity Management (PIM), i membri del ruolo idonei per le risorse di Azure possono pianificare l'attivazione per una data e un'ora future. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori.

Questo articolo è destinato ai membri che devono attivare il proprio ruolo delle risorse di Azure in PIM.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario usare un ruolo delle risorse di Azure è possibile richiedere l'attivazione tramite l'opzione di navigazione in PIM **Ruoli personali**.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**. Per informazioni su come aggiungere il riquadro PIM alla dashboard, vedere [Iniziare a usare PIM](pim-getting-started.md).

1. Fare clic su **Ruoli personali**.

    ![Pagina ruoli personali che mostra i ruoli che è possibile attivare](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Fare clic su **ruoli delle risorse di Azure** per visualizzare un elenco dei ruoli delle risorse di Azure idonei.

   ![Ruoli personali-pagina ruoli risorse di Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Nell'elenco dei **ruoli delle risorse di Azure** trovare il ruolo da attivare.

    ![Ruoli delle risorse di Azure-elenco dei ruoli idonei](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Fare clic su **Attiva** per aprire il riquadro di attivazione.

1. Se il ruolo richiede l'autenticazione a più fattori (MFA), fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verificare l'identità con l'autenticazione a più fattori prima dell'attivazione del ruolo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Fare clic su **Verifica la mia identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Schermata per fornire la verifica della sicurezza, ad esempio un codice PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se si vuole specificare un ambito ridotto, fare clic su **Ambito** per aprire il riquadro Filtro della risorsa.

    È consigliabile richiedere solo l'accesso alle risorse necessarie. Nel riquadro Filtro della risorsa, è possibile specificare i gruppi di risorse o le risorse a cui è necessario accedere.

    ![Attiva-riquadro filtro risorse per specificare l'ambito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il membro verrà attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

    ![Riquadro di attivazione completato con ambito, ora di inizio, durata e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Fare clic su **Attiva**.

    Se il ruolo non richiede l'approvazione, viene attivato e aggiunto all'elenco di ruoli attivi. Se si desidera utilizzare il ruolo, attenersi alla procedura descritta nella sezione successiva.

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![La richiesta di attivazione è una notifica di approvazione in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

In caso di ritardo dopo l'attivazione, seguire questa procedura dopo aver attivato per usare immediatamente i ruoli delle risorse di Azure.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **ruoli personali** per visualizzare un elenco dei ruoli Azure ad idonei e dei ruoli delle risorse di Azure.

1. Fare clic su **ruoli risorse di Azure**.

1. Fare clic sulla scheda **ruoli attivi** .

1. Quando il ruolo è attivo, disconnettersi dal portale ed eseguire di nuovo l'accesso.

    Il ruolo ora sarà disponibile per l'utilizzo.

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **richieste personali** per visualizzare un elenco delle richieste di ruolo di Azure ad e di risorse di Azure.

    ![Richieste personali-pagina delle risorse di Azure che mostra le richieste in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Richieste personali**.

1. Per il ruolo che si vuole annullare, scegliere il collegamento **Annulla**.

    Quando si fa clic su Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Elenco di richieste con azione di annullamento evidenziato](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Le autorizzazioni non vengono concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in PIM, è possibile che l'attivazione non venga propagata immediatamente a tutti i portali che richiedono il ruolo privilegiato. In alcuni casi, anche se la modifica viene propagata, la memorizzazione nella cache Web in un portale può comportare che la modifica non abbia effetto immediato. Se l'attivazione viene ritardata, è necessario eseguire le operazioni seguenti.

1. Disconnettersi dal portale di Azure e quindi eseguire nuovamente l'accesso.

    Quando si attiva un ruolo delle risorse di Azure, vengono visualizzate le fasi dell'attivazione. Dopo aver completato tutte le fasi, si vedrà il collegamento **Disconnetti**. È possibile usare questo collegamento per la disconnessione. Questa operazione risolverà molti casi di ritardo dell'attivazione.

1. In PIM verificare di essere elencati come membri del ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in PIM](pim-resource-roles-renew-extend.md)
- [Attivare i ruoli di Azure AD in PIM](pim-how-to-activate-role.md)
