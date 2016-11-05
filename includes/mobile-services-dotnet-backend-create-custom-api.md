

1. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controller, espandere **Aggiungi**, quindi fare clic su **New Scaffolded Item**. Verrà visualizzata la finestra di dialogo Add Scaffold.
2. Espandere **Servizi mobili di Azure**e, fare clic su **Controller personalizzato per Servizi mobili di Azure**, selezionare **Aggiungi**, specificare il **Nome controller** di `CompleteAllController` e fare nuovamente clic su **Aggiungi**.
   
    ![Finestra di dialogo Add Scaffold dell'API Web](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)
   
    Verrà creata una nuova classe controller vuota denominata **CompleteAllController**.
   
   > [!NOTE]
   > Se la finestra di dialogo non include scaffolding specifici di Servizi mobili, creare invece un nuovo oggetto **Web API Controller - Empty**. Nella nuova classe controller aggiungere una proprietà **Services** pubblica, che restituisce il tipo **ApiServices**. Questa proprietà viene utilizzata per accedere a impostazioni specifiche del server dall'interno del controller.
   > 
   > 
3. In **CompleteAllController.cs** aggiungere le istruzioni **using** seguenti. Sostituire `todolistService` con lo spazio dei nomi del progetto di servizio mobile, che deve essere costituito dal nome del servizio mobile a cui viene aggiunto `Service`.
   
        using System.Threading.Tasks;
        using todolistService.Models;
4. In **CompleteAllController.cs** aggiungere la classe seguente per eseguire il wrapping della risposta inviata al client.
   
        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }
5. Aggiungere il seguente codice al nuovo controller. Sostituire `todolistContext` con il nome dell'oggetto DbContext del modello di dati, che deve essere costituito dal nome del servizio mobile a cui viene aggiunto `Context`. Analogamente, sostituire il nome dello schema nell'istruzione UPDATE con il nome del servizio mobile. In questo codice viene utilizzata la [classe di database](http://msdn.microsoft.com/library/system.data.entity.database.aspx) per accedere direttamente alla tabella **TodoItems** e impostare il contrassegno di completamento su tutti gli elementi. Questo metodo supporta una richiesta POST e al client viene restituito un valore intero corrispondente al numero di righe modificate.

        // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

    > [AZURE.TIP]Con le autorizzazioni predefinite, chiunque disponga della chiave dell'app può chiamare l'API personalizzata. La chiave dell'applicazione non è tuttavia considerata una credenziale di sicurezza, in quanto non può essere distribuita o archiviata in modo sicuro. Per maggiore sicurezza, è consigliabile limitare l'accesso ai soli utenti autenticati.

<!---HONumber=Oct15_HO3-->