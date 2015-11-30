<properties
	pageTitle="Come configurare l'autenticazione Twitter per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione Twitter per un'applicazione dei servizi app."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Come configurare un'applicazione del servizio app per usare l'account di accesso di Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Twitter come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, visitare il sito <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrare l'applicazione con Twitter


1. Accedere al [portale di gestione di Azure] e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app Twitter.

2. Passare al sito Web [Twitter Developers], accedere con le credenziali dell'account Twitter e quindi fare clic su **Create New App**.

3. Digitare i valori per **Name** e **Description** per la nuova app. Incollare l’**URL** dell’applicazione per il valore **Website**. Nella casella **Callback URL** incollare quindi il valore di **URL callback** copiato in precedenza. Si tratta del gateway dell'app per dispositivi mobili con il percorso _/.auth/login/twitter/callback_ come suffisso. ad esempio `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Assicurarsi che sia in uso lo schema HTTPS.

    ![][0]
	

	> [AZURE.NOTE]Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di reindirizzamento utilizza l'URL del gateway URL con il percorso _/signin-twitter_.


3.  Nella parte inferiore della pagina, leggere e accettare le condizioni di utilizzo. Fare clic su **Create your Twitter application**. L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

4. Fare clic sulla scheda **Settings**, selezionare **Allow this application to be used to sign in with Twitter** e quindi fare clic su **Update Settings**.

5. Selezionare la scheda **Keys and Access Tokens**. Prendere nota dei valori di **Consumer Key (API Key)** e **Consumer secret (API Secret)**.

    > [AZURE.NOTE]Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.


## <a name="secrets"> </a>Aggiungere informazioni di Twitter all'applicazione


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Twitter**. Paste in the values you obtained earlier and click **Save**.


13. Nel [Portale di gestione di Azure] passare all'applicazione desiderata. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

14. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

15. Fare clic su **Twitter**. Incollare i valori relativi all'ID dell'app e al segreto dell'app ottenuti in precedenza. Fare quindi clic su **OK**.

    ![][1]
	
16. Per impostazione predefinita, il servizio app consente l'accesso ma non pone limiti per quanto riguarda il contenuto e le interfacce API accessibili nel sito. Questo è compito del codice dell'app. Se si desidera proteggere interamente il sito mediante la funzionalità di accesso di Twitter, modificare l'elenco a discesa **Azione da intraprendere se la richiesta non è autenticata** in modo da poter utilizzare l'opzione **Twitter**. Ciò richiede che tutte le richieste siano autenticate. Le richieste non autenticate verranno reindirizzate per l'accesso con Twitter.

17. Fare clic su **Save**.

È ora possibile usare un account Twitter per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portale di gestione di Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=Nov15_HO4-->