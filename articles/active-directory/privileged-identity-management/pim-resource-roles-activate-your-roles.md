---
title: Attiva il mio ruolo di risorse di Azure in PIM - Azure Active Directory | Microsoft Docs
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
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e8615cc5534255308c35fa1f675ef3a85aa84e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438454"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Attivare i ruoli di risorse di Azure in PIM

Usa Azure Active Directory (Azure AD) Privileged Identity Management (PIM), i membri del ruolo idoneo per le risorse di Azure possono pianificare l'attivazione per una data futura e un'ora. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori.

Questo articolo è destinato ai membri che devono attivare il proprio ruolo delle risorse di Azure in PIM.

## <a name="activate-a-role"></a>Attivare un ruolo

Quando è necessario usare un ruolo delle risorse di Azure è possibile richiedere l'attivazione tramite l'opzione di navigazione in PIM **Ruoli personali**.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**. Per informazioni su come aggiungere il riquadro PIM alla dashboard, vedere [Iniziare a usare PIM](pim-getting-started.md).

1. Fare clic su **Ruoli personali**.

    ![Ruoli di Azure AD e i ruoli delle risorse di Azure - ruoli personali](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Fare clic su **ruoli delle risorse di Azure** per visualizzare un elenco dei ruoli idonei risorse di Azure.

   ![Ruoli delle risorse di Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

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

    Se il ruolo non richiede l'approvazione, viene attivato e aggiunto all'elenco di ruoli attivi. Se si desidera utilizzare il ruolo, seguire i passaggi nella sezione successiva.

    Se il [ruolo richiede l'approvazione](pim-resource-roles-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica che informa che la richiesta è in attesa di approvazione.

    ![Notifica di richiesta in attesa](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

In caso di qualsiasi ritardo dopo l'attivazione, seguire questi passaggi dopo aver attivato per utilizzare immediatamente i ruoli delle risorse di Azure.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **ruoli personali** per visualizzare un elenco di idonei per i ruoli di Azure AD e i ruoli delle risorse di Azure.

1. Fare clic su **ruoli delle risorse di Azure**.

1. Scegliere il **ruoli attivi** scheda.

1. Una volta che il ruolo è attivo, disconnettersi dal portale e accedere nuovamente.

    Il ruolo dovrebbe ora essere disponibile per l'uso.

## <a name="view-the-status-of-your-requests"></a>Visualizzare lo stato della richiesta da attivare

È possibile visualizzare lo stato delle richieste in attesa da attivare.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **richieste personali** per visualizzare un elenco dei ruoli delle risorse di Azure e ruolo di Azure AD le richieste.

    ![Ruoli di Azure AD e i ruoli delle risorse di Azure - richieste personali](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scorrere verso destra per visualizzare la colonna **Stato richiesta**.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

Nel caso in cui non è richiesta l'attivazione di un ruolo che richiede l'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic su **Richieste personali**.

1. Per il ruolo che si vuole annullare, scegliere il collegamento **Annulla**.

    Quando si sceglie Annulla, la richiesta verrà annullata. Per attivare nuovamente il ruolo, è necessario inviare una nuova richiesta per l'attivazione.

   ![Annullare una richiesta in sospeso](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="permissions-not-granted-after-activating-a-role"></a>Autorizzazioni non concesse dopo l'attivazione di un ruolo

Quando si attiva un ruolo in PIM, sono necessari almeno 10 minuti prima di poter accedere al portale di amministrazione desiderato o eseguire funzioni all'interno di un carico di lavoro amministrativo specifico. Una volta completata l'attivazione, disconnettersi dal portale Azure e accedere nuovamente per iniziare a usare il ruolo appena attivato.

Per altri passaggi per la risoluzione dei problemi, vedere [Troubleshooting Elevated Permissions](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx) (Risoluzione dei problemi relativi alle autorizzazioni elevate).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Impossibile attivare un ruolo a causa di un blocco delle risorse

Se si riceve un messaggio che indica che una risorsa di Azure è bloccata quando si prova ad attivare un ruolo, è possibile che una risorsa inclusa nell'ambito di un'assegnazione di ruolo abbia un blocco. I blocchi proteggono le risorse dall'eliminazione accidentale o da modifiche impreviste. Un blocco impedisce inoltre a PIM di rimuovere un'assegnazione di ruolo nella risorsa alla fine del periodo di attivazione. Poiché PIM non può funzionare correttamente quando c'è un blocco applicato, viene impedito agli utenti di attivare ruoli nella risorsa. Ci sono due modi per risolvere questo problema:

- Eliminare il blocco come descritto in [Bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/resource-group-lock-resources.md).
- Se si vuole mantenere il blocco, rendere permanente l'assegnazione di ruolo o usare un account di emergenza.

## <a name="next-steps"></a>Passaggi successivi

- [Estendere o rinnovare i ruoli delle risorse di Azure in PIM](pim-resource-roles-renew-extend.md)
- [Attiva il mio ruolo di Azure AD in PIM](pim-how-to-activate-role.md)
