
1. Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**. 
 
2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.
   
   	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
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
		}

   	Verrà registrato un nuovo script insert, che usa l'[oggetto gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) per inviare una notifica push a tutti i dispositivi registrati dopo la corretta esecuzione dell'inserimento.

<!---HONumber=Oct15_HO3-->