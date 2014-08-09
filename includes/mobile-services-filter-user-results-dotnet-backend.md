
Poiché l'autenticazione è necessaria per accedere ai dati della tabella TodoItem, è possibile utilizzare il valore userID assegnato da Servizi mobili per filtrare i dati restituiti.

> [WACOM.NOTE]Per i metodi indicati di seguito, è necessario applicare **RequiresAuthorizationAttribute** a **User** **Authorizationlevel**. In questo modo, l'accesso alla tabella è consentito solo agli utenti autenticati.

1.  In Visual Studio 2013 aprire il progetto di servizio mobile, espandere la cartella DataObjects, quindi aprire il file di progetto TodoItem.cs.

    La classe TodoItem definisce l'oggetto dati ed è necessario aggiungere una proprietà UserId da utilizzare per il filtro.

2.  Aggiungere la nuova proprietà UserId seguente alla classe **TodoItem**:

         public string UserId { get; set; }

    > [WACOM.NOTE] Quando si utilizza l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. L'inizializzatore predefinito non può essere utilizzato su un database SQL in Azure. Per ulteriori informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati](/it-it/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

3.  In Esplora soluzioni espandere la cartella Controllers, aprire il file di progetto TodoItemController.cs e aggiungere l'istruzione **using** seguente:

         using Microsoft.WindowsAzure.Mobile.Service.Security;

    La classe **TodoItemController** consente di implementare l'accesso ai dati per la tabella TodoItem.

4.  Individuare il metodo **PostTodoItem** e aggiungere il codice seguente all'estrema destra, prima dell'istruzione **return**:

         // Get the logged-in user.
         var currentUser = User as ServiceUser;

         // Set the user ID on the item.
         item.UserId = currentUser.Id;

    Questo codice aggiunge un valore UserId, ovvero l'ID utente dell'utente autenticato, all'elemento prima che venga inserito nella tabella TodoItem.

5.  Individuare il metodo **GetAllTodoItems** e sostituire l'istruzione **return** esistente con la riga di codice seguente:

         // Get the logged-in user.
         var currentUser = User as ServiceUser;

         return Query().Where(todo => todo.UserId == currentUser.Id);

        Questa query filtra gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente. Facoltativamente è possibile: 

6.  Ripubblicare il progetto di servizio mobile in Azure.


