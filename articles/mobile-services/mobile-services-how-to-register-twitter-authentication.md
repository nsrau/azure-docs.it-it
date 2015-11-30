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
	ms.date="11/15/2015" 
	ms.author="glenga"/>

#Registrare le app per l'autenticazione Twitter con Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

In questo argomento viene illustrato come registrare le proprie app in modo da poter utilizzare Twitter per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione [Aggiungere l'autenticazione all’app](mobile-services-ios-get-started-users.md) che illustra come consentire agli utenti l'accesso all’app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione relativa all'[introduzione a Servizi mobili](mobile-services-ios-get-started).

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Passare al sito Web [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300), accedere con le credenziali dell'account Twitter e quindi fare clic su **Create New App**.

2. Digitare i valori di **Nome**, **Descrizione**, e **Sito Web** per l'app, quindi digitare uno dei seguenti formati URL in **URL Callback**.
 
	+ **Back-end .NET**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **Back-end JavaScript**: `https://<mobile_service>.azure-mobile.net/login/twitter` 

	 >[AZURE.NOTE]Assicurarsi di utilizzare il formato del percorso URL di reindirizzamento corretto per il tipo di back-end di servizi mobili. Quando questo non è corretto, l'autenticazione non avrà esito positivo. & nbsp;

   	![][2]

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni, quindi fare clic su **Creare l’applicazione Twitter**.

   	L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

6. Fare clic sulla scheda **Chiavi e token di accesso** nel dashboard relativo all'applicazione e prendere nota dei valori di **Chiave utente** e **Chiave privata utente**.

    > [AZURE.NOTE]Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

7. Fare clic sulla scheda **Impostazioni**, scorrere verso il basso e assicurarsi che la casella di controllo **Utilizzare l'applicazione per accedere a Twitter** sia selezionata, quindi fare clic su **Aggiorna impostazioni**.

È ora possibile utilizzare un account di accesso Twitter per l'autenticazione nell'app fornendo i valori di chiave e segreto consumer a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Nov15_HO4-->