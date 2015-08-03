<properties 
	pageTitle="Inviare notifiche push agli utenti autenticati" 
	description="Informazioni su come inviare notifiche push a utenti specifici" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione [Aggiungere notifiche push all’app], in questa esercitazione il servizio mobile viene modificato in modo da richiedere l'autenticazione di un utente prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

Questa esercitazione descrive il processo seguente:

1. [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
2. [Aggiornamento dell'app per accedere prima della registrazione]
3. [Test dell'app]
 
Questa esercitazione supporta app sia di Windows Store che di Windows Phone Store.

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Aggiungere notifiche push all'app]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Sostituire la funzione insert con il codice seguente, quindi fare clic su <strong>Salva</strong>:</p>
<pre><code>function insert(item, user, request) {
    // Definire un payload per la notifica di tipo avviso popup di Windows Store.
    var payload = '&lt;?xml version="1.0" encoding="utf-8"?>&lt;toast>&lt;visual>' +    
    '&lt;binding template="ToastText01">&lt;text id="1">' +
    item.text + '&lt;/text>&lt;/binding>&lt;/visual>&lt;/toast>';

    // Ottenere l'ID dell'utente che ha eseguito l'accesso.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // Se l'inserimento ha esito positivo, inviare una notifica a tutti i dispositivi 
	    	// registrati con l'utente che ha eseguito l'accesso come tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
}</code></pre>

<p>Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app di Windows Store create dall'utente connesso.</p></li></ol>

##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
[Aggiornamento dell'app per accedere prima della registrazione]: #update-app
[Test dell'app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Aggiungere notifiche push all'app]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Aggiungere notifiche push all’app]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->