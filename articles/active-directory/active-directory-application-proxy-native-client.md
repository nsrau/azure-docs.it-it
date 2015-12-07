<properties
	pageTitle="Come abilitare la pubblicazione delle app client native con le applicazioni proxy | Microsoft Azure"
	description="Illustra come abilitare la comunicazione tra le app client native e il connettore del proxy di applicazione di Azure AD per consentire l'accesso remoto sicuro alle app locali."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="kgremban"/>

# Come abilitare le app client native per l'interazione con le applicazioni proxy
Il proxy di applicazione di Azure Active Directory è ampiamente usato per pubblicare applicazioni browser come SharePoint, Outlook Web Access e applicazioni line-of-business personalizzate. Può inoltre essere usato per pubblicare le applicazioni back-end HTTP utilizzate tramite client nativi. A tale scopo, è necessario il supporto di token di Azure AD inviati in intestazioni HTTP Authorize standard.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


Il metodo consigliato per la pubblicazione di tali applicazioni è l'uso della libreria di autenticazione di Azure AD che si occupa di tutte le questioni correlate all'autenticazione e supporta molti ambienti client diversi. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). A tale scopo, seguire questa procedura:

1. Pubblicare il proxy di applicazione come qualsiasi altra applicazione, assegnare gli utenti e concedere loro licenze di base o premium. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](active-directory-application-proxy-publish.md).
2. Configurare l'applicazione nativa come indicato di seguito:
  3. Accedere al portale di gestione di Azure.
  4. Scegliere l'icona Active Directory dal menu a sinistra e quindi fare clic sulla directory desiderata.
  5. Nel menu in alto fare clic su Applicazioni. Se alla directory non è stata aggiunta alcuna applicazione, la pagina mostrerà solo il collegamento Aggiungi app. Fare clic sul collegamento o, in alternativa, fare clic sul pulsante Aggiungi sulla barra dei comandi.
  4. Nella pagina **Come procedere** fare clic sul collegamento **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
  5. Nella pagina **Informazioni sull'applicazione** specificare un nome per l'applicazione e scegliere **Applicazione client nativa** che rappresenta un'applicazione installata in un dispositivo, ad esempio un telefono o un computer. Al termine, fare clic sulla freccia nell'angolo inferiore destro della pagina.
  6. Nella pagina **Proprietà dell'app** specificare l'**URI di reindirizzamento** per l'applicazione client nativa, quindi fare clic sulla casella di controllo nell'angolo inferiore destro della pagina. </br>L'applicazione viene aggiunta e si viene quindi reindirizzati alla pagina Avvio rapido dell'applicazione.
8. Abilitare l'applicazione nativa da esporre ad altre applicazioni nella directory:
  9. Scegliere **Applicazioni** dal menu in alto, selezionare la nuova applicazione nativa e quindi fare clic su **Configura**.
  10. Scorrere verso il basso fino alla sezione **autorizzazioni per altre applicazioni**. Fare clic sul pulsante **Aggiungi applicazione** e selezionare l'applicazione proxy a cui si desidera concedere l'accesso dell'applicazione nativa e fare clic sul segno di spunta nell'angolo inferiore destro. Nel menu a discesa **Autorizzazioni delegate** selezionare la nuova autorizzazione. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4. Modificare il codice dell'applicazione nativa nel contesto di autenticazione di Active Directory Authentication Library (ADAL) per includere quanto segue:

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Le variabili devono essere sostituite come segue:


- **TenantId** è reperibile nel GUID nell'URL della pagina **Configurazione** dell'applicazione, subito dopo "/Directory/".
- **URL front-end** è l'URL front-end immesso nell'applicazione proxy e si trova nella pagina **Configurazione** dell'app proxy.
- **ID client** dell'app nativa che può trovarsi nella pagina **Configura** dell'applicazione nativa.
- **URI di reindirizzamento** dell'app nativa che può trovarsi nella pagina **Configura** dell'applicazione nativa.

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>Per altre informazioni sul flusso dell'applicazione nativa, vedere [Da applicazione nativa ad API Web](active-directory-authentication-scenarios.md#native-application-to-web-api).






## Passaggi successivi
Si può fare molto di più con il proxy dell'applicazione:


- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)


### Altre informazioni sul proxy dell'applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
* [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
* [Identità di Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_1125_2015-->