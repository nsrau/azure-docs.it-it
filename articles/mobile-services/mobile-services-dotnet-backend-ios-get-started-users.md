<properties
	pageTitle="Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente (iOS) | .NET Backend | Microsoft Azure"
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
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Aggiungere l'autenticazione all'app di Servizi mobili di Azure esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questa esercitazione consente di aggiungere l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione [Guida introduttiva per Servizi mobili], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

In Xcode aprire il progetto. Premere il pulsante **Run** per avviare l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Archiviare i token di autenticazione nell'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Autorizzazione sul lato servizio degli utenti di Servizi mobili], si userà il valore dell'ID utente per filtrare i dati restituiti.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Autorizzazione sul lato servizio degli utenti di Servizi mobili]: mobile-services-dotnet-backend-service-side-authorization.md
[Guida introduttiva per Servizi mobili]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=Sept15_HO3-->