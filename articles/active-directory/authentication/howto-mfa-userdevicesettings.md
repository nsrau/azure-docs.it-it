---
title: Gestire utenti e dispositivi Azure MFA - Azure Active DirectoryManage users and devices Azure MFA - Azure Active Directory
description: In che modo gli amministratori possono modificare le impostazioni utente, ad esempio forzando gli utenti a eseguire nuovamente il processo di correzione.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653504"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito:

* Richiedere agli utenti di fornire di nuovo i metodi di contatto
* Eliminare password per le app
* Richiedere l'autenticazione a più fattori su tutti i dispositivi attendibili

## <a name="manage-authentication-methods"></a>Gestire i metodi di autenticazione

Come amministratore assegnato al ruolo di amministratore di autenticazione è possibile richiedere agli utenti di reimpostare la password, rieseguire la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente.

![Gestire i metodi di autenticazione dal portale di AzureManage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory** > **Users** > **All Users**.
1. Scegliere l'utente su cui si desidera eseguire un'azione e selezionare **Metodi di autenticazione**.
   - **Reimposta password** reimposterà la password dell'utente e assegnerà una password temporanea che deve essere modificata al successivo accesso.
   - **Richiedi nuovamente registrazione dell'autenticazione** a più fattori in modo che quando l'utente accede la prossima volta, verrà richiesto di impostare un nuovo metodo di autenticazione MFA.
   - **Revoca sessioni di autenticazione** a più fattori cancella le sessioni di autenticazione a più fattori memorizzate dell'utente e richiede l'esecuzione dell'autenticazione a più fattori la volta successiva che richiesto dai criteri nel dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Questa impostazione elimina tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app. Per eseguire questa azione sono necessarie autorizzazioni di amministratore globale.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Users** > **All Users**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![Eliminare tutte le password delle app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Fare clic su **save**.
8. Fare clic su **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user.md)
