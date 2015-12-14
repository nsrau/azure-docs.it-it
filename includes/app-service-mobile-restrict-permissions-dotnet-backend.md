
Per impostazione predefinita, le API in un back-end dell'app per dispositivi mobili possono essere richiamate in modo anonimo. È necessario limitare l'accesso solo ai client autenticati.

+ **Back-end Node.js (tramite portale)** :  
	
	Nelle**Impostazioni** di un’app per dispositivi mobili, fare clic su **Tabelle facili** e selezionare la tabella. Fare clic su **Modifica autorizzazioni**, selezionare **Solo accesso autenticato** per tutte le autorizzazioni, e fare clic su **Salva**.

+ **Back-end .NET (C#)**:

	Nel progetto server passare a **Controller** > **TodoItemController.cs**. Aggiungere l'attributo `[Authorize]` alla classe **TodoItemController**, come indicato di seguito: Per limitare l'accesso solo a metodi specifici, è inoltre possibile applicare questo attributo solo a tali metodi anziché alla classe. Pubblicare di nuovo il progetto server.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end Node.js (tramite codice Node.js)** :
	
	Per richiedere l'autenticazione per l'accesso alla tabella, aggiungere la riga seguente allo script del server Node.js:


        table.access = 'authenticated';

	Per ulteriori informazioni, fare riferimento a [Richiedere l'autenticazione per l'accesso alle tabelle](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth) in [Come utilizzare l’SDK di Node.js di App per dispositivi mobili di Azure](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md).

<!---HONumber=AcomDC_1203_2015-->