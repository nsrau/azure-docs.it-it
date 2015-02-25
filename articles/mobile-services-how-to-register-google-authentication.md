<properties pageTitle="Registrazione per l'autenticazione Google - Servizi mobili" description="Informazioni su come registrare le proprie app in modo da poter usare Google per l'autenticazione con Servizi mobili di Azure." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Registrazione delle app per l'autenticazione di Google con Servizi mobili

Questo argomento descrive come registrare le proprie app in modo da poter usare Google per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE] Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/it-it/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione <a href="http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started-users/">Introduzione all'autenticazione</a>, che illustra come consentire agli utenti l'accesso alla propria app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione <a href="http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started/">Introduzione a Servizi mobili</a>.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, andare alla pagina all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Passare al sito Web delle <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API di Google</a>, accedere con le credenziali dell'account Google, fare clic su **Create project**, specificare un valore in **Project name**, quindi fare clic su **Crea**.

   	![][1]

2. Fare clic su **Consent screen**, selezionare **Email Address**, immettere il nome di un prodotto in **Product Name**, quindi fare clic su **Save**. 

3. Fare clic su **API & Auth**, selezionare **Credentials**, quindi **Create new Client ID**.

   	![][2]

4. Selezionare **Web application** e digitare l'URL del servizio mobile in **Authorized JavaScript Origins**, sostituire l'URL generato in **Authorized Redirect URI** con l'URL del servizio mobile con l'aggiunta del percorso _/login/google_, quindi fare clic su **Create client ID**.

	>[AZURE.NOTE] Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-google_, ovvero il servizio mobile come servizio .NET, ad esempio <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. In **Client ID for web applications** prendere nota dei valori di **Client ID** e **Client secret**. 

   	![][4]

    > [AZURE.IMPORTANT] Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

È ora possibile usare un account di accesso Google per l'autenticazione nell'app fornendo i valori dell'ID e del segreto client a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portale di gestione di Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
