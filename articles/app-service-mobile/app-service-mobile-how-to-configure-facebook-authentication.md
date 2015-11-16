<properties
	pageTitle="Come configurare l'autenticazione Facebook per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione Facebook per un'applicazione dei servizi app."
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

# Come configurare un'applicazione per usare l'account di accesso di Facebook

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare le app per dispositivi mobili di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register"> </a>Registrare l'applicazione con Facebook

1. Accedere al [portale di gestione di Azure] e passare all'app per dispositivi mobili. Copiare l'**URL**. Verrà usato per configurare l'app Facebook.
 
2. Fare clic su **Impostazioni**, **Autenticazione per dispositivi mobili** e quindi fare clic su **Facebook**. Copiare quindi l'**URI di reindirizzamento** dal pannello Facebook. Verrà usato con l'app Facebook.
 
3. In un'altra finestra del browser passare al sito Web [Facebook Developers] e accedere con le credenziali dell'account Facebook.

4. (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **Apps** e quindi su **Register as a Developer**, accettare le condizioni e seguire la procedura di registrazione.

5. Fare clic su **My Apps** e quindi su **Create a New App**.

6. Selezionare **Website** come piattaforma. Scegliere un nome univoco per l'app e quindi fare clic su **Create New Facebook App ID**.

7. Scegliere una categoria per l'applicazione dall'elenco a discesa. Fare clic su **Create App ID**.

8. Nella pagina successiva scegliere **Skip Quick Start** in alto a destra. Si passerà al dashboard dello sviluppatore per la propria applicazione.

9. Nel campo **App Secret** fare clic su **Show**, fornire la password se richiesto, quindi prendere nota dei valori di **App ID** e **App Secret**. Questi valori verranno impostati nel pannello delle impostazioni di autenticazione di Facebook dell'app per dispositivi mobili.

	> [AZURE.NOTE]**Nota sulla sicurezza** Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

10. Nella barra di spostamento a sinistra fare clic su **Settings**. Digitare l'**URL** dell'app per dispositivi mobili in **App Domains** e quindi immettere un indirizzo di posta elettronica in **Contact Email**.

    ![][0]

11. Se non viene visualizzata una sezione "Website" in basso, fare clic su **Add Platform** e selezionare **Website**. Digitare l'**URL** dell'app per dispositivi mobili in **Site URL** e quindi fare clic su **Save Changes**.

12. Fare clic sulla scheda **Advanced** e aggiungere l'**URI di reindirizzamento** dell'app per dispositivi mobili copiato in precedenza in **Valid OAuth redirect URIs**. Fare clic su **Save Changes**. L'URI di reindirizzamento corrisponde all'URL del gateway dell'app per dispositivi mobili con l'aggiunta del percorso _/signin-facebook_ come suffisso. Ad esempio: `https://contosogateway.azurewebsites.net/signin-facebook`. Assicurarsi che sia in uso lo schema HTTPS.

13. L'account di Facebook usato per registrare l'applicazione sarà un account di amministratore dell'app. A questo punto, solo gli amministratori potranno effettuare l'accesso a questa applicazione. Per autenticare altri account di Facebook, fare clic su **Status & Review** nella barra di spostamento a sinistra. quindi su **Yes** per abilitare l'accesso pubblico.


## <a name="secrets"> </a>Aggiungere le informazioni di Facebook all'app per dispositivi mobili


12. Tornare al [portale di gestione di Azure] e passare di nuovo al pannello delle impostazioni Facebook per l'app per dispositivi mobili. Incollare i valori relativi all'ID dell'app e al segreto dell'app ottenuti in precedenza. Fare quindi clic su **Salva**.

    ![][1]

È ora possibile usare un account di Facebook per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /it-IT/develop/mobile/tutorials/get-started-with-users-dotnet/
[portale di gestione di Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO2-->