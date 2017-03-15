---
title: Gestire utenti e dispositivi come amministratore - Azure MFA | Documentazione Microsoft
description: Viene descritto come modificare le impostazioni utente (ad esempio, come imporre agli utenti di ripetere il processo di registrazione).
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud
Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito:

* Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto
* Eliminare le password per le app esistenti degli utenti
* Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

## <a name="require-selected-users-to-provide-contact-methods-again"></a>Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto
Questa impostazione impone all'utente di ripetere il processo di registrazione quando effettua l'accesso. Tenere presente che le app non basate su browser continuano a funzionare se l'utente dispone delle relative password dell'app.  È possibile eliminare le password dell'app degli utenti anche selezionando **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Come richiedere agli utenti di fornire di nuovo i metodi di contatto
1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente** . quindi farvi clic.
8. Inserire un segno di spunta in **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**.
   ![Fornire metodi di contatto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti
Vengono eliminate tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non viene creata una nuova password dell'app.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare le password per le app esistenti degli utenti
1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente** . quindi farvi clic.
8. Inserire un segno di spunta in **Eliminare le password per le app esistenti degli utenti**.
   ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Ripristinare MFA in tutti i dispositivi memorizzati per un utente
Una delle funzionalità configurabili di Azure Multi-Factor Authentication è dare agli utenti la possibilità di contrassegnare i dispositivi come attendibili. Per altre informazioni, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust)

Gli utenti possono scegliere di rifiutare esplicitamente la verifica in due passaggi per un numero configurabile di giorni per i propri dispositivi regolari. Se un account viene compromesso o un dispositivo attendibile viene smarrito, è necessario poter rimuovere lo stato attendibile e richiedere di nuovo la verifica in due passaggi.

L'impostazione **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati** indica che all'utente verrà chiesto di eseguire la verifica in due passaggi al successivo accesso, indipendentemente dal fatto che abbia scelto di contrassegnare il dispositivo come attendibile. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente
1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente** . quindi farvi clic.
8. Inserire un segno di spunta in **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati**
   ![Eliminare tutte le password dell'app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.

