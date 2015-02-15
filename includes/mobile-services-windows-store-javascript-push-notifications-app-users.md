
È quindi necessario modificare la modalità di registrazione delle notifiche push, per assicurarsi che l'utente sia autenticato prima del tentativo di registrazione. Gli aggiornamenti dell'app client dipendono dalla modalità di implementazione delle notifiche push.

###Uso della procedura guidata Aggiungi notifica push in Visual Studio 2013 Update 2 o versione successiva

Con questo metodo la procedura guidata genera nuovi file push.register.js e service.js nel progetto.

>[AZURE.NOTE]La procedura guidata Aggiungi notifica push è attualmente supportata solo per un servizio mobile back-end .NET.

1. In Esplora soluzioni di Visual Studio aprire il file di progetto push.register.js e impostare come commento o eliminare la chiamata a **addEventListener**. 

2. Nel file di progetto default.js sostituire la funzione **login** esistente con il codice seguente:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Notifiche push abilitate manualmente		

Con questo metodo il codice di registrazione è stato aggiunto direttamente dall'esercitazione nel file di progetto default.js.

1. In Esplora soluzioni di Visual Studio aprire il file di progetto default.js e nel gestore eventi **onActivated** individuare la riga di codice che chiama la funzione **createPushNotificationChannelForApplicationAsync**, simile a quanto indicato di seguito:

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Spostare questa riga di codice nella funzione **login**, subito prima della chiamata a **refreshTodoItems**, in modo che la funzione **login** sia simile alla seguente:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

<!--HONumber=42-->
