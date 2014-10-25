<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Inviare notifiche push agli utenti autenticati

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript" >Windows Store JavaScript</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">Back-end JavaScript</a></div>

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo iOS registrato. A differenza della precedente esercitazione sulle [notifiche push][notifiche push], in questa esercitazione il servizio mobile viene modificato in modo da richiedere l'autenticazione di un utente prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine il codice del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

In questa esercitazione viene descritto il processo seguente:

+ [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione][Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
+ [Aggiornamento dell'app per accedere prima della registrazione][Aggiornamento dell'app per accedere prima della registrazione]
+ [Test dell'app][Test dell'app]

Questa esercitazione supporta app sia di Windows Store che di Windows Phone Store.

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione all'autenticazione][Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push][notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

## <a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][mobile-services-dotnet-backend-push-notifications-app-users]]

## <a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login][mobile-services-ios-push-notifications-app-users-login]]

## <a name="test"></a>Test dell'app

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app][mobile-services-ios-push-notifications-app-users-test-app]]

<!-- Anchors. -->
<!-- URLs. -->

[Windows Store C\#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
[Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
[Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
[iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
[Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
[Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ ".NET backend"
[Back-end JavaScript]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "JavaScript backend"
[notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
[Aggiornamento dell'app per accedere prima della registrazione]: #update-app
[Test dell'app]: #test
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
[mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
[mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
