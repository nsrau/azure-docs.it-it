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
	ms.date="06/11/2015" 
	ms.author="glenga"/>

# Registrare le app per l'autenticazione Google con Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Questo argomento illustra come registrare le proprie app in modo da usare Google per l'autenticazione con Servizi mobili di Azure.

>[AZURE.NOTE]Questa esercitazione riguarda [Servizi mobili di Azure](http://azure.microsoft.com/services/mobile-services/), una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione [Introduzione all'autenticazione](mobile-services-ios-get-started-users.md) che illustra come consentire agli utenti l'accesso alla propria app. <br/>Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili](mobile-services-ios-get-started.md).

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Passare al sito Web delle <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API di Google</a>, accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

   	![Nuovo progetto Google API](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png)

2. Fare clic su **Consent screen**, selezionare **Email Address**, immettere il nome di un prodotto in **Product Name** e quindi fare clic su **Save**.

3. Fare clic su **API & Auth** > **Credentials** > **Create new Client ID**.

   	![Creare un nuovo ID client](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png)

4. Selezionare **Web application** e digitare l'URL del servizio mobile in **Authorized JavaScript Origins**, sostituire l'URL generato in **Authorized Redirect URI** con l'URL del servizio mobile con l'aggiunta del percorso `/login/google`, quindi fare clic su **Create client ID**.

	>[AZURE.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-google_, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-google`. &nbsp;

   	![](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png)

5. In **Client ID for web applications** prendere nota dei valori di **Client ID** e **Client secret**.

   	![Credenziali del client](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png)

    >[AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

È ora possibile configurare il servizio mobile in modo da usare l'accesso Google per l'autenticazione nell'app.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO8-->