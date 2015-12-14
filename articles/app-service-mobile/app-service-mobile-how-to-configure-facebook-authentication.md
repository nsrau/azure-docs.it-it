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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# Come configurare un'applicazione del servizio App per usare l'account di accesso di Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare Servizio app di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

> [AZURE.NOTE]Questo argomento illustra le modalità di utilizzo della funzione di autenticazione/autorizzazione del servizio app che, nella maggior parte delle applicazioni, sostituisce il gateway del servizio app. Le differenze di cui tener conto quando si usa il gateway sono indicate nelle note nell’argomento.


## <a name="register"> </a>Registrare l'applicazione con Facebook

1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app Facebook.
 
2. In un'altra finestra del browser passare al sito Web [Facebook Developers] e accedere con le credenziali dell'account Facebook.

3. (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **App** > **Registrarsi come uno Sviluppatore**, quindi accettare le condizioni e seguire la procedura di registrazione.

4. Fare clic su **Le mie App** > **Aggiungi una nuova App** > **Sito web** immettere un nome univoco per l'app, quindi fare clic su **Crea nuovo ID dell’app Facebook**.

6. Selezionare una categoria per l'applicazione dall'elenco a discesa, quindi fare clic su **Crea ID dell’App** e nella pagina successiva, fare clic su **Salta Avvio rapido**. Si passa al dashboard dello sviluppatore per la propria applicazione.

8. Nel campo **App Secret** fare clic su **Show**, fornire la password se richiesto, quindi prendere nota dei valori di **App ID** e **App Secret**. L'applicazione verrà configurata in questo modo più avanti.

	> [AZURE.NOTE]**Nota sulla sicurezza** Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

9. Sulla barra di spostamento a sinistra, fare clic su **Impostazioni**, digitare l’**URL** dell'app per dispositivi mobili in **Domini dell’App** e quindi immettere un **Indirizzo email del contatto**.

    ![][0]

10. Se non viene visualizzata una sezione del sito Web di seguito, fare clic su **Aggiungi piattaforma** > **Sito web**, immettere l’**URL** dell'App per dispositivi mobili nel campo **URL sito**, quindi fare clic su **Salva modifiche**.

11. Fare clic sulla scheda **Avanzate** e aggiungere l’**URI di reindirizzamento** dell’applicazione a **URI di reindirizzamento OAuth validi**, poi fare clic su **Salva modifiche**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso _/.auth/login/facebook/callback_. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assicurarsi che sia in uso lo schema HTTPS.


	> [AZURE.NOTE]Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di reindirizzamento utilizza l'URL del gateway URL con il percorso _/signin-facebook_.


12. L'account di Facebook usato per registrare l'applicazione sarà un account di amministratore dell'app. A questo punto, solo gli amministratori potranno effettuare l'accesso a questa applicazione. Per autenticare altri account di Facebook, fare clic su **Status & Review** nella barra di spostamento a sinistra. quindi su **Yes** per abilitare l'accesso pubblico.


## <a name="secrets"> </a>Aggiungere le informazioni di Facebook all'applicazione

> [AZURE.NOTE]Se si usa il gateway del servizio app, ignorare questa sezione e accedere al gateway nel portale. Selezionare **Impostazioni**, **Identità**, e quindi **Facebook**. Incollare i valori ottenuti nel passaggio precedente e fare clic su **Salva**.


13. Nel [portale di Azure], passare all'applicazione. Fare clic su **impostazioni** > **Autenticazione/autorizzazione**, e assicurarsi che **l'autenticazione del servizio App** sia su **Attiva**.

15. Fare clic su **Facebook**, incollare nell’ID dell’app e nel segreto dell’app i valori ottenuti in precedenza, e abilitare facoltativamente tutti gli ambiti richiesti dall'applicazione, poi fare clic su **OK**.

    ![][1]
	
	Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

17. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Facebook, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Facebook**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Facebook per l'autenticazione.

17. Fare clic su **Save**.

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
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->