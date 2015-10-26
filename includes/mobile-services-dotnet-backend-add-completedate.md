In questa sezione il modello del database verrà modificato con l'aggiunta di un nuovo campo timestamp denominato **CompleteDate**. In questo campo verrà registrata l'ultima volta in cui l'elemento Todo è stato completato. Entity Framework aggiornerà il database in base alla modifica del modello utilizzando una classe predefinita dell'inizializzatore del database derivata da [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621).

1. In Esplora soluzioni di Visual Studio espandere la cartella **App\_Start** nel progetto di servizio todolist. Aprire il file WebApiConfig.cs.

2. Nel file WebApiConfig.cs notare che la classe predefinita dell'inizializzatore del database deriva dalla classe `DropCreateDatabaseIfModelChanges`. Ciò significa che se viene apportata qualsiasi modifica al modello, la tabella verrà eliminata e ricreata per gestire il nuovo modello. Pertanto, i dati della tabella andranno persi e verrà eseguito il reseeding della tabella. Modificare il metodo Seed dell'inizializzatore del database in modo che i dati di seeding siano i seguenti e salvare il file WebApiConfig.cs.

    >[AZURE.NOTE]Quando si usa l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. Per altre informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. In Esplora soluzioni di Visual Studio espandere la cartella **DataObjects** nel progetto di servizio todolist. Aprire il file TodoItem.cs e aggiornare la classe TodoItem per includere il campo CompleteDate, come indicato di seguito. Quindi, salvare il file TodoItem.cs.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto di servizio todolist. Aprire il file TodoItemController.cs e aggiornare il metodo `PatchTodoItem` in modo che venga impostato il campo **CompleteDate** quando la proprietà **Complete** cambia da false a true. Quindi, salvare il file TodoItemController.cs.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Ricompilare il progetto di servizio back-end .NET todolist e verificare che non siano presenti errori di compilazione.

A questo punto, l'app client verrà aggiornata per visualizzare i nuovi dati **CompleteDate**.

<!---HONumber=Oct15_HO3-->