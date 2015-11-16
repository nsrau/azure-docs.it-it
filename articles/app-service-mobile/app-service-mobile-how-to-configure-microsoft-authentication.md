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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Come configurare un'applicazione per usare l'account di accesso Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare le app per dispositivi mobili di Azure per usare l'account Microsoft come provider di autenticazione.

## <a name="register"> </a>Registrare l'applicazione con l'account Microsoft

1. Accedere al [portale di gestione di Azure] e passare all'app per dispositivi mobili.

2. Fare clic su **Impostazioni**, **Autenticazione utente** e quindi su **Account Microsoft**. Copiare l'**URL di reindirizzamento**. Verrà usato per configurare una nuova app per l'Account Microsoft.

3. Passare alla pagina [My Applications] di Microsoft Account Developer Center e, se necessario, accedere con il proprio account Microsoft.

4. Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

5. Fare clic su **API Settings**. Selezionare **Yes** per **Mobile or desktop client app**. Nel campo **URL di reindirizzamento** immettere l'**URL di reindirizzamento** copiato in precedenza. Si tratta del gateway dell'app per dispositivi mobili con _/signin-microsoft_ come suffisso. Ad esempio: `https://contosogateway.azurewebsites.net/signin-microsoft`. Assicurarsi che sia in uso lo schema HTTPS. Dopo avere immesso l'URL di reindirizzamento, fare clic su **Save**.

	![][0]

	>[AZURE.NOTE]Per la registrazione di un'app con account Microsoft esistente, potrebbe essere necessario abilitare prima **Enhanced redirection security**.

4. Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**.

    > [AZURE.NOTE]Il segreto client è un'importante credenziale di sicurezza. Non condividere segreto client con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'app per dispositivi mobili

1. Tornare al [portale di gestione di Azure], nel pannello delle impostazioni dell'account Microsoft per l'app per dispositivi mobili, incollare i valori relativi all'ID client e alla chiave privata client ottenuti in precedenza. Fare quindi clic su **Salva**.

    ![][1]

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

<!---HONumber=Nov15_HO2-->