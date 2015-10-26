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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Come configurare un'applicazione per usare l'account di accesso di Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-previewapp-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come configurare app per dispositivi mobili di Azure per l'uso di Twitter come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, visitare il sito <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrare l'applicazione con Twitter


1. Accedere al [portale di gestione di Azure] e passare all'app per dispositivi mobili. Copiare l'**URL**. Verrà usato per configurare l'app Twitter.

2. Fare clic su **Impostazioni**, **Autenticazione utente** e quindi su **Twitter**. Copiare il valore di **URL callback**. Verrà usato per configurare l'app Twitter.

3. Passare al sito Web [Twitter Developers], accedere con le credenziali dell'account Twitter e quindi fare clic su **Create New App**.

4. Digitare i valori per **Name** e **Description** per la nuova app. Incollare l'**URL dell'app per dispositivi mobili** come valore per **Website**. Nella casella **Callback URL** incollare quindi il valore di **URL callback** copiato in precedenza. Si tratta del gateway dell'app per dispositivi mobili con il percorso _/signin-twitter_ come suffisso. ad esempio `https://contosogateway.azurewebsites.net/signin-twitter`. Assicurarsi che sia in uso lo schema HTTPS.

    ![][0]

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni di utilizzo. Fare clic su **Create your Twitter application**. L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

4. Fare clic sulla scheda **Settings**, selezionare **Allow this application to be used to sign in with Twitter** e quindi fare clic su **Update Settings**.

5. Selezionare la scheda **Keys and Access Tokens**. Prendere nota dei valori di **Consumer Key (API Key)** e **Consumer secret (API Secret)**.

    > [AZURE.NOTE]Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.


## <a name="secrets"> </a>Aggiungere le informazioni di Twitter all'app per dispositivi mobili

1. Tornare al [portale di gestione di Azure], quindi nel pannello delle impostazioni di Twitter per l'app per dispositivi mobili incollare i valori relativi alla chiave API e alla chiave privata API ottenuti in precedenza. Fare quindi clic su **Salva**.

    ![][1]

È ora possibile usare un account Twitter per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portale di gestione di Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=Oct15_HO3-->