---
title: Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare le impostazioni del ruolo Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cff298e24ac185767e6290e396818ccece7b9b55
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809161"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management

Un amministratore del ruolo con privilegi può personalizzare Privileged Identity Management (PIM) nell'organizzazione Azure Active Directory (Azure AD), inclusa la modifica dell'esperienza di un utente che sta attivando un'assegnazione di ruolo idonea.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo di Azure AD seguire questa procedura.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Settings**.

    ![Ruoli di Azure AD-impostazioni](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Fare clic su **Ruoli**.

1. Fare clic sul ruolo di cui si intende configurare le impostazioni.

    ![Ruoli di Azure AD-ruoli delle impostazioni](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Nella pagina delle impostazioni per ogni ruolo sono presenti numerose impostazioni che è possibile configurare. Tali impostazioni interessano solo gli utenti **idonei** per le assegnazioni e non le assegnazioni **permanenti**.

## <a name="activations"></a>Attivazioni

Usare il dispositivo di scorrimento **Attivazioni** per impostare il tempo massimo, espresso in ore, per cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 72 ore.

## <a name="notifications"></a>Notifiche

Usare il parametro **Notifiche** per determinare se gli amministratori riceveranno notifiche via email all'attivazione dei ruoli. Questa notifica può essere utile per rilevare le attivazioni non autorizzate o illegittime.

Se impostato su **Abilita**, le notifiche sono inviate a:

- Amministratore dei ruoli con privilegi
- Amministratore della sicurezza
- Amministratore globale

Per ulteriori informazioni, vedere [notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Ticket di evento imprevisto/richiesta

Usare l' **opzione** per richiedere agli amministratori idonei di includere un numero di ticket quando attivano il proprio ruolo. Questa procedura può rendere più efficienti i controlli di accesso ai ruoli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Usare l'opzione **Multi-Factor Authentication** per specificare se richiedere agli utenti di verificare la propria identità con MFA prima di attivare i ruoli. È necessario verificarne l'identità una sola volta per ogni sessione, non ogni volta che viene attivato un ruolo. Tenere presente due suggerimenti quando si abilita l'autenticazione MFA:

- Gli utenti che dispongono di account Microsoft per gli indirizzi di posta elettronica (in genere @outlook.com, ma non sempre) non possono registrarsi per Multi-Factor Authentication di Azure. Se si desidera assegnare ruoli agli utenti con account Microsoft, è necessario renderli amministratori permanenti o disabilitare l'autenticazione a più fattori per quel ruolo.
- Non è possibile disabilitare Multi-Factor Authentication di Azure per i ruoli con privilegi elevati per Azure AD e Office 365. Questa funzionalità di sicurezza consente di proteggere i ruoli seguenti:  
  
  - Amministratore Azure Information Protection
  - Amministratore fatturazione
  - Amministratore di applicazioni cloud
  - Amministratore di conformità
  - Amministratore di accesso condizionale
  - Amministratore di Dynamics 365
  - Responsabile approvazione per l'accesso a Customer Lockbox
  - Writer di directory
  - Amministratore di Exchange
  - Amministratore globale
  - Amministratore di Intune
  - Amministratore Power BI
  - Amministratore dei ruoli con privilegi
  - Amministratore della sicurezza
  - Amministratore di SharePoint
  - Amministratore di Skype for Business
  - Amministratore utenti

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Richiedi approvazione

Se si desidera delegare l'approvazione richiesta per attivare un ruolo, attenersi alla seguente procedura.

1. Impostare l'opzione **Richiedi approvazione** su **Abilitato**. Il riquadro viene espanso con le opzioni per selezionare i responsabili dell'approvazione.

    ![Ruoli Azure AD-impostazioni-Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se non vengono specificati responsabili approvazione, l'amministratore del ruolo con privilegi diventa il responsabile approvazione predefinito ed è quindi necessario per approvare tutte le richieste di attivazione per questo ruolo.

1. Per specificare i responsabili dell'approvazione, fare clic su **Selezione responsabili approvazione**.

    ![Ruoli Azure AD-impostazioni-Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selezionare uno o più responsabili approvazione oltre all'amministratore dei ruoli con privilegi, quindi fare clic su **Seleziona**. È possibile selezionare utenti o gruppi. È consigliabile che almeno due responsabili approvazione siano. Anche se si aggiunge se stessi come responsabile approvazione, non è possibile approvare autonomamente l'attivazione di un ruolo. I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato le impostazioni di tutti i ruoli, selezionare **Salva** per salvare le modifiche.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurare gli avvisi di sicurezza per i ruoli di Azure AD in Privileged Identity Management](pim-how-to-configure-security-alerts.md)
