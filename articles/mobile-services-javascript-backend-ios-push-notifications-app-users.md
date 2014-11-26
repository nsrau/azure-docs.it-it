<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Inviare notifiche push agli utenti autenticati

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone" >Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/it-it/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo iOS registrato. A differenza della precedente esercitazione sulle [notifiche push][notifiche push], in questa esercitazione i servizi mobili vengono modificati in modo da richiedere che un utente venga autenticato prima che il client iOS possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

In questa esercitazione viene descritto il processo seguente:

-   [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione][Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
-   [Aggiornamento dell'app per accedere prima della registrazione][Aggiornamento dell'app per accedere prima della registrazione]
-   [Test dell'app][Test dell'app]

Questa esercitazione supporta app per Windows Phone 8.0 e Windows Phone 8.1 ("Silverlight"). Per le app per Windows Phone 8.1 Store, vedere la [versione per Windows Store di questo argomento][versione per Windows Store di questo argomento].

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Aggiunge un requisito di accesso all'app di esempio TodoList.

-   [Introduzione alle notifiche push][notifiche push]
    Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

## <a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

1.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

        function insert(item, user, request) {

                function insert(item, user, request) {
                    request.execute();
                    setTimeout(function() {
                        push.apns.send(null, {
                            alert: "Alert: " + item.text,
                            payload: {
                                "Hey, a new item arrived: '" + item.text + "'"
                            }
                        });
                    }, 2500);
                }

        }

    Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app per Windows Phone (MPNS) create dall'utente connesso.

## <a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Test dell'app

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]




  [notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
  [Aggiornamento dell'app per accedere prima della registrazione]: #update-app
  [Test dell'app]: #test
  [versione per Windows Store di questo argomento]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-ios-get-started-users/
