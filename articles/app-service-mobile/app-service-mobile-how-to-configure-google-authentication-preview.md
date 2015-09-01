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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Come configurare un'applicazione per usare l'account di accesso di Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come configurare App per dispositivi mobili di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registrare l'applicazione con Google


1. Accedere al [portale di gestione di Azure] e passare all'app per dispositivi mobili. Copiare l'**URL**. Questa informazione verrà usata più avanti con l'app Google.
 
2. Fare clic su **Impostazioni**, **Autenticazione utente** e quindi fare clic su **Google**. Copiare il valore da **URI di reindirizzamento**. Verrà usato per configurare l'app Google.

3. Passare al sito Web delle <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API di Google</a>, accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

4. Nella barra di spostamento a sinistra fare clic su **API & Auth**. Fare clic su **Consent screen**. Selezionare il proprio indirizzo in **Email Address**, quindi immettere un nome prodotto in **Product Name**. Fare quindi clic su **Save**.

5. In **API & Auth** selezionare anche **APIs** e abilitare **Google+ API**. Si trova in **Social APIs**. È anche possibile cercare **Google+ API**.

6. Sempre in **API & Auth** selezionare **Credentials**, quindi **Create new Client ID**.

7. Selezionare **Web application**. Incollare l'**URL**copiato in precedenza in **Authorized JavaScript Origins** e quindi sostituire l'URL generato in **Authorized Redirect URI** con l'**URI di reindirizzamento** dell'app per dispositivi mobili copiato in precedenza. Questo URI corrisponde al gateway dell'app per dispositivi mobili con il percorso _/signin-google_ come suffisso, ad esempio `https://contosogateway.azurewebsites.net/signin-google`. Assicurarsi che sia in uso lo schema HTTPS. Fare clic su **Create client ID**.

     ![][0]

8. Nella schermata successiva, in **Client ID for web applications**, prendere nota dei valori di **Client ID** e **Client secret**.

    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'app per dispositivi mobili

7. Tornare al [portale di gestione di Azure], quindi nel pannello delle impostazioni di Google per l'app per dispositivi mobili, incollare i valori relativi all'ID client e al segreto client ottenuti in precedenza. Fare quindi clic su **Salva**.

     ![][1]


È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portale di gestione di Azure]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->