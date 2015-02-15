

Per impostazione predefinita, tutte le richieste alle risorse del servizio mobile sono limitate ai client che presentano la chiave applicazione, che non protegge in modo efficace l'accesso alle risorse. Per proteggere le risorse, è necessario limitare l'accesso ai soli client autenticati.

1. In Visual Studio aprire il progetto contenente il servizio mobile. 

2. In Esplora soluzioni espandere la cartella Controllers e aprire il file di progetto TodoItemController.cs.

	La classe **TodoItemController**consente di implementare l'accesso ai dati per la tabella TodoItem. 

3. Aggiungere la seguente istruzione `using` all'inizio della pagina dei codici:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

4. Applicare il seguente attributo AuthorizeLevel alla classe **TodoItemController**:

		[AuthorizeLevel(AuthorizationLevel.User)] 

	In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. 

	>[AZURE.NOTE]Applicare l'attributo AuthorizeLevel ai singoli metodi per impostare livelli di autorizzazione specifici per i metodi esposti dal controller.

5. Per eseguire il debug dell'autenticazione in locale, espandere la cartella App_Start, aprire il file di progetto WebApiConfig.cs, quindi aggiungere il seguente codice al metodo **Register**:

		config.SetIsHosted(true);
	
	In questo modo, il progetto del servizio mobile locale verrà eseguito come se fosse ospitato in Azure, incluso il rispetto delle impostazioni di AuthorizeLevel. Senza tale impostazione, tutte le richieste HTTP a *localhost* sono autorizzate senza autenticazione, indipendentemente dall'impostazione di AuthorizeLevel.  

6. Pubblicare di nuovo il progetto di servizio.


<!--HONumber=42-->
