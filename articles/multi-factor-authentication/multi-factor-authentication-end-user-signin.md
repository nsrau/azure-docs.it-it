<properties
	pageTitle="Azure MFA. Esperienza di accesso con Azure Multi-Factor Authentication"
	description="Questa pagina fornirà indicazioni su dove trovare le istruzioni per visualizzare i diversi metodi di accesso disponibili con Azure MFA."
	keywords="autenticazione utente, esperienza di accesso con telefono cellulare, accesso con telefono dell'ufficio"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>  

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Esperienza di accesso con Azure Multi-Factor Authentication
> [AZURE.NOTE]  La documentazione fornita in questa pagina illustra una tipica esperienza di accesso. Per ricevere assistenza sull’accesso, vedere [Problemi di Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## Come sarà l'esperienza di accesso?
A seconda di come si accede e di come si utilizza l'autenticazione a più fattori, l'esperienza sarà diversa. In questa sezione verranno fornite informazioni su cosa aspettarsi quando si accede. Scegliere quello che meglio descrive l'operazione:


Cosa si farà?|Descrizione
:------------- | :------------- |
[Accesso con telefono cellulare o dell'ufficio](#signing-in-with-mobile-or-office-phone) | Questo è ciò che può accadere effettuando l'accesso tramite telefono cellulare o dell'ufficio.
[Accesso con l'app Authenticator Microsoft mediante la notifica](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Ecco cosa aspettarsi quando si usa l'app Authenticator Microsoft con le notifiche.
[Accesso con l'app Microsoft Authenticator mediante il codice di verifica](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Ecco cosa aspettarsi quando si usa l'app Microsoft Authenticator con un codice di verifica.
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

## Accesso con l'app Authenticator Microsoft mediante la notifica

Le informazioni seguenti descriveranno l'esperienza di utilizzo dell'autenticazione a più fattori con l'app Microsoft Authenticator quando si riceve una notifica.

### Per eseguire l'accesso con una notifica inviata dall'app Microsoft Authenticator

- Accedere a un'applicazione o servizio come Office 365 utilizzando nome utente e password.
- Microsoft invierà una notifica.

![Microsoft invia una notifica](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Rispondere al telefono e premere il tasto di verifica. Se la società richiede un PIN, verrà richiesto qui.

![Verificare](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)  

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Ora dovrebbe essere stato effettuato l’accesso.


## Accesso con l'app Microsoft Authenticator mediante il codice di verifica

Le informazioni seguenti descrivono l'esperienza di utilizzo dell'autenticazione a più fattori con l'app Microsoft Authenticator quando la si usa con un codice di verifica.

### Per effettuare l'accesso usando un codice di verifica con l'app Microsoft Authenticator

- Accedere a un'applicazione o servizio come Office 365 utilizzando nome utente e password.
- Microsoft richiederà un codice di verifica.

![Inserire il codice di verifica dell'app](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Aprire l'app Microsoft Authenticator sul telefono e immettere il codice nella casella da cui si sta effettuando l'accesso.

![Ottenere il codice](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)  



- Ora dovrebbe essere stato effettuato l’accesso.


## Accesso con un metodo alternativo


Nella sezione seguente si mostrerà come effettuare l'accesso con un metodo alternativo quando il metodo principale può non essere disponibile.

### Accedere con un metodo alternativo

- Accedere a un'applicazione o servizio, ad esempio Office 365 utilizzando nome utente e password.
- Selezionare Utilizza un'opzione di verifica diversa. Verrà offerta una scelta di opzioni differenti. Il numero visualizzato dipenderà da quante opzioni sono state impostate.

![Utilizzare un metodo alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Scegliere un metodo alternativo ed effettuare l’accesso.

<!---HONumber=AcomDC_0921_2016-->