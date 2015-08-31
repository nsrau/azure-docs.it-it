<properties 
	pageTitle="Effettuare la registrazione per l'autenticazione per Facebook | Microsoft Azure" 
	description="Informazioni su come usare l'autenticazione Facebook nell'app Servizi Mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/27/2015" 
	ms.author="glenga"/>

# Registrare le app per l'autenticazione Facebook con Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Questo argomento illustra come registrare le app in modo da usare Facebook per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure], una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introduzione all'autenticazione</a> che illustra come consentire agli utenti l'accesso alla propria app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introduzione a Servizi mobili</a>.
	
Per completare la procedura descritta in questo argomento, è necessario disporre di un account Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account Facebook, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook Developers</a> e accedere con le proprie credenziali dell'account Facebook.

2. (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **My Apps** e quindi su **Register as a Developer**, accettare le condizioni e seguire la procedura di registrazione.

3. Fare clic su **My Apps** > **Add a New App** > **Advanced setup**.

4. Immettere un valore univoco per **Display name** per l'app, scegliere **Apps for Pages** in **Category**, quindi fare clic su **Create App ID** e infine completare l'esercizio relativo alla sicurezza.

	Verrà creato un nuovo ID per l'app di Facebook.

5. Fare clic su **Settings**, digitare il nome del dominio del servizio mobile in **App Domains**, immettere un valore facoltativo per **Contact Email**, fare clic su **Add Platform** e selezionare **Website**.

   	![][3]

6. Digitare l'URL del servizio mobile in **Site URL** e quindi fare clic su **Save Changes**.

7. Fare clic su **Show**, digitare la propria password, se richiesto, quindi annotare i valori di **App ID** e **App Secret**.

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app. &nbsp;

8. Fare clic sulla scheda **Advanced**, immettere l'URL del servizio mobile con l'aggiunta del percorso _/login/facebook_ in **Valid OAuth redirect URIs**, quindi fare clic su **Save Changes**. &nbsp;

     >[AZURE.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-facebook_, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-facebook`.
       

9. Fare clic su **Status & Review** > **Yes** per abilitare l'accesso pubblico generale all'app.

	L'account di Facebook usato per registrare la nuova app, ha la funzione di amministratore dell'app e dispone quindi dell'accesso all'app con privilegi di amministratore. Questo passaggio garantisce l'accesso pubblico in modo che l'app possa effettuare l'autenticazione mediante altri account di Facebook.


È ora possibile utilizzare un account di accesso di Facebook per l'autenticazione nell'app fornendo i valori dell'ID e del segreto dell'app a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Servizi mobili di Azure]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=August15_HO8-->