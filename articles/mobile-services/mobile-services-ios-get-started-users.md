<properties
	pageTitle="Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente (iOS) | Mobile Dev Center"
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per iOS tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questa esercitazione consente di aggiungere l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato.

Questa esercitazione è basata sull'esercitazione [Guida introduttiva per Servizi mobili], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

In Xcode aprire il progetto. Premere il pulsante **Run** per avviare l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Archiviare i token di autenticazione nell'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Autorizzazione sul lato servizio degli utenti di Servizi mobili], si userà il valore dell'ID utente per filtrare i dati restituiti.

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
[Autorizzazione sul lato servizio degli utenti di Servizi mobili]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Guida introduttiva per Servizi mobili]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 