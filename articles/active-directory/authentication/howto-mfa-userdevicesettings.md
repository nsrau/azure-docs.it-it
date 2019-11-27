---
title: Gestire utenti e dispositivi autenticazione a più fattori di Azure-Azure Active Directory
description: In che modo gli amministratori possono modificare le impostazioni utente, ad esempio forzando gli utenti a eseguire di nuovo il processo di prova.
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

## <a name="manage-authentication-methods"></a>Gestire i metodi di autenticazione

Quando un amministratore ha assegnato il ruolo di amministratore dell'autenticazione, è possibile richiedere agli utenti di reimpostare la password, ripetere la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente.

![Gestire i metodi di autenticazione dal portale di Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Scegliere l'utente su cui si vuole eseguire un'azione e selezionare **metodi di autenticazione**.
   - **Reimposta password** Reimposta la password dell'utente e assegna una password temporanea che deve essere modificata al successivo accesso.
   - È **necessario ripetere la registrazione** dell'autenticazione a più fattori, in modo che, quando l'utente esegue l'accesso in un secondo momento, verrà richiesto di configurare un nuovo metodo di autenticazione a più fattori.
   - **Revoca le sessioni** di autenticazione a più fattori consente di cancellare le sessioni di autenticazione a più fattori memorizzate dall'utente e le richiede di eseguire l'autenticazione a più fattori la volta successiva che è richiesta dal criterio sul dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Questa impostazione elimina tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app. Per eseguire questa azione, sono necessarie le autorizzazioni di amministratore globale.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![eliminare tutte le password dell'app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user.md)
