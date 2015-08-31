<properties 
	pageTitle="Effettuare la registrazione per l'autenticazione Twitter | Microsoft Azure" 
	description="Informazioni su come usare l'autenticazione Twitter con l'applicazione Servizi Mobili di Azure." 
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
	ms.date="08/08/2015" 
	ms.author="glenga"/>

#Registrare le app per l'autenticazione Twitter con Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

In questo argomento viene illustrato come registrare le proprie app in modo da poter utilizzare Twitter per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introduzione all'autenticazione</a> che illustra come consentire agli utenti l'accesso alla propria app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introduzione a Servizi mobili</a>.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter Developers</a>, accedere con le credenziali dell'account Twitter e fare clic su **Create a new application**.

   	![][1]

2. Immettere i valori per l'app in **Name**, **Description** e **Website**, quindi digitare l'URL del servizio mobile con l'aggiunta del percorso _/login/twitter_ in **Callback URL**.

	>[AZURE.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento corrisponde all'URL del servizio mobile con l'aggiunta del percorso _signin-twitter_. In questo esempio, il servizio mobile sarebbe l'URL di Callback ```https://todolist.azure-mobile.net/signin-twitter```.

   	![][2]

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni, quindi fare clic su **Create your Twitter application**.

   	L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

6. Fare clic sulla scheda **Keys and Access Tokens** nel dashboard relativo all'applicazione e prendere nota dei valori di **Chiave utente** e **Chiave privata utente**.

    > [AZURE.NOTE]Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

7. Fare clic sulla scheda **Settings**, scorrere verso il basso e assicurarsi che la casella di controllo **Allow this application to be used to sign in with Twitter** sia selezionata, quindi fare clic su **Update settings**.

È ora possibile utilizzare un account di accesso Twitter per l'autenticazione nell'app fornendo i valori di chiave e segreto consumer a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO8-->