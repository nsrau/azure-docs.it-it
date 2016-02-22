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
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Come configurare l’applicazione del servizio app per usare l'account di accesso Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare l'account Microsoft come provider di autenticazione.


> [AZURE.NOTE]
Questo argomento illustra le modalità di utilizzo della funzione di autenticazione/autorizzazione del servizio app che, nella maggior parte delle applicazioni, sostituisce il gateway del servizio app. Le differenze di cui tener conto quando si usa il gateway sono indicate nelle note nell’argomento.


## <a name="register"> </a>Registrare l'applicazione con l'account Microsoft

1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l’app per l'Account Microsoft.

2. Passare alla pagina [My Applications] di Microsoft Account Developer Center e, se necessario, accedere con il proprio account Microsoft.

4. Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

5. Fare clic su **API Settings**. Selezionare **Yes** per **Mobile or desktop client app**. Nel campo **Redirect URL** immettere l'**URL di reindirizzamento** dell’applicazione e fare clic su **Save**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso _/.auth/login/microsoftaccount/callback_. ad esempio `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assicurarsi che sia in uso lo schema HTTPS.

	![][0]


	> [AZURE.NOTE]
	Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di reindirizzamento utilizza l'URL del gateway URL con il percorso _/signin-microsoft_.


6. Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**.


    > [AZURE.NOTE] Il segreto client è un'importante credenziale di sicurezza. Non condividere segreto client con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'applicazione

> [AZURE.NOTE]
Se si usa il gateway del servizio app, ignorare questa sezione e accedere al gateway nel portale. Selezionare **Settings**, **Identity**, e quindi **Microsoft Account**. Incollare i valori ottenuti nel passaggio precedente e fare clic su **Save**.


7. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

8. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

9. Fare clic su **Account Microsoft**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

    ![][1]

	Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

17. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati dall’account Microsoft, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Account Microsoft**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate all’account Microsoft per l'autenticazione.

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
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->