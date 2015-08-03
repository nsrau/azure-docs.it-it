

Per impostazione predefinita, gli endpoint definiti nell'app per dispositivi mobili vengono esposti pubblicamente. Per proteggere le risorse, è necessario limitare l'accesso ai soli client autenticati.

1. In Visual Studio aprire il progetto contenente il codice dell'app mobile. 

2. In Esplora soluzioni espandere la cartella Controllers e aprire il file di progetto TodoItemController.cs.

	La classe **TodoItemController** consente di implementare l'accesso ai dati per la tabella TodoItem.

3. Applicare l'attributo `Authorize` alla classe **TodoItemController**:

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato.

	>[AZURE.NOTE]Applicare l'attributo Authorize ai singoli metodi per impostare livelli di autorizzazione specifici per i metodi esposti dal controller.

4. Ripubblicare il progetto dell'app mobile.

<!---HONumber=July15_HO4-->