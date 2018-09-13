---
title: Configurare le impostazioni dei ruoli della directory di Azure AD in PIM | Microsoft Docs
description: Informazioni su come configurare le impostazioni dei ruoli della directory di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2d7226f18eb922eaba3c8184656560c33202ef56
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665435"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>Configurare le impostazioni dei ruoli della directory di Azure AD in PIM

Un amministratore dei ruoli con privilegi può personalizzare Azure AD Privileged Identity Management (PIM) nell'organizzazione, ad esempio modificando l'esperienza di un utente che attiva l'assegnazione di idoneo al ruolo.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Seguire questi passaggi per aprire le impostazioni per un ruolo della directory di Azure AD.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Impostazioni**.

    ![Ruoli della directory di Azure AD - Impostazioni](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Fare clic su **Ruoli**.

1. Fare clic sul ruolo di cui si intende configurare le impostazioni.

    ![Ruoli della directory di Azure AD - Impostazioni - Ruoli](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Nella pagina delle impostazioni per ogni ruolo sono presenti numerose impostazioni che è possibile configurare. Tali impostazioni interessano solo gli utenti **idonei** per le assegnazioni e non le assegnazioni **permanenti**.

## <a name="activations"></a>Attivazioni

Usare il dispositivo di scorrimento **Attivazioni** per impostare il tempo massimo, espresso in ore, per cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 72 ore.

## <a name="notifications"></a>Notifiche

Usare l'opzione **Notifiche** per specificare se scegliere se il sistema invia messaggi di posta elettronica agli amministratori per confermare che hanno attivato un ruolo. Questa opzione può essere utile per il rilevamento di attivazioni non autorizzate o dannose.

## <a name="incidentrequest-ticket"></a>Ticket di evento imprevisto/richiesta

Usare l'opzione **Ticket di evento imprevisto/richiesta** per specificare se richiedere agli amministratori idonei di includere un numero di ticket quando attivano il proprio ruolo. Questa opzione può essere utile quando si eseguono i controlli di accesso dei ruoli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Usare l'opzione **Multi-Factor Authentication** per specificare se richiedere agli utenti di verificare la propria identità con MFA prima di attivare i ruoli. La verifica è necessaria solo una volta per ogni sessione, non ogni volta che si attiva un ruolo. Tenere presente due suggerimenti quando si abilita l'autenticazione MFA:

* Gli utenti che dispongono di account Microsoft per i relativi indirizzi di posta elettronica (in genere @outlook.com) non possono eseguire la registrazione per Azure MFA. Se si vuole assegnare ruoli agli utenti con account Microsoft, renderli amministratori permanenti o disabilitare l'autenticazione MFA per il ruolo.
* Non è possibile disabilitare l'autenticazione MFA per i ruoli con privilegi elevati per Azure AD e Office365. Si tratta di una funzionalità di sicurezza poiché è necessaria una protezione elevata per questi ruoli:  
  
  * Amministratore di applicazioni
  * Amministratore server proxy applicazione
  * Amministratore fatturazione  
  * Amministratore di conformità  
  * Amministratore del servizio CRM
  * Responsabile approvazione per l'accesso a Customer Lockbox
  * Ruolo con autorizzazioni di scrittura nella directory  
  * Amministratore di Exchange  
  * Amministratore globale
  * Amministratore del servizio Intune
  * Amministratore della cassetta postale  
  * Supporto partner - Livello 1  
  * Supporto partner - Livello 2  
  * Amministratore dei ruoli con privilegi
  * Amministratore della sicurezza  
  * Amministratore di SharePoint  
  * Amministratore di Skype for Business  
  * Amministratore account utente  

Per altre informazioni, vedere [Multi-Factor Authentication (MFA) e PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Richiedi approvazione

Se si vuole richiedere l'approvazione per attivare un ruolo, seguire questa procedura.

1. Impostare l'opzione **Richiedi approvazione** su **Abilitato**. Il riquadro viene espanso con le opzioni per selezionare i responsabili dell'approvazione.

    ![Ruoli della directory di Azure AD - Impostazioni - Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se **NON** viene specificato alcun responsabile delle approvazioni, gli amministratori con ruolo con privilegi diventano i responsabili dell'approvazione predefiniti. Gli amministratori con ruolo con privilegi devono approvare **TUTTE** le richieste di attivazione per il ruolo.

1. Per specificare i responsabili dell'approvazione, fare clic su **Selezione responsabili approvazione**.

    ![Ruoli della directory di Azure AD - Impostazioni - Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selezionare uno o più responsabili dell'approvazione e quindi fare clic su **Seleziona**. È possibile selezionare utenti o gruppi. È consigliabile selezionare almeno 2 responsabili dell'approvazione. L'autoapprovazione non è consentita.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato tutte le impostazioni dei ruoli, fare clic su **Salva** per salvare le modifiche.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli della directory di Azure AD in PIM](pim-how-to-add-role-to-user.md)
- [Configurare gli avvisi di sicurezza per i ruoli della directory di Azure AD in PIM](pim-how-to-configure-security-alerts.md)
