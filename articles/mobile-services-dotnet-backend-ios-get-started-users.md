<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-ios-get-started-users/ "Back-end JavaScript")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili
------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  In Visual Studio aprire il file Web.config per il progetto di servizio mobile, quindi nella sezione appSettings impostare l'identificatore dell'app e i valori segreti condivisi ottenuti dal provider di identità.

    Queste impostazioni vengono utilizzate durante lo sviluppo locale. Dopo la pubblicazione del progetto di servizio mobile in Azure, le impostazioni verranno sostituite dai valori impostati nel portale.

Limitazione delle autorizzazioni agli utenti autenticati
--------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-ios-get-started).

2.  Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.??

## Aggiunta dell'autenticazione all'app 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## Passaggi successivi Nella prossima esercitazione, 
[Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. 

[Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register 
[Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions 
[Aggiunta dell'autenticazione all'app]: #add-authentication 
[Passaggi successivi]: #next-steps 
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[Autorizzazione di utenti con script]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 
[Portale di gestione di Azure]: https://manage.windowsazure.com/ 
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Registrazione del pacchetto dell'app Windows Store per l'autenticazione Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[Get started with Mobile Services]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Get started with authentication]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Get started with push notifications]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Authorize users with scripts]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
