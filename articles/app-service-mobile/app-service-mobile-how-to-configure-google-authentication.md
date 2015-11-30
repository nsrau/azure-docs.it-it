<properties
	pageTitle="Come configurare l'autenticazione Google per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione Google per un'applicazione dei servizi app."
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
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Come configurare l'applicazione del servizio app per usare l'account di accesso di Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrare l'applicazione con Google

1. Accedere al [portale di gestione di Azure] e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app Google.
 
2. Passare al sito Web delle [API di Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

3. Nella barra di spostamento a sinistra, fare clic su **API & Auth**, quindi in **API Social** fare clic su **API Google+** > **Enable API**.

4. Fare clic su **API & Auth** > **Credenziali** > **OAuth consent screen**, quindi selezionare il proprio **Indirizzo di posta elettronica**, immettere un **Nome prodotto** e quindi fare clic su **Salva**.

5. Nella scheda **Credenziali** fare clic su **Aggiungi credenziali** > **ID client OAuth 2.0**, quindi selezionare **Applicazione Web**.

6. Incollare l'**URL** del servizio app per dispositivi mobili copiato in precedenza in **Authorized JavaScript Origins** e quindi incollare l’**URI di reindirizzamento** copiato in precedenza in **Authorized Redirect URI**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso _/.auth/login/google/callback_. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assicurarsi che sia in uso lo schema HTTPS. Fare quindi clic su **Crea**.


	> [AZURE.NOTE]Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di reindirizzamento utilizza l'URL del gateway URL con il percorso _/signin-google_.


7. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.


    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. Nel [Portale di gestione di Azure] passare all'applicazione desiderata. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

9. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

10. Fare clic su **Google**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

    ![][1]
	
11. Per impostazione predefinita, il servizio app consente l'accesso ma non pone limiti per quanto riguarda il contenuto e le interfacce API accessibili nel sito. Questo è compito del codice dell'app. Se si desidera proteggere interamente il sito mediante la funzionalità di accesso di Google, modificare l'elenco a discesa **Azione da intraprendere se la richiesta non è autenticata** in modo da poter utilizzare l'opzione **Google**. Ciò richiede che tutte le richieste siano autenticate. Le richieste non autenticate verranno reindirizzate per l'accesso con Google.

12. Fare clic su **Save**.

È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portale di gestione di Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO4-->