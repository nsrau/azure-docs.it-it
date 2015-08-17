1. Nel file insert.js relativo alla tabella **channels** individuare le righe di codice seguenti, impostarle come commenti o rimuoverle dal file, quindi salvare le modifiche.

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	Quando si salvano le modifiche apportate al file insert.js, nel servizio mobile viene caricata una nuova versione dello script.

2. In Esplora server espandere la tabella TodoItem, aprire il file insert.js e sostituire la funzione insert corrente con il codice seguente, quindi salvare le modifiche:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	A questo punto, quando si inserisce un nuovo elemento TodoItem, verrà inviata una notifica push a tutti i dispositivi registrati.

<!---HONumber=August15_HO6-->