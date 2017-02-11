---
title: Report di Azure Multi-Factor Authentication
description: Viene descritto come modificare le impostazioni utente (ad esempio, come imporre agli utenti di ripetere il processo di registrazione).
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: curtand
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80eb9d882fa6b057623e0a5559baf111542359da


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud
Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito.  

* [Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto](#require-selected-users-to-provide-contact-methods-again)
* [Eliminare le password per le app esistenti degli utenti](#delete-users-existing-app-passwords)
* [Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente](#restore-mfa-on-all-suspended-devices-for-a-user)

Ciò è utile se un computer o dispositivo viene perso o rubato o se è necessario rimuovere l'accesso agli utenti.

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
Gli amministratori hanno la possibilità di ripristinare Multi-Factor Authentication su dispositivi e browser degli utenti. Questa operazione rimuove la memorizzazione di MFA da tutti i dispositivi e da tutti i browser dell'utente, che all'accesso successivo dovrà usare MFA.

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




<!--HONumber=Nov16_HO3-->


