<properties
	pageTitle="Inviare notifiche push agli utenti autenticati"
	description="Informazioni su come inviare notifiche push a utenti specifici"
	services="mobile-services,notification-hubs"
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
	ms.date="04/02/2015"
	ms.author="krisragh"/>

# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Questo argomento illustra come inviare notifiche push a un utente autenticato in iOS. Prima di iniziare questa esercitazione, completare le esercitazioni [Introduzione all'autenticazione] e [Introduzione alle notifiche push].

In questa esercitazione, è possibile richiedere agli utenti di eseguire innanzitutto l'autenticazione, registrarsi all'hub di notifica per le notifiche push e aggiornare gli script del server per inviare le notifiche solo agli utenti autenticati.


##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Sostituire la funzione `insert` con il codice seguente, quindi fare clic su **Salva**. Lo script insert usa il tag dell'ID utente per inviare una notifica push a tutte le registrazioni di app per iOS da parte dell'utente connesso:

```
// Get the ID of the logged-in user.
var userId = user.userId; 

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introduzione all'autenticazione]: mobile-services-ios-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-javascript-backend-ios-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!--HONumber=54--> 