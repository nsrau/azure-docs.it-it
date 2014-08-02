1.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controllers relativa al progetto di servizio mobile, espandere **Add**, quindi fare clic su **New Scaffolded Item**.

    Verrà visualizzata la finestra di dialogo Add Scaffold.

2.  Espandere **Azure Mobile Services** e fare clic su **Azure Mobile Services Custom Controller** e quindi su **Add**, specificare `CompleteAllController` per **Controller name** e infine fare di nuovo clic su **Add**.

    ![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

    Verrà creata una nuova classe controller vuota denominata **CompleteAllController**.

> [WACOM.NOTE]Se la finestra di dialogo non include scaffolding specifici di Servizi mobili, creare invece un nuovo oggetto **Web API Controller - Empty**. Nella nuova classe controller aggiungere una proprietà **Services** pubblica, che restituisce il tipo **ApiServices**. Questa proprietà viene utilizzata per accedere a impostazioni specifiche del server dall'interno del controller.

1.  Nel nuovo file di progetto CompleteAllController.cs aggiungere le istruzioni **using** seguenti:

         using System.Threading.Tasks;
         using todolistService.Models;

    Nel codice precedente sostituire `todolistService` con lo spazio dei nomi del progetto di servizio mobile, che non deve essere il nome del servizio mobile aggiunto con `Service`.

2.  Aggiungere il codice seguente al nuovo controller:

         // POST api/completeall        
         public Task<int> Post()
         {
             using (todolistContext context = new todolistContext())
             {
                 // Get the database from the context.
                 var database = context.Database;

                 // Create a SQL statement that sets all uncompleted items
                 // to complete and execute the statement asynchronously.
                 var sql = @"UPDATE TodoItems SET Complete = 1 " +
                             @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                 var result = database.ExecuteSqlCommandAsync(sql);

                 // Log the result.
                 Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                     result.ToString()));
                    
                 return result;
             }
         }

    Nel codice precedente sostituire `todolistContext` con il nome dell'oggetto DbContext del modello di dati, che dovrà corrispondere al nome del servizio mobile aggiunto con `Context`. In questo codice viene utilizzata la [classe di database](http://msdn.microsoft.com/en-us/library/system.data.entity.database(v=vs.113).aspx) per accedere direttamente alla tabella **TodoItems** e impostare il contrassegno di completamento su tutti gli elementi. Questo metodo supporta una richiesta POST e al client viene restituito un valore intero corrispondente al numero di righe modificate.

    > [WACOM.NOTE] Poiché vengono impostate autorizzazioni predefinite, qualsiasi utente dell'app può chiamare l'API personalizzata. Tuttavia, la chiave dell'applicazione non viene distribuita né archiviata in modo sicuro e non può essere considerata una credenziale di sicurezza. Per questo motivo, è consigliabile limitare l'accesso solo agli utenti autenticati per le operazioni che modificano dati o hanno effetto sul servizio mobile.

In seguito, l'app di guida introduttiva verrà modificata per aggiungere un pulsante New e il codice che chiama in modo asincrono la nuova API personalizzata.

