---
title: Gestire utenti e dispositivi come amministratore - Azure MFA | Documentazione Microsoft
description: Viene descritto come modificare le impostazioni utente (ad esempio, come imporre agli utenti di ripetere il processo di registrazione).
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: e9b8504d4a59cf0fae69a4e975d6f834028066d5
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

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
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**. Viene aperta la pagina dell'autenticazione a più fattori. 
4. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Sulla destra viene visualizzato un elenco di opzioni di azione rapida. 
5. Selezionare **Gestisci le impostazioni dell'utente**.
6. Selezionare la casella accanto a **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**.
   ![Fornire metodi di contatto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
7. Fare clic su **save**.
8. Fare clic su **chiudi**.

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti
Questa impostazione elimina tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**. Viene aperta la pagina dell'autenticazione a più fattori. 
6. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Sulla destra viene visualizzato un elenco di opzioni di azione rapida. 
7. Selezionare **Gestisci le impostazioni dell'utente**.
8. Selezionare la casella accanto a **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.
   ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Fare clic su **save**.
10. Fare clic su **chiudi**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Ripristinare MFA in tutti i dispositivi memorizzati per un utente
Una delle funzionalità configurabili di Azure Multi-Factor Authentication è dare agli utenti la possibilità di contrassegnare i dispositivi come attendibili. Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Gli utenti possono rifiutare esplicitamente la verifica in due passaggi per un numero configurabile di giorni per i propri dispositivi regolari. Se un account viene compromesso o un dispositivo attendibile viene smarrito, è necessario poter rimuovere lo stato attendibile e richiedere di nuovo la verifica in due passaggi.

L'impostazione **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati** indica che all'utente verrà chiesto di eseguire la verifica in due passaggi al successivo accesso, indipendentemente dal fatto che abbia scelto di contrassegnare il dispositivo come attendibile. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**. Viene aperta la pagina dell'autenticazione a più fattori. 
6. Selezionare la casella accanto a uno o più utenti che si desidera gestire. Sulla destra viene visualizzato un elenco di opzioni di azione rapida. 
7. Selezionare **Gestisci le impostazioni dell'utente**.
8. Selezionare la casella accanto a **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati**
   ![Eliminare tutte le password dell'app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png).
9. Fare clic su **save**.
10. Fare clic su **chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

- Se gli utenti hanno bisogno di supporto, suggerire loro di vedere il [Manuale dell'utente per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user.md)

