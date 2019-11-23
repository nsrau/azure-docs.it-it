---
title: Manage users and devices Azure MFA - Azure Active Directory
description: How can administrators change user settings such as forcing the users to do the proof-up process again.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404174"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito:

* Richiedere agli utenti di fornire di nuovo i metodi di contatto
* Eliminare password per le app
* Richiedere l'autenticazione a più fattori su tutti i dispositivi attendibili

## <a name="manage-authentication-methods"></a>Manage authentication methods

As an administrator assigned the Authentication Administrator role you can require users to reset their password, re-register for MFA, or revoke existing MFA sessions from their user object.

![Manage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Choose the user you wish to perform an action on and select **Authentication methods**.
   - **Reset Password** will reset the user's password and assign a temporary password that must be changed on the next sign in.
   - **Require Re-register MFA** will make it so that when the user signs in next time, they will be requested to setup a new MFA authentication method.
   - **Revoke MFA Sessions** clears the user's remembered MFA sessions and requires them to perform MFA the next time it is required by the policy on the device.

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Questa impostazione elimina tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app. Global administrator permissions are required to perform this action.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A list of quick step options appears on the right.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![Delete all existing app passwords](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user.md)
