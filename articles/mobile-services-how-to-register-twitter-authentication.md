<properties pageTitle="Registrazione per l'autenticazione di Twitter - Servizi mobili" description="Informazioni su come usare l'autenticazione Twitter con l'applicazione Servizi Mobili di Azure." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

#Registrare le app per l'autenticazione di Twitter con Servizi mobili

Questo argomento descrive come registrare le proprie app in modo da poter usare Twitter per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE] Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/it-it/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione <a href="http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started-users/">Introduzione all'autenticazione</a>, che illustra come consentire agli utenti l'accesso alla propria app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione <a href="http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started/">Introduzione a Servizi mobili</a>.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, passare alla pagina all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter Developers</a>, accedere con le credenziali dell'account Twitter, quindi fare clic su **Create a new application**.

   	![][1]

2. Immettere i valori per l'app in **Name**, **Description**, e **Website**, quindi digitare l'URL del servizio mobile con l'aggiunta del percorso _/login/twitter_ in **Callback URL**.

	>[AZURE.NOTE] Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-twitter_, ovvero il servizio mobile come servizio .NET, ad esempio <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni, digitare le parole CAPTCHA corrette e quindi fare clic su **Create your Twitter application**. 

   	![][3]

   	L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

6. Prendere nota dei valori in **Consumer key** e **Consumer secret**. 

   	![][4]

    > [AZURE.NOTE] Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

7. Fare clic sulla scheda **Settings**, scorrere verso il basso e selezionare **Allow this application to be used to sign in with Twitter**, quindi fare clic su **Update this Twitter application's settings**.

	![][5]

È ora possibile usare un account di accesso Twitter per l'autenticazione nell'app fornendo i valori di chiave e segreto consumer a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Sviluppatori di Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portale di gestione di Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
