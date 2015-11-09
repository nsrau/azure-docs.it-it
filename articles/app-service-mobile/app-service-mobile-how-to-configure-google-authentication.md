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

# Come configurare un'applicazione per usare l'account di accesso di Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare App per dispositivi mobili di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Accedere al [portale di gestione di Azure] e passare all'app per dispositivi mobili. Copiare l'**URL**. Questa informazione verrà usata più avanti con l'app Google.
 
2. Fare clic su **Impostazioni**, **Autenticazione utente** e quindi fare clic su **Google**. Copiare il valore da **URI di reindirizzamento**. Verrà usato per configurare l'app Google.

3. Passare al sito Web delle [API di Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

4. Nella barra di spostamento a sinistra, fare clic su **API & Auth**, quindi in **API Social** fare clic su **API Google+** > **Enable API**.

5. Fare clic su **API & Auth** > **Credenziali** > **OAuth consent screen**, quindi selezionare il proprio **Indirizzo di posta elettronica**, immettere un **Nome prodotto** e quindi fare clic su **Salva**.

6. Nella scheda **Credenziali** fare clic su **Aggiungi credenziali** > **ID client OAuth 2.0**, quindi selezionare **Applicazione Web**.

7. Incollare l'**URL** delle app per dispositivi mobili copiato in precedenza in **Authorized JavaScript Origins** e quindi incollare l’**URI di reindirizzamento** copiato in precedenza in **Authorized Redirect URI**. L’URI di reindirizzamento corrisponde al gateway dell'app per dispositivi mobili con il percorso _/signin-google_ come suffisso, Ad esempio: `https://contosogateway.azurewebsites.net/signin-google`. Assicurarsi che sia in uso lo schema HTTPS. Fare quindi clic su **Crea**.

8. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.

    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

9. Tornare al [portale di gestione di Azure], quindi nel pannello delle impostazioni di Google per l'app per dispositivi mobili, incollare i valori relativi all'ID client e al segreto client ottenuti in precedenza. Fare quindi clic su **Salva**.

     ![][1]

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
 

<!---HONumber=Nov15_HO1-->