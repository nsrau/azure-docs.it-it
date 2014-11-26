<properties pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Introduzione all'autenticazione in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" >iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android" class="current">Android</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-android-get-started-users/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2. [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]
4. [Archiviazione dei token di autenticazione sul client][Archiviazione dei token di autenticazione sul client]
5. [Aggiornamento dei token scaduti][Aggiornamento dei token scaduti]


Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="register"></a>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

>WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]

>WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]

## <a name="permissions"></a>Limitazione delle autorizzazioni agli utenti autenticati

>WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]

1. In Eclipse aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

2. Nel menu **Run** fare clic su **Run** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

	 L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiunta dell'autenticazione all'app

>WACOM.INCLUDE [mobile-services-android-authenticate-app][mobile-services-android-authenticate-app]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

>WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][mobile-services-android-authenticate-app-with-token]

## <a name="refresh-tokens"></a>Aggiornare la cache dei token

>WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][mobile-services-android-authenticate-app-refresh-token]

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione sul lato servizio degli utenti di Servizi mobili], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili.




[Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#"
[Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript"
[Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
[iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
[Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
[Xamarin.iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
[Xamarin.Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
[Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/ ".NET backend"
[Back-end JavaScript]: /it-it/documentation/articles/mobile-services-android-get-started-users/ "JavaScript backend"
[Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
[Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiunta dell'autenticazione all'app]: #add-authentication
[Archiviazione dei token di autenticazione sul client]: #cache-tokens
[Aggiornamento dei token scaduti]: #refresh-tokens
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
[mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
[mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
[mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
[mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
[mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
[Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts
