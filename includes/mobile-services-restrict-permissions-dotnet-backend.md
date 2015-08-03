

Per impostazione predefinita, tutte le richieste alle risorse del servizio mobile sono limitate ai client che presentano la chiave applicazione, che non protegge in modo efficace l'accesso alle risorse. Per proteggere le risorse, è necessario limitare l'accesso ai soli client autenticati.

1. In Visual Studio aprire il progetto del servizio mobile, espandere la cartella Controller e aprire **TodoItemController.cs**. La classe **TodoItemController** consente di implementare l'accesso ai dati per la tabella TodoItem. Aggiungere l'istruzione `using` seguente:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Applicare l'attributo _AuthorizeLevel_ seguente alla classe **TodoItemController**.

		[AuthorizeLevel(AuthorizationLevel.User)]

	In questo modo, tutte le operazioni eseguite sulla tabella _TodoItem_ richiedono un utente autenticato. È inoltre possibile applicare l’attributo *AuthorizeLevel* a livello di metodo.

3. (Facoltativo) Per eseguire il debug dell'autenticazione in locale, espandere la cartella `App_Start`, aprire **WebApiConfig.cs** e aggiungere il codice seguente al metodo **Register**.

		config.SetIsHosted(true);

	In questo modo, il progetto del servizio mobile locale verrà eseguito come se fosse ospitato in Azure, incluso il rispetto delle impostazioni di *AuthorizeLevel*. Senza tale impostazione, tutte le richieste HTTP a localhost sono autorizzate senza autenticazione, indipendentemente dall'impostazione di *AuthorizeLevel*. Quando si attiva la modalità self-hosted, è necessario impostare anche un valore per la chiave dell'applicazione locale.

4. (Facoltativo) Nel file di progetto web.config, impostare un valore stringa per l'impostazione app `MS_ApplicationKey`.

	Si tratta della password che viene usata (senza nome utente) per verificare le pagine della guida dell'API quando il servizio viene eseguito localmente. Questo valore di stringa non viene usato dal sito in Azure e non è necessario usare la chiave dell'applicazione vera e propria. Sarà possibile usare qualsiasi valore di stringa valido.
 
4. Pubblicare di nuovo il progetto.

<!---HONumber=July15_HO4-->