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

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato.

Questa esercitazione è basata sull'[esercitazione Guida introduttiva per Servizi mobili], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

In Xcode aprire il progetto. Premere il pulsante **Run** per avviare l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Archiviare i token di autenticazione nell'app

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Autorizzazione sul lato servizio degli utenti di Servizi mobili], si userà il valore dell'ID utente per filtrare i dati restituiti.

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps
[Archiviazione dei token di autenticazione nell'app]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Autorizzazione sul lato servizio degli utenti di Servizi mobili]: mobile-services-javascript-backend-service-side-authorization.md
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Esercitazione Guida introduttiva per Servizi mobili]: /develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-ios
[Autorizzare gli utenti con gli script]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->