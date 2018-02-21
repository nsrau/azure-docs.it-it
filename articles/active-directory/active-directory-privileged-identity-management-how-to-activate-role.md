---
title: Come attivare o disattivare un ruolo | Microsoft Docs
description: "Informazioni su come attivare i ruoli per le identità con privilegi con l'applicazione Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8bd8a72653699df4f4953053d61c16e30a2a101d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management semplifica la gestione aziendale dell'accesso con privilegi alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.  

Se un ruolo amministrativo è stato impostato come idoneo, è possibile attivare il ruolo quando è necessario eseguire delle azioni con privilegi. Se ad esempio si gestiscono occasionalmente le funzionalità di Office 365, è possibile che gli amministratori dei ruoli con privilegi dell'organizzazione non configurino l'utente come amministratore globale permanente perché tale ruolo interessa anche altri servizi. L'utente è invece considerato idoneo per ruoli di Azure AD, ad esempio amministratore di Exchange Online. È possibile chiedere l'attivazione del ruolo quando si necessita di questi privilegi; si avrà il controllo amministrativo per un periodo di tempo predeterminato.

Questo articolo è indirizzato agli amministratori che devono attivare il proprio ruolo in Azure AD Privileged Identity Management (PIM). Verranno illustrati i passaggi per attivare un ruolo quando sono necessarie le autorizzazioni e per disattivarlo al termine. Gli amministratori di ruolo con privilegi possono anche richiedere l'approvazione per attivare un ruolo (Anteprima). Altre informazioni sui [Flussi di lavoro di approvazione di PIM](./privileged-identity-management/azure-ad-pim-approval-workflow.md).

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management
Usare l'applicazione Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/) per richiedere l'attivazione di un ruolo, anche se si intende usare un altro portale o PowerShell. Se Azure AD Privileged Identity Management non è disponibile nel portale di Azure, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **Altri servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## <a name="activate-a-role"></a>Attivare un ruolo
Quando è necessario usare un ruolo, è possibile richiedere l'attivazione selezionando l'opzione di navigazione **Ruoli personali** nella colonna di navigazione a sinistra dell'applicazione Azure AD Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il riquadro Azure AD Privileged Identity Management.
2. Selezionare **Ruoli personali**. Nel raggruppamento nella parte superiore della pagina è visualizzato un elenco dei ruoli idonei assegnati.
3. Selezionare un ruolo da attivare.
4. Selezionare **Attiva**. Verrà visualizzato il pannello **Richiedi l'attivazione del ruolo** .
5. Alcuni ruoli richiedono l'autenticazione Multi-Factor Authentication (MFA) prima di poter attivare il ruolo. È sufficiente eseguire l'autenticazione una volta per sessione.
   
    ![Schermata Verifica con MFA prima dell'attivazione del ruolo][2]
6. Immettere il motivo della richiesta di attivazione nel campo di testo.  Alcuni ruoli richiedono di specificare un numero di ticket.
7. Selezionare **OK**.  Se il ruolo non richiede l'approvazione, viene attivato ora e visualizzato nell'elenco dei ruoli attivi (direttamente sotto l'elenco di assegnazioni di ruoli idonei). Se il [ruolo richiede l'approvazione](./privileged-identity-management/azure-ad-pim-approval-workflow.md) per l'attivazione, nell'angolo superiore destro del browser verrà visualizzata una notifica popup che informa che la richiesta è in attesa di approvazione.

    ![Notifica di richiesta in sospeso - schermata][3]

## <a name="deactivate-a-role"></a>Disattivare un ruolo
Un ruolo attivato si disattiva automaticamente quando viene raggiunto il limite di tempo (durata idonea).

Se si completano le attività dell'amministratore prima del raggiungimento del limite di tempo, è possibile disattivare un ruolo manualmente nell'applicazione Azure AD Privileged Identity Management.  Selezionare **Ruoli personali**, scegliere il ruolo da usare dal raggruppamento **Assegnazioni di ruolo attive** e selezionare **Disattiva**.  

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso
Nel caso in cui non si richieda l'attivazione di un ruolo che necessita dell'approvazione, è possibile annullare una richiesta in sospeso in qualsiasi momento. Selezionare semplicemente l'opzione di navigazione **Ruoli personali** nella colonna di navigazione a sinistra dell'applicazione Azure AD Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il riquadro Azure AD Privileged Identity Management.
2. Selezionare **Ruoli personali**. Nel raggruppamento nella parte superiore della pagina è visualizzato un elenco dei ruoli idonei assegnati.
3. Selezionare un ruolo.
4. Selezionare il banner **Activation is pending approval** (Attivazione in attesa di approvazione) nel pannello contenente i dettagli dell'attivazione del ruolo.
5. Selezionare **Annulla** nella parte superiore del pannello **In attesa di approvazione**.

   ![Annullare la richiesta in sospeso - schermata][4]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Azure AD Privileged Identity Management, vedere i collegamenti seguenti.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
