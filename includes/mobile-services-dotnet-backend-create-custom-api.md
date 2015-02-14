

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controllers relativa al progetto di servizio mobile, espandere **Aggiungi**, quindi fare clic su **New Scaffolded Item**.

	Verrà visualizzata la finestra di dialogo Add Scaffold.

2. Espandere **Servizi mobili di Microsoft Azure**, fare clic su **Controller personalizzato per Servizi mobili di Microsoft Azure**, selezionare **Aggiungi**, specificare il **Nome controller**  `CompleteAllController` e fare nuovamente clic su **Aggiungi**.

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Verrà creata una nuova classe controller vuota denominata **CompleteAllController**.

>[AZURE.NOTE]Se la finestra di dialogo non include scaffolding specifici di Servizi mobili, creare invece un nuovo oggetto **Web API Controller - Empty**. Nella nuova classe controller aggiungere una proprietà **Services** pubblica, che restituisce il tipo **ApiServices**. Questa proprietà viene utilizzata per accedere a impostazioni specifiche del server dall'interno del controller.

3. Nel nuovo file di progetto CompleteAllController.cs aggiungere le istruzioni **using** seguenti:

		using System.Threading.Tasks;
		using todolistService.Models;

	Nel codice precedente sostituire  `todolistService` con lo spazio dei nomi del progetto di servizio mobile, che deve essere costituito dal nome del servizio mobile a cui viene aggiunto  `Service`. 

4. In CompleteAllController.cs aggiungere allo spazio dei nomi la definizione di classe riportata di seguito. La classe include la risposta inviata al client.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. Aggiungere il codice seguente al nuovo controller:

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	Nel codice precedente sostituire  `todolistContext` con il nome dell'oggetto DbContext del modello di dati, che deve essere costituito dal nome del servizio mobile a cui viene aggiunto  `Context`. Sostituire inoltre il nome dello schema nell'istruzione UPDATE con il nome del servizio mobile. 

	In questo codice viene utilizzata la [classe di database](http://msdn.microsoft.com/it-it/library/system.data.entity.database.aspx) per accedere direttamente alla tabella **TodoItems** e impostare il contrassegno di completamento su tutti gli elementi. Questo metodo supporta una richiesta POST e al client viene restituito un valore intero corrispondente al numero di righe modificate.

	> [AZURE.NOTE] Poiché vengono impostate autorizzazioni predefinite, qualsiasi utente dell'app può chiamare l'API personalizzata. Tuttavia, la chiave dell'applicazione non viene distribuita né archiviata in modo sicuro e non può essere considerata una credenziale di sicurezza. Per questo motivo, è consigliabile limitare l'accesso solo agli utenti autenticati per le operazioni che modificano dati o hanno effetto sul servizio mobile. 

In seguito, l'app della Guida introduttiva verrà modificata per aggiungere un pulsante New e il codice che chiama in modo asincrono la nuova API personalizzata.

<!--HONumber=42-->
