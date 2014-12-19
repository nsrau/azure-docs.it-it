
<properties pageTitle="Inviare notifiche push agli utenti autenticati" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="dwrede" editor="" />


<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Inviare notifiche push agli utenti autenticati

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Questo argomento illustra come inviare notifiche push a un utente autenticato su un dispositivo iOS registrato. A differenza della precedente esercitazione sulle [notifiche push][Introduzione alle notifiche push], in questa esercitazione i servizi mobili vengono modificati in modo da richiedere che un utente venga autenticato prima che il client iOS possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

In questa esercitazione viene descritto il processo seguente:

+ [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
+ [Aggiornamento dell'app per accedere prima della registrazione]
+ [Testare l'app]

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Sostituire la funzione insert con il codice seguente, quindi fare clic su <strong>Salva</strong>:</p>
<pre><code>function insert(item, user, request) {

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

}</code></pre>

<p>Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app di Windows Phone (MPNS) create dall'utente connesso.</p></li></ol>


##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Testare l'app

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
[Aggiornamento dell'app per accedere prima della registrazione]: #update-app
[Testare l'app]: #test
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-ios-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library

[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
