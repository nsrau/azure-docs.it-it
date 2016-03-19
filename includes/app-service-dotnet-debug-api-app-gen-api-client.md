## Generare un client app per le API 

Gli strumenti App per le API in Visual Studio semplificano la generazione di codice C# che chiama le app per le API di Azure dal desktop, dall'archivio e dalle app per dispositivi mobili.

1. In Visual Studio aprire la soluzione che contiene l'app per le API usata nell'argomento [Creare un'app per le API](../article/app-service-api/app-service-dotnet-create-api-app.md). 

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi** > **Nuovo progetto**.

	![Aggiungere un nuovo progetto](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. Nella finestra di dialogo **Aggiungi nuovo progetto** eseguire la procedura seguente:

	1. Selezionare la categoria **Desktop Windows**.
	
	2. Selezionare il modello di progetto **Applicazione console**.
	
	3. Assegnare un nome al progetto.
	
	4. Fare clic su **OK** per generare il nuovo progetto nella soluzione esistente.
	
	![Aggiungere un nuovo progetto](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Fare clic con il pulsante destro del mouse sul progetto Applicazione console appena creato e scegliere **Aggiungi** > **Client app per le API di Azure**.

	![Aggiungere un nuovo client](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. Nella finestra di dialogo **Aggiungi client app per le API di Microsoft Azure** eseguire la procedura seguente:

	1. Selezionare l'opzione **Download**. 
	
	2. Dall'elenco a discesa selezionare l'app per le API creata in precedenza.
	
	3. Fare clic su **OK**.

	![Schermata di generazione](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	La procedura guidata scaricherà il file di metadati dell'API e genererà un'interfaccia tipizzata per richiamare l'app per le API.

	![Generazione in corso](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	Al termine della generazione di codice, in Esplora soluzioni verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa i modelli di client e dati.

	![Generazione completata](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Aprire il file **Program.cs** dalla radice del progetto e sostituire il metodo **Main** con il codice seguente:

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Testare il client app per le API

Dopo la scrittura di codice per l'app per le API, è possibile testare il codice.

1. Aprire **Esplora soluzioni**.

2. Fare clic con il pulsante destro del mouse sull'applicazione console creata nella sezione precedente.

3. Dal menu contestuale dell'applicazione console selezionare **Debug > Avvia nuova istanza**.

4. Verrà visualizzata una finestra della console, in cui vengono visualizzati tutti i contatti.

	![App console in esecuzione](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Premere **INVIO** per chiudere la finestra della console.

<!---HONumber=Oct15_HO3-->