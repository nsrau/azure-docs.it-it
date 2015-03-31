

Per impostazione predefinita, tutte le richieste alle risorse del servizio mobile sono limitate ai client che presentano la chiave applicazione, che non protegge in modo efficace l'accesso alle risorse. Per proteggere le risorse, è necessario limitare l'accesso ai soli client autenticati.

1. In Visual Studio aprire il progetto di servizio mobile, espandere la cartella Controller e aprire **TodoItemController.cs**. La classe **TodoItemController**consente di implementare l'accesso ai dati per la tabella TodoItem. Aggiungere la seguente istruzione `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Applicare il seguente attributo _AuthorizeLevel_ alla classe **TodoItemController**. In questo modo, tutte le operazioni eseguite sulla tabella _TodoItem_ richiederanno un utente autenticato.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Applicare l'attributo AuthorizeLevel ai singoli metodi per impostare livelli di autorizzazione specifici per i metodi esposti dal controller.

3. Per eseguire il debug dell'autenticazione in locale, espandere la cartella `App_Start`, aprire **WebApiConfig.cs** e aggiungere il seguente codice al metodo **Register**.  

		config.SetIsHosted(true);

	In questo modo, il progetto del servizio mobile locale verrà eseguito come se fosse ospitato in Azure, incluso il rispetto delle impostazioni di *AuthorizeLevel*. Senza tale impostazione, tutte le richieste HTTP a localhost sono autorizzate senza autenticazione, indipendentemente dall'impostazione di *AuthorizeLevel*. 

4. Pubblicare di nuovo il progetto.

<!--HONumber=47-->
