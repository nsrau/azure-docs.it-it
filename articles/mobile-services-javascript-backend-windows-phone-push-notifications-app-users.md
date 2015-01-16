<properties pageTitle="Inviare notifiche push agli utenti autenticati" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Informazioni su come inviare notifiche push a specifici " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Inviare notifiche push agli utenti autenticati

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Questo argomento illustra come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione sulle [notifiche push][Introduzione alle notifiche push], in questa esercitazione i servizi mobili vengono modificati in modo da richiedere che un utente venga autenticato prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

In questa esercitazione viene descritto il processo seguente:

+ [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
+ [Aggiornamento dell'app per accedere prima della registrazione]
+ [Testare l'app]
 
Questa esercitazione supporta app di Windows Phone 8.0 e Windows Phone 8.1 ("Silverlight"). Per le app di Windows Phone 8.1 Store, vedere la [versione per Windows Store di questo argomento](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users).

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica. 

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Sostituire la funzione insert con il codice seguente, quindi fare clic su <strong>Salva</strong>:</p>
<pre><code>function insert(item, user, request) {
	// Define a payload for the Windows Phone toast notification.
	var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		'<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		'<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		'</wp:Text2></wp:Toast></wp:Notification>';

	// Get the ID of the logged-in user.
	var userId = user.userId;		

	request.execute({
		success: function() {
			// If the insert succeeds, send a notification.
			push.mpns.send(userId, payload, 'toast', 22, {
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

<p>Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app di Windows Phone (MPNS) create dall'utente connesso.</p></li></ol>

##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[WACOM.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>Testare l'app

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]-->

<!-- Anchors. -->
[Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
[Aggiornamento dell'app per accedere prima della registrazione]: #update-app
[Testare l'app]: #test
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
