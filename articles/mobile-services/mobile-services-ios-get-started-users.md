<properties
	pageTitle="Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente (iOS) | Backend JavaScript | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per iOS tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Aggiungere l'autenticazione a un'app esistente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questa esercitazione consente di aggiungere l'autenticazione all’[esercitazione Guida introduttiva per Servizi mobili] tramite un provider di identità supportato.

È consigliabile completare prima di tutto l'[Esercitazione Guida introduttiva per Servizi mobili]. In alternativa scaricare semplicemente il progetto iOS di avvio rapido dal [portale di Azure classico], fare clic su **Servizi mobili** > servizio mobile personalizzato > l'accesso cloud in alto a sinistra > **iOS** > **Crea una nuova app iOS** > **Scaricare ed eseguire l'applicazione** > **Objective-C** > **Scarica**. Fare clic su **Crea tabella TodoItem** prima di fare clic su **Scarica**, se la tabella non è ancora stata creata.

##<a name="register"></a>Registrare l'app per l'autenticazione

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni dati agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Archiviare i token di autenticazione nell'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi

Quindi, leggere le informazioni su [come utilizzare il valore dell'ID utente per filtrare i dati restituiti](mobile-services-javascript-backend-service-side-authorization.md).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Service-side authorization of Mobile Services users]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[esercitazione Guida introduttiva per Servizi mobili]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[portale di Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_1203_2015-->