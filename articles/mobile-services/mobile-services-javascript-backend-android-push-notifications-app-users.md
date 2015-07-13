
<properties 
	pageTitle="Inviare notifiche push agli utenti autenticati" 
	description="Informazioni su come inviare notifiche push a utenti specifici" 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="wesmc"/>


# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Panoramica

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione sulle [notifiche push][Get started with push notifications], in questa esercitazione il servizio mobile viene modificato in modo da richiedere l'autenticazione di un utente prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

Questa esercitazione supporta le app Android.

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app di Servizi mobili]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Sostituire la funzione insert con il codice seguente, quindi fare clic su <strong>Salva</strong>:</p>
<pre><code>function insert(item, user, request) {

    // Definire un payload per una notifica di tipo avviso popup di Google Cloud Messaging.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Ottenere l'ID dell'utente che ha eseguito l'accesso.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // Se l'inserimento ha esito positivo, inviare una notifica a tutti i dispositivi 
            // registrati con l'utente che ha eseguito l'accesso come tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

<p>Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni Google Cloud Messaging create dall'utente connesso.</p></li></ol>

##Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](mobile-services-android-push-notifications-app-users.md)]

##Testare l'app

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)]

<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Aggiungere l'autenticazione all'app di Servizi mobili]: mobile-services-android-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-android-get-started-push.md
[Introduzione alle notifiche push]: mobile-services-javascript-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
 

<!---HONumber=July15_HO1-->