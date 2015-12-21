<properties
	pageTitle="Effettuare la registrazione per l'autenticazione Google | Microsoft Azure"
	description="Informazioni su come registrare le proprie app in modo da poter usare Google per l'autenticazione con Servizi mobili di Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="glenga"/>

# Registrare le app per l'autenticazione Google con Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Questo argomento illustra come registrare le proprie app in modo da usare Google per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione [Introduzione all'autenticazione](mobile-services-ios-get-started-users.md) che illustra come consentire agli utenti l'accesso alla propria app. <br/>Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili](mobile-services-ios-get-started.md).

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Passare al sito Web delle [API di Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

4. Nel menu a discesa **Products & services** fare clic su **API Manage**, quindi in **Social APIs** fare clic su **Google+ API** > **Enable API**.

5. Fare clic su **Credentials** > **OAuth consent screen**, quindi selezionare il proprio **indirizzo di posta elettronica**, immettere un **nome prodotto** e quindi fare clic su **Save**

6. Nella scheda **Credenziali** fare clic su **Aggiungi credenziali** > **ID client OAuth 2.0**, quindi selezionare **Applicazione Web**.


7. Digitare l'URL del servizio mobile in **Authorized JavaScript Origins**, sostituire l'URL generato in **Authorized Redirect URI** con uno dei formati di URL seguenti e quindi fare clic su **Create**:
 

	+ **Back-end .NET**: `https://<mobile_service>.azure-mobile.net/signin-google`
	+ **Back-end JavaScript**: `https://<mobile_service>.azure-mobile.net/login/google`

	 >[AZURE.NOTE]Assicurarsi di utilizzare il formato del percorso URL di reindirizzamento corretto per il tipo di back-end di servizi mobili. Quando questo non è corretto, l'autenticazione avrà esito negativo.

8. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.

    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

È ora possibile configurare il servizio mobile in modo da usare l'accesso Google per l'autenticazione nell'app.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1210_2015-->