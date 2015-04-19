

Poiché l'autenticazione è necessaria per accedere ai dati della tabella TodoItem, è possibile usare il valore userID assegnato da Servizi mobili per filtrare i dati restituiti.

>[AZURE.NOTE]Per i metodi indicati di seguito è necessario applicare l'attributo **AuthorizeLevel** ad **AuthorizeLevel** di **User**. In questo modo, l'accesso alla tabella è consentito solo agli utenti autenticati.

1. In Visual Studio 2013 aprire il progetto di servizio mobile, espandere la cartella DataObjects, quindi aprire il file di progetto TodoItem.cs.

	La classe TodoItem definisce l'oggetto dati ed è necessario aggiungere una proprietà UserId da usare per il filtro.

2. Aggiungere la nuova proprietà UserId seguente alla classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Quando si usa l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario usare Migrazioni Code First. L'inizializzatore predefinito non può essere usato su un database SQL in Azure. Per altre informazioni, vedere [Come usare le Migrazioni Code First per aggiornare il modello di dati](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

3. In Esplora soluzioni espandere la cartella Controller, aprire il file di progetto TodoItemController.cs e aggiungere l'istruzione **using** seguente:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	La classe **TodoItemController**consente di implementare l'accesso ai dati per la tabella TodoItem. 
 
4. Individuare il metodo **PostTodoItem** e aggiungere il codice seguente all'inizio del metodo:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

   Questo codice aggiunge un valore UserId, ovvero l'ID utente dell'utente autenticato, all'elemento prima che venga inserito nella tabella TodoItem. 
	

5. Individuare il metodo **GetAllTodoItems** e sostituire l'istruzione **return** esistente con la riga di codice seguente:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   Questa query filtra gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente. 

6. Ripubblicare il progetto di servizio mobile in Azure.
<!--HONumber=42-->
