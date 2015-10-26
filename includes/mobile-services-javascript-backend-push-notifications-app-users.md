
1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

2. Fare clic sulla scheda **Push**, selezionare **Solo gli utenti autenticati** per **Autorizzazioni**, quindi fare clic su **Salva** e poi su **Modifica script**.
	
	In questo modo è possibile personalizzare la funzione di callback per la registrazione delle notifiche push. Se si usa Git per modificare il codice sorgente, questa stessa funzione di registrazione è disponibile in `.\service\extensions\push.js`.

3. Sostituire la funzione **register** esistente con il codice seguente e fare clic su **Salva**:

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	In questo modo viene aggiunto alla registrazione un tag corrispondente all'ID dell'utente connesso. I tag specificati vengono convalidati per evitare che un utente si registri con l'ID di un altro utente. Le notifiche inviate saranno ricevute dall'utente su questo e su qualsiasi altro dispositivo registrato dall'utente stesso.

4. Fare clic sulla freccia indietro, fare clic sulla scheda **Dati**, su **TodoItem**, su **Script** e infine selezionare **Inserisci**.

<!---HONumber=Oct15_HO3-->