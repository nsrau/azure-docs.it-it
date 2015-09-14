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
	ms.date="08/27/2015"
	ms.author="glenga"/>

# Registrare le app per l'autenticazione Google con Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Questo argomento illustra come registrare le proprie app in modo da usare Google per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione [Introduzione all'autenticazione](mobile-services-ios-get-started-users.md) che illustra come consentire agli utenti l'accesso alla propria app. <br/>Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili](mobile-services-ios-get-started.md).

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Passare al sito Web delle [API di Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

4. Nella barra di spostamento a sinistra, fare clic su **API & Auth**, quindi in **API Social** fare clic su **API Google+** > **Enable API**.

5. Fare clic su **API & Auth** > **Credenziali** > **OAuth consent screen**, quindi selezionare il proprio **Indirizzo di posta elettronica**, immettere un **Nome prodotto** e quindi fare clic su **Salva**.

6. Nella scheda **Credenziali** fare clic su **Aggiungi credenziali** > **ID client OAuth 2.0**, quindi selezionare **Applicazione Web**.

7. Digitare l'URL del servizio mobile in **Authorized JavaScript Origins**, sostituire l'URL generato in **Authorized Redirect URI** con l'URL del servizio mobile con l'aggiunta del percorso `/login/google`, quindi fare clic su **Create client ID**.

	>[AZURE.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-google_, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-google`. &nbsp;
	
8. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.

    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

È ora possibile configurare il servizio mobile in modo da usare l'accesso Google per l'autenticazione nell'app.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=September15_HO1-->