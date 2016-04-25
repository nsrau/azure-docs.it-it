<properties 
	pageTitle="Report di Azure Multi-Factor Authentication" 
	description="Viene descritto come modificare le impostazioni utente (ad esempio, come imporre agli utenti di ripetere il processo di registrazione)." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/11/2016" 
	ms.author="billmath"/>

# Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito.

- [Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto](#require-selected-users-to-provide-contact-methods-again)
- [Eliminare le password per le app esistenti degli utenti](#delete-users-existing-app-passwords)
- [Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente](#restore-mfa-on-all-suspended-devices-for-a-user)






Ciò è utile se un computer o dispositivo viene perso o rubato o se è necessario rimuovere l'accesso agli utenti.


## Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto

Questa impostazione impone all'utente di ripetere il processo di registrazione quando effettua l'accesso. Tenere presente che le app non basate su browser continuano a funzionare se l'utente dispone delle relative password dell'app. È possibile eliminare le password dell'app degli utenti anche selezionando **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.

### Come richiedere agli utenti di fornire di nuovo i metodi di contatto




1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. quindi farvi clic.
8. Inserire un segno di spunta in **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**. ![Fornire metodi di contatto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Fare clic su Salva.
11. Fare clic su Chiudi

## Eliminare le password per le app esistenti degli utenti

Vengono eliminate tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non viene creata una nuova password dell'app.

### Come eliminare le password per le app esistenti degli utenti

1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. quindi farvi clic. 
8. Inserire un segno di spunta in **Eliminare le password per le app esistenti degli utenti**. ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Fare clic su Salva.
10. Fare clic su Chiudi.

## Ripristinare MFA in tutti i dispositivi memorizzati per un utente

Gli amministratori hanno la possibilità di ripristinare Multi-Factor Authentication su dispositivi e browser degli utenti. Questa operazione rimuove la memorizzazione di MFA da tutti i dispositivi e da tutti i browser dell'utente, che all'accesso successivo dovrà usare MFA.

### Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente desiderato.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. quindi farvi clic.
8. Inserire un segno di spunta in **Ripristina l'autenticazione a più fattori in tutti i dispositivi memorizzati** ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.

<!---HONumber=AcomDC_0413_2016-->