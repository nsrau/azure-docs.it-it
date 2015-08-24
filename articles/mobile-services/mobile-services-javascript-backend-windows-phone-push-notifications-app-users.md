<properties 
	pageTitle="Inviare notifiche push agli utenti dell'app Windows Phone Silverlight autenticati | Microsoft Azure" 
	description="Informazioni su come inviare notifiche push da Servizi mobili di Azure a utenti specifici dell'app Windows Phone Silverlight." 
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
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Panoramica

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione [Aggiungere notifiche push all'app], in questa esercitazione il servizio mobile viene modificato in modo da richiedere l'autenticazione di un utente prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine lo script del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.
 
>[AZURE.NOTE]Questa esercitazione supporta app per Windows Phone 8.0 e Windows Phone 8.1 Silverlight. Per le app per Windows Phone 8.1 Store, vedere la [versione per Windows Store di questo argomento](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md).

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Aggiungere notifiche push all'app]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

    function insert(item, user, request) {
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
	}

&nbsp;&nbsp;Questo script insert usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app per Windows Phone (MPNS) create dall'utente connesso.

##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: mobile-services-windows-phone-get-started-users.md
[Aggiungere notifiche push all'app]: mobile-services-javascript-backend-windows-phone-get-started-push.md
[portale di gestione di Azure]: https://manage.windowsazure.com/

 

<!---HONumber=August15_HO7-->