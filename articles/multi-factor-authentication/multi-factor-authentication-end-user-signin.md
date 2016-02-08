<properties 
	pageTitle="Azure MFA. Esperienza di accesso con Azure Multi-Factor Authentication" 
	description="Questa pagina fornirà indicazioni su dove trovare le istruzioni per visualizzare i diversi metodi di accesso disponibili con Azure MFA."
	keywords="autenticazione utente, esperienza di accesso con telefono cellulare, accesso con telefono dell'ufficio" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/25/2016" 
	ms.author="billmath"/>

# Esperienza di accesso con Azure Multi-Factor Authentication
> [AZURE.NOTE]  La documentazione fornita in questa pagina illustra una tipica esperienza di accesso. Per ricevere assistenza sull’accesso, vedere [Problemi di Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## Come sarà l'esperienza di accesso?
A seconda di come si accede e di come si utilizza l'autenticazione a più fattori, l'esperienza sarà diversa. In questa sezione verranno fornite informazioni su cosa aspettarsi quando si accede. Scegliere quello che meglio descrive l'operazione:


Cosa si farà?|Descrizione
:------------- | :------------- | 
[Accesso con telefono cellulare o dell'ufficio](#signing-in-with-mobile-or-office-phone) | Questo è ciò che può accadere effettuando l'accesso tramite telefono cellulare o dell'ufficio.
[Accesso con l’app per dispositivi mobili utilizzando la notifica](#signing-in-with-the-mobile-app-using-notification) | Questo è ciò che può accadere effettuando l'accesso tramite app per dispositivi mobili con notifiche.
[Accesso con app per dispositivi mobili utilizzando il codice di verifica](#signing-in-with-the-mobile-app-using-verification-code)|Questo è ciò che può accadere effettuando l'accesso tramite app per dispositivi mobili con un codice di verifica.
[Accesso con un metodo alternativo](#signing-in-with-an-alternate-method)|Verrà illustrato cosa aspettarsi se si desidera utilizzare un metodo alternativo.

## Accesso con telefono cellulare o dell'ufficio

Le informazioni seguenti descriveranno l'esperienza di utilizzo dell'autenticazione a più fattori con il telefono cellulare o dell'ufficio.

### Effettuare l'accesso con una chiamata al telefono cellulare o dell’ufficio

- Accedere a un'applicazione o servizio come Office 365 utilizzando nome utente e password.
- Microsoft effettuerà una telefonata.

![Chiamate di Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Rispondere al telefono e premere il tasto #.

![Risposta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Ora dovrebbe essere stato effettuato l'accesso.</li>

## Accesso con l’app per dispositivi mobili utilizzando la notifica

Le informazioni seguenti descriveranno l'esperienza di utilizzo dell'autenticazione a più fattori con l’app per dispositivi mobili quando si riceve una notifica.

### Effettuare l'accesso con una notifica inviata all'app per dispositivi mobili

- Accedere a un'applicazione o servizio come Office 365 utilizzando nome utente e password.
- Microsoft invierà una notifica.

![Microsoft invia una notifica](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Rispondere al telefono e premere il tasto di verifica.

![Verificare](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)


- Ora dovrebbe essere stato effettuato l’accesso.


## Accesso con app per dispositivi mobili utilizzando il codice di verifica

Le informazioni seguenti descriveranno l'esperienza di utilizzo dell'autenticazione a più fattori con l’app per dispositivi mobili quando si utilizza un codice di verifica.

### Accedere utilizzando un codice di verifica con l’app per dispositivi mobili

- Accedere a un'applicazione o servizio come Office 365 utilizzando nome utente e password.
- Microsoft richiederà un codice di verifica.

![Inserire il codice di verifica dell'app](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Aprire l'app Azure Authenticator sul telefono e immettere il codice nella casella da cui si sta effettuando l’accesso.

![Ottenere il codice](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)

- Ora dovrebbe essere stato effettuato l’accesso.


## Accesso con un metodo alternativo


Nella sezione seguente si mostrerà come effettuare l'accesso con un metodo alternativo quando il metodo principale può non essere disponibile.

### Accedere con un metodo alternativo

- Accedere a un'applicazione o servizio, ad esempio Office 365 utilizzando nome utente e password.
- Selezionare Utilizza un'opzione di verifica diversa. Verrà offerta una scelta di opzioni differenti. Il numero visualizzato dipenderà da quante opzioni sono state impostate.

![Utilizzare un metodo alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Scegliere un metodo alternativo ed effettuare l’accesso.

 

<!---HONumber=AcomDC_0128_2016-->