---
title: Attivare i ruoli di risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come attivare i ruoli di risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666248"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Attivare i ruoli di risorse di Azure in PIM

Usando Azure AD Privileged Identity Management (PIM), i membri dei ruoli idonei per le risorse di Azure possono pianificare l'attivazione per una data e un'ora future. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori.

Questo articolo è destinato ai membri che devono attivare il proprio ruolo delle risorse di Azure in PIM.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario usare un ruolo delle risorse di Azure è possibile richiedere l'attivazione tramite l'opzione di navigazione in PIM **Ruoli personali**.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**. Per informazioni su come aggiungere il riquadro PIM alla dashboard, vedere [Iniziare a usare PIM](pim-getting-started.md).

1. Fare clic su **Ruoli personali** per visualizzare un elenco di ruoli idonei della directory di Azure AD e delle risorse di Azure.

    ![Ruoli della directory di Azure AD e ruoli delle risorse di Azure - Ruoli personali](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Nell'elenco dei **ruoli delle risorse di Azure** trovare il ruolo da attivare.

    ![Ruoli delle risorse di Azure - Elenco dei ruoli personali](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Fare clic su **Attiva** per aprire il riquadro di attivazione.

1. Se il ruolo richiede l'autenticazione a più fattori (MFA), fare clic su **Verificare la propria identità prima di procedere**. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Verifica con MFA prima dell'attivazione del ruolo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Fare clic su **Verifica la mia identità** e seguire le istruzioni per fornire la verifica aggiuntiva di sicurezza.

    ![Verifica aggiuntiva di sicurezza](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se si vuole specificare un ambito ridotto, fare clic su **Ambito** per aprire il riquadro Filtro della risorsa.

    È consigliabile richiedere solo l'accesso alle risorse necessarie. Nel riquadro Filtro della risorsa, è possibile specificare i gruppi di risorse o le risorse a cui è necessario accedere.

    ![Attivazione - Filtro della risorsa](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessario, specificare un'ora di inizio di attivazione personalizzata. Il membro verrà attivato dopo l'ora selezionata.

1. Nella casella **Motivo** immettere il motivo della richiesta di attivazione.

    ![Riquadro Completed Activate (Attivazione completata)](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Fare clic su **Attiva**.

    Se il ruolo non richiede l'approvazione, viene attivato ora e visualizzato nell'elenco dei ruoli attivi. Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![Notifica di richiesta in attesa](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Richieste personali** per visualizzare un elenco richieste di ruoli della directory di Azure AD e delle risorse di Azure.

    ![Ruoli della directory di Azure AD e ruoli delle risorse di Azure - Richieste personali](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

A causa della memorizzazione nella cache, le attivazioni non si verificano immediatamente nel portale di Azure senza un aggiornamento. Se è necessario ridurre la possibilità di ritardi dopo l'attivazione di un ruolo, è possibile usare la pagina **Accesso all'applicazione** nel portale. Le applicazioni a cui si esegue l'accesso da questa pagina verificano immediatamente la presenza di nuove assegnazioni di ruolo.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic sulla pagina **Accesso all'applicazione**.

    ![Accesso alle applicazioni di PIM - schermata](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Fare clic su **Risorse di Azure** per riaprire il portale in corrispondenza della pagina **Tutte le risorse**.

    Quando si fa clic su questo collegamento, vengono eseguiti un aggiornamento e quindi un controllo per le nuove assegnazioni di ruolo delle risorse di Azure.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Richieste personali**.

1. Per il ruolo che si vuole annullare, scegliere il collegamento **Annulla**.

    Quando si sceglie Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Annullare una richiesta in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in PIM](pim-resource-roles-renew-extend.md)
- [Attivare i ruoli della directory di Azure AD in PIM](pim-how-to-activate-role.md)