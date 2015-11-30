<properties
	pageTitle="Come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="mahender"/>

# Come configurare l’applicazione del servizio app per usare l'account di accesso Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare l'account Microsoft come provider di autenticazione.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrare l'applicazione con l'account Microsoft

1. Accedere al [portale di gestione di Azure] e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l’app per l'Account Microsoft.

2. Passare alla pagina [My Applications] di Microsoft Account Developer Center e, se necessario, accedere con il proprio account Microsoft.

4. Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

5. Fare clic su **API Settings**. Selezionare **Yes** per **Mobile or desktop client app**. Nel campo **URL di reindirizzamento** immettere l'**URL di reindirizzamento** dell’applicazione e fare clic su **Salva**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso _/.auth/login/microsoftaccount/callback_. ad esempio `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assicurarsi che sia in uso lo schema HTTPS.

	![][0]


	> [AZURE.NOTE]Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di reindirizzamento utilizza l'URL del gateway URL con il percorso _/signin-microsoft_.


6. Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**.


    > [AZURE.NOTE]Il segreto client è un'importante credenziale di sicurezza. Non condividere segreto client con altri e non distribuirlo all'interno di un'applicazione client.
	

## <a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'applicazione


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Microsoft Account**. Paste in the values you obtained earlier and click **Save**.


7. Nel [Portale di gestione di Azure] passare all'applicazione desiderata. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

8. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

9. Fare clic su **Account Microsoft**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

    ![][1]
	
11. Per impostazione predefinita, il servizio app consente l'accesso ma non pone limiti per quanto riguarda il contenuto e le interfacce API accessibili nel sito. Questo è compito del codice dell'app. Se si desidera proteggere interamente il sito mediante la funzionalità di accesso dell’account Microsoft, modificare l'elenco a discesa **Azione da intraprendere se la richiesta non è autenticata** in modo da poter utilizzare l'opzione **Account Microsoft**. Ciò richiede che tutte le richieste siano autenticate. Le richieste non autenticate verranno reindirizzate per l'accesso con l’account Microsoft.

11. Fare clic su **Save**.


È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portale di gestione di Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->