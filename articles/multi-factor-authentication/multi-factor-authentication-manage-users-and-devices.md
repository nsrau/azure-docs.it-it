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
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Gestione delle impostazioni utente con Azure Multi-Factor Authentication nel cloud

Come amministratore, è possibile gestire le impostazioni relative all'utente e al dispositivo riportate di seguito.

- [Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto](#require-selected-users-to-provide-contact-methods-again)
- [Eliminare le password per le app esistenti degli utenti](#delete-users-existing-app-passwords)
- [Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente (anteprima pubblica)](#restore-mfa-on-all-suspended-devices-for-a-user)






Ciò è utile se un computer o dispositivo viene perso o rubato o se è necessario rimuovere l'accesso agli utenti.


## Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto

Questa impostazione impone all'utente di ripetere il processo di registrazione quando effettua l'accesso. Tenere presente che le app non basate su browser continuano a funzionare se l'utente dispone delle relative password dell'app. È possibile eliminare le password dell'app degli utenti anche selezionando **Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati**.

### Come richiedere agli utenti di fornire di nuovo i metodi di contatto

<ol>
<li>Accedere al portale di gestione di Azure.</li>
<li>A sinistra fare clic su Active Directory.</li>
<li>In Directory fare clic sulla directory relativa all'utente desiderato.</li>
<li>Nella parte superiore fare clic su Utenti.</li>
<li>Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina di multi-factor authentication. <li>Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.</li> <li>A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. Fare clic su di esso.</li> <li>Inserire un segno di spunta in **Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto**.</li>

![Fornire metodi di contatto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>Fare clic su Salva.</li>
<li>Fare clic su Chiudi</li>

## Eliminare le password per le app esistenti degli utenti

Vengono eliminate tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non viene creata una nuova password dell'app.

### Come eliminare le password per le app esistenti degli utenti

<ol>
<li>Accedere al portale di gestione di Azure.</li>
<li>A sinistra fare clic su Active Directory.</li>
<li>In Directory fare clic sulla directory relativa all'utente desiderato.</li>
<li>Nella parte superiore fare clic su Utenti.</li>
<li>Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina di multi-factor authentication. <li>Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.</li> <li>A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. Fare clic su di esso.</li> <li>Inserire un segno di spunta in **Eliminare le password per le app esistenti degli utenti**.</li> ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png) <li>Fare clic su Salva.</li> <li>Fare clic su Chiudi.</li>





## Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

Gli amministratori hanno la possibilità di reimpostare Multi-Factor Authentication su dispositivi e browser. Ciò avviene tramite il ripristino di Multi-Factor Authentication per dispositivi e browser di un utente. Quando si esegue questa operazione, la sospensione viene rimossa da tutti i dispositivi e i browser dell'utente.

### Come ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi per un utente

<ol>
<li>Accedere al portale di gestione di Azure.</li>
<li>A sinistra fare clic su Active Directory.</li>
<li>In Directory fare clic sulla directory relativa all'utente desiderato.</li>
<li>Nella parte superiore fare clic su Utenti.</li>
<li>Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina di multi-factor authentication. <li>Individuare l'utente che si desidera gestire e inserire un segno di spunta nella casella situata accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.</li> <li>A destra viene visualizzato il collegamento **Gestisci le impostazioni dell'utente**. Fare clic su di esso.</li> <li>Inserire un segno di spunta in Ripristinare Multi-Factor Authentication in tutti i dispositivi sospesi.</li> ![Eliminare password per le app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>Fare clic su Salva.</li> <li>Fare clic su Chiudi.</li>

<!---HONumber=AcomDC_1125_2015-->