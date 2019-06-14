---
title: Gli amministratori di gestire utenti e dispositivi - Azure MFA - Azure Active Directory
description: Come gli amministratori possono modificare le impostazioni utente, ad esempio imporre agli utenti di eseguire nuovamente il processo di registrazione.
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
ms.openlocfilehash: 04d4848a00fd645bcf23342f27fe820ccf034a8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298850"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito:

* Richiedere agli utenti di fornire di nuovo i metodi di contatto
* Eliminare password per le app
* Richiedere l'autenticazione a più fattori su tutti i dispositivi attendibili

## <a name="require-users-to-provide-contact-methods-again"></a>Richiedere agli utenti di fornire di nuovo i metodi di contatto

Questa impostazione impone all'utente di completare di nuovo il processo di registrazione. Le app non basate su browser continuano a funzionare se l'utente dispone delle relative password.  È possibile eliminare le password dell'app degli utenti anche selezionando **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Come richiedere agli utenti di fornire di nuovo i metodi di contatto

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Un elenco di opzioni di azione rapida viene visualizzato a destra.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**.
   ![Richiedere agli utenti di fornire di nuovo i metodi di contatto](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

Le organizzazioni possono completare questi passaggi con PowerShell usando il seguente come guida per cancellare il `StrongAuthenticationMethods` attributo:

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
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Un elenco di opzioni di azione rapida viene visualizzato a destra.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![Eliminare tutte le password dell'app](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Ripristinare MFA in tutti i dispositivi memorizzati per un utente

Una delle funzionalità configurabili di Azure Multi-Factor Authentication è dare agli utenti la possibilità di contrassegnare i dispositivi come attendibili. Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Gli utenti possono rifiutare esplicitamente la verifica in due passaggi per un numero configurabile di giorni per i propri dispositivi regolari. Se un account viene compromesso o un dispositivo attendibile viene smarrito, è necessario poter rimuovere lo stato attendibile e richiedere di nuovo la verifica in due passaggi.

Se selezionata, **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati** agli utenti verrà richiesto per eseguire la verifica in due passaggi al successivo accesso, anche se il dispositivo come attendibile contrassegnati.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
3. A destra selezionare **Multi-Factor Authentication** sulla barra degli strumenti. Viene aperta la pagina dell'autenticazione a più fattori.
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Un elenco di opzioni di azione rapida viene visualizzato a destra.
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati**
   ![Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user.md)
