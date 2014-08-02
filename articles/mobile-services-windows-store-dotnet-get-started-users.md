<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/ "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/ "Back-end JavaScript")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.

[guarda l'esercitazione](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Riproduci video](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) 10:04

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

> [WACOM.NOTE]In questa esercitazione viene illustrato il metodo di base disponibile in Servizi mobili per autenticare gli utenti tramite diversi provider di identità. Questo metodo è semplice da configurare e supporta più provider, tuttavia richiede l'accesso da parte degli utenti a ogni avvio dell'app. Per utilizzare invece Live Connect per offrire un ambiente Single Sign-On nella propria app di Windows Store, vedere l'argomento [Single Sign-On per le app di Windows Store tramite Live Connect](/en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on).

Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili
------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Facoltativo) Completare la procedura descritta in [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft](/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/).

    **Nota**

    Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identità per account Microsoft.

Limitazione delle autorizzazioni agli utenti autenticati
--------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-windows-store-get-started).

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazione all'app
------------------------------------

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili](/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
[Authorize users with scripts]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript and HTML]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package