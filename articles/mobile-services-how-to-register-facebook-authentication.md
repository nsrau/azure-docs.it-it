<properties 
	pageTitle="Registrare le app per l'autenticazione Facebook - Servizi mobili" 
	description="Informazioni su come usare l'autenticazione Facebook nell'applicazione Servizi Mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrazione delle app per l'autenticazione di Facebook con Servizi mobili

Questo argomento descrive come registrare le app in modo da poter usare Facebook per l'autenticazione con Servizi mobili di Azure. 

> [AZURE.NOTE] Questa esercitazione riguarda [Servizi mobili di Azure], una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introduzione all'autenticazione</a> che illustra come eseguire l'accesso all'applicazione. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introduzione a Servizi mobili</a>.
	
Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account Facebook, andare alla pagina all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">Facebook.com</a>.

1. Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Sviluppatori di Facebook</a> e accedere con le credenziali dell'account Facebook.

2. (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **App** e quindi su **Register as a Developer**, accettare le condizioni e seguire la procedura di registrazione. 

   	![][0]

3. Fare clic su **Apps**, quindi su **Create a New App**.

   	![][1]

4. Scegliere un nome univoco per l'app, selezionare **Apps for Pages**, fare clic su **Create App** e completare la domanda di verifica.

   	![][2]

	L'app verrà registrata in Facebook 

5. Fare clic su **Settings**, digitare il nome di dominio del servizio mobile in **App Domains**, immettere un valore in **Contact Email**, quindi fare clic su **Add Platform** e selezionare **Website**.

   	![][3]

6. Digitare l'URL del servizio mobile in **Site URL** e quindi fare clic su **Save Changes**.

	![][4]

7. Fare clic su **Show**, digitare la propria password, se richiesto, quindi annotare i valori di **App ID** e **App Secret**. 

   	![][5]

	> [AZURE.NOTE] **Nota sulla sicurezza**
	Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.


8. Fare clic sulla scheda **Advanced**, immettere l'URL del servizio mobile con l'aggiunta del percorso _/login/facebook_ in **Valid OAuth redirect URIs**, quindi fare clic su **Save Changes**. 

	> [AZURE.NOTE] Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-facebook_, ovvero il servizio mobile come servizio .NET, ad esempio <code>https://todolist.azure-mobile.net/signin-facebook</code>.  
	
	![][7]

9. L'account di Facebook per il quale è stata definita la nuova app, ha la funzione di amministratore dell'app e dispone quindi dell'accesso all'app con privilegi di amministratore. Per autenticare altri account di Facebook, questi devono avere accesso all'app. Questo passaggio garantisce l'accesso pubblico in modo che l'app possa autenticare altri account di Facebook. Fare clic su **Status & Review**, quindi su **Yes** per abilitare l'accesso pubblico.

    ![][6]



È ora possibile usare un account di accesso di Facebook per l'autenticazione nell'app fornendo i valori dell'ID e del segreto dell'app a Servizi mobili.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Sviluppatori di Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Servizi mobili di Azure]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=47-->
