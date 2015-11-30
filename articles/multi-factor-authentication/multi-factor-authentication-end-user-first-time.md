<properties 
	pageTitle="Primo accesso ad Azure Multi-Factor Authentication" 
	description="In questa pagina viene descritta la prima esperienza d’accesso per gli utenti." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="billmath"/>
# Esperienza di installazione per Azure multi-Factor Authentication

 Le impostazioni per la verifica aggiuntiva di sicurezza vengono utilizzate quando un amministratore ha configurato l'account in modo tale che la verifica dell’identità dell’utente avvenga tramite password e risposta sul telefono dello stesso. Se un amministratore ha configurato l'account per richiedere la verifica aggiuntiva di sicurezza, **non sarà possibile effettuare l'accesso fino a quando non si completa il processo di autoregistrazione**.

## Scelta della modalità di utilizzo dell’autenticazione a più fattori

 La prima volta che si accede al proprio account dopo la configurazione dello stesso, verrà richiesto di avviare il processo di autoregistrazione. È possibile iniziare il processo facendo clic su **Configuralo subito**.

![Impostazione](./media/multi-factor-authentication-end-user-first-time/first.png)

Il processo di registrazione consente di specificare il metodo di verifica preferito. È possibile scegliere tra uno dei metodi presentati nella tabella di seguito. Per ulteriori informazioni e per la procedura dettagliata, fare clic su uno dei metodi.

Metodo|Descrizione
:------------- | :------------- | 
[Chiamata sul telefono cellulare](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Invia una chiamata vocale automatizzata sul telefono predisposto all'autenticazione. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non verrà sincronizzato con Active Directory locale.
[Messaggio di testo sul telefono cellulare](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Invia un messaggio di testo contenente un codice di verifica per l'utente. L'utente deve rispondere al messaggio con il codice di verifica o immettere il codice di verifica nell'interfaccia di accesso.
[Chiamata telefonica dell’ufficio](multi-factor-authentication-end-user-first-time-office-phone.md)|Invia una chiamata vocale automatizzata all'utente. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico.
[App per dispositivi mobili](multi-factor-authentication-end-user-first-time-mobile-app.md)|Invia una notifica sull'app Multi-Factor per dispositivi mobili, come il tablet o lo smartphone dell'utente. Per l’autenticazione, l'utente tocca "Verifica" all’interno dell’applicazione. In alternativa, l'applicazione può anche essere utilizzata come token OTP per l'autenticazione offline. Per l’autenticazione, l’utente inserisce il token nella schermata di accesso.<br><p> L'applicazione Multi-Factor Authentication può essere utilizzata in 2 diverse modalità per garantire una protezione aggiuntiva all’autenticazione a più fattori. Le modalità sono:<li>**Notifica**: in questa modalità, l'app Multi-Factor Authentication impedisce l'accesso agli account non autorizzati e arresta le transazioni illecite. L’avvertenza è una notifica push inviata sul telefono o sul dispositivo registrato. Visualizzare la notifica e, se del caso, selezionare Autentica. In caso contrario, è possibile scegliere Nega oppure Nega e segnala la notifica illecita. Per informazioni sulla segnalazione di notifiche illecite, consultare Come utilizzare la funzionalità Nega e Segnala la notifica illecita per Multi-Factor Authentication.</li><p><li>**Password monouso**: in questa modalità l'app Multi-Factor Authentication può essere utilizzata come token del software per generare un codice di verifica OATH. Questo codice di verifica quindi può pertanto essere immesso con il nome utente e con la password come seconda modalità di autenticazione.</li><br><p> L’app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/it-IT/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

 

<!---HONumber=Nov15_HO4-->