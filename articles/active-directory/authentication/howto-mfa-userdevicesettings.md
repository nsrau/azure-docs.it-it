---
title: Amministratori gestire utenti e dispositivi-autenticazione a più fattori di Azure-Azure Active Directory
description: In che modo gli amministratori possono modificare le impostazioni utente, ad esempio forzando gli utenti a eseguire di nuovo il processo di prova.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d500f6c74bb62322a5e14ce3c489e8dba11dc2f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68555768"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito:

* Richiedere agli utenti di fornire di nuovo i metodi di contatto
* Eliminare password per le app
* Richiedere l'autenticazione a più fattori su tutti i dispositivi attendibili

## <a name="manage-authentication-methods"></a>Gestire i metodi di autenticazione

Quando un amministratore ha assegnato il ruolo di amministratore dell'autenticazione, è possibile richiedere agli utenti di reimpostare la password, ripetere la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente.

![Gestire i metodi di autenticazione dal portale di Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Reimposta password Reimposta la password dell'utente e assegna una password temporanea che deve essere modificata al successivo accesso.
2. È necessario ripetere la registrazione dell'autenticazione a più fattori, in modo che, quando l'utente esegue l'accesso in un secondo momento, verrà richiesto di configurare un nuovo metodo di autenticazione a più fattori.
3. Revoca le sessioni di autenticazione a più fattori consente di cancellare le sessioni di autenticazione a più fattori memorizzate dall'utente e le richiede di eseguire l'autenticazione a più fattori la volta successiva che è richiesta dal criterio sul dispositivo.

## <a name="require-users-to-provide-contact-methods-again"></a>Richiedere agli utenti di fornire di nuovo i metodi di contatto

Questa impostazione impone all'utente di completare di nuovo il processo di registrazione. Le app non basate su browser continuano a funzionare se l'utente dispone delle relative password.  È possibile eliminare le password dell'app degli utenti anche selezionando **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Come richiedere agli utenti di fornire di nuovo i metodi di contatto

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**.
   ![Richiedi agli utenti di fornire di nuovo i metodi di contatto](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

Le organizzazioni possono completare questi passaggi con PowerShell usando il comando seguente come guida per cancellare `StrongAuthenticationMethods` l'attributo:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Questa impostazione elimina tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![Elimina tutte le password dell'app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Ripristinare MFA in tutti i dispositivi memorizzati per un utente

Una delle funzionalità configurabili di Azure Multi-Factor Authentication è dare agli utenti la possibilità di contrassegnare i dispositivi come attendibili. Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Gli utenti possono rifiutare esplicitamente la verifica in due passaggi per un numero configurabile di giorni per i propri dispositivi regolari. Se un account viene compromesso o un dispositivo attendibile viene smarrito, è necessario poter rimuovere lo stato attendibile e richiedere di nuovo la verifica in due passaggi.

Quando questa opzione è selezionata, è necessario **ripristinare l'autenticazione a più fattori in tutti i dispositivi memorizzati** per eseguire la verifica in due passaggi al successivo accesso, anche se il dispositivo è stato contrassegnato come attendibile.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella per **ripristinare l'autenticazione a più fattori in tutti i dispositivi**
   ![memorizzati ripristinare l'autenticazione a più fattori in tutti i dispositivi memorizzati](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user.md)
