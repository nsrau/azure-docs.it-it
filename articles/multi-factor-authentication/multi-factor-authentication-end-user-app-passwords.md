<properties 
	pageTitle="Che cosa sono le password per le app in Azure MFA?" 
	description="Questa pagina consente agli utenti di comprendere il ruolo e la funzione delle password per le app in Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# Che cosa sono le password per le app in Azure Multi-Factor Authentication?

Alcune applicazioni non basate su browser, come il client di posta elettronica di Apple, utilizzano Exchange Active Sync e attualmente non supportano l'autenticazione a più fattori. L’autenticazione a più fattori viene abilitata per singolo utente. Questo implica che, se un utente è stato abilitato per l'autenticazione a più fattori e tenta di utilizzare applicazioni non basate su browser, non potrà farlo. La password dell’app consente tale operazione.

>[AZURE.NOTE]Autenticazione moderna per i client di Office 2013
>
> I client di Office 2013 (tra cui Outlook) supportano ora nuovi protocolli di autenticazione e possono essere abilitati per supportare Multi-Factor Authentication. Pertanto, una volta attivate, le password delle app non sono necessarie per l'utilizzo con i client di Office 2013. Per ulteriori informazioni, vedere l’[anteprima dell’annuncio pubblico per l’autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Come utilizzare le password delle app

Di seguito sono riportate alcune considerazioni da tenere presente per l’utilizzo delle password delle app.

- La password effettiva viene generata in automatico e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
- Al momento esiste un limite di 40 password per utente. Se si tenta di creare una password dopo che è stato raggiunto il limite, verrà richiesto di eliminare una delle password esistenti prima della creazione della nuova.
- È consigliabile creare password delle app per ogni dispositivo e non per ogni applicazione. Ad esempio, è possibile creare una sola password per il computer portatile e usarla per tutte le applicazioni su tale computer.
- Al primo accesso, viene fornita una password per le app. Se sono necessarie password aggiuntive, è possibile crearle.
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

Dopo aver creato una password dell’app, è possibile utilizzarla al posto della password originale con le applicazioni non basate su browser. Ad esempio, quando si utilizza l'autenticazione a più fattori e il client di posta elettronica di Apple sul telefono. Utilizzare la password dell’app per ignorare l’autenticazione a più fattori e continuare a lavorare.

### Creazione di password per le app
Durante l'accesso iniziale, viene fornita una password dell’app che è possibile utilizzare. Inoltre, è inoltre possibile creare le password per le app in un secondo momento. La procedura dipende dalla modalità di utilizzo dell’autenticazione a più fattori. Scegliere l'opzione più appropriata.

Come utilizzare l’autenticazione a più fattori|Descrizione
:------------- | :------------- | 
[Utilizzo con Office 365](multi-factor-authentication-end-user-manage-o365.md)| Significa che si desidera creare le password dell’app tramite il portale di Office 365.
[Utilizzo con Microsoft Azure](multi-factor-authentication-end-user-manage-azure.md)| Significa che è possibile creare le password dell’app tramite il portale di Azure.
[Non so](multi-factor-authentication-end-user-manage-myapps.md)|Significa che è possibile creare password dell’app tramite [https://myapps.microsoft.com](https://myapps.microsoft.com)




 

<!---HONumber=July15_HO2-->