

1. Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **Registrations**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. In **Registrations** fare clic sulla scheda **Script** e selezionare **Insert**.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **Registrations**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   Verrà registrato un nuovo script insert, che archivia le informazioni di registrazione nella nuova tabella.

<!---HONumber=July15_HO2-->