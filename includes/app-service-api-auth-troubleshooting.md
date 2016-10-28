Spesso la maggior parte degli errori di autenticazione sono causati da impostazioni di configurazione non corrette o incoerenti. Ecco alcuni suggerimenti specifici per gli elementi da verificare.

* Assicurarsi di aver selezionato il pulsante **Salva**. Questa operazione non è scontata e capita spesso di visualizzare i valori correnti nella pagina del portale senza averli effettivamente salvanti nell'ambiente Azure o nell'applicazione Azure AD.
* Per le impostazioni configurate nel pannello **Impostazioni applicazione** del portale di Azure, verificare che sia stata selezionata l'app per le API o l'app Web corretta quando sono state immesse le impostazioni. Assicurarsi inoltre che le impostazioni sono state immesse come **Impostazioni app** e non come **Stringhe di connessione**, dal momento che il formato delle due sezioni è simile.
* Per l'autenticazione a un front-end JavaScript, scaricare nuovamente il manifesto per verificare che `oauth2AllowImplicitFlow` è stato modificato correttamente in `true`.
* Verificare di avere usato HTTPS ogni volta che sono stati configurati gli URL:

	* Nel codice del progetto
	* In CORS
	* Nelle impostazioni dell'app dell'ambiente Azure per ogni app per le API e app Web
	* Nelle impostazioni dell'applicazione di Azure AD.
	
	Si noti che se si copia l'URL dell'app per le API dal portale, spesso presenta `http://` ed è necessario modificarlo manualmente in `https://`.

* Assicurarsi che tutte le modifiche del codice sono state correttamente distribuite. In una soluzione multiprogetto, ad esempio, è possibile modificare il codice del progetto e scegliere per errore uno tra questi quando si prevede di distribuire la modifica.
* Verificare che si stia passando a URL HTTPS nel browser e non a URL HTTP. Per impostazione predefinita, Visual Studio crea profili di pubblicazione con URL HTTP, che verranno poi aperti nel browser dopo aver distribuito un progetto.
* Per l'autenticazione a un front-end JavaScript, assicurarsi che CORS sia configurato correttamente nell'app per le API che chiama il codice JavaScript. In caso di dubbio che il problema sia correlato a CORS, provare "*" come URL di origine consentito.
* Per un front-end JavaScript, aprire la scheda relativa alla console degli strumenti di sviluppo del browser per visualizzare altre informazioni sugli errori ed esaminare le richieste HTTP in rete. Tuttavia, i messaggi di errore della console possono essere fuorvianti. Se viene visualizzato un messaggio che indica un errore CORS, il vero problema potrebbe essere l'autenticazione. È possibile verificare il problema eseguendo l'app con l'autenticazione temporaneamente disabilitata.
* Per un'app per le API .NET, assicurarsi di ottenere il maggior numero possibile di informazioni nei messaggi di errore impostando [customErrors mode su Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Per un'app per le API .NET, avviare una [sessione di debug remoto](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) ed esaminare i valori delle variabili che vengono passati al codice che usa ADAL per acquisire un token di connessione, o al codice che verifica le attestazioni rispetto all'ID entità servizio previsto. Si noti che il codice può ottenere i valori di configurazione da più origini diverse, quindi è possibile che questo metodo restituisca risultati non previsti. Se, ad esempio, si inserisce per errore `ida:ClientId` invece di `ida:ClientID` quando si configurano le impostazioni dell'ambiente del servizio app di Azure, è possibile che il codice ottenga il valore `ida:ClientId` che sta cercando dal file Web.config, ignorando l'impostazione del servizio app di Azure. 
* Se ciò non funziona in una normale finestra di Internet Explorer, gli accessi esistenti potrebbero interferire. Provare quindi la modalità InPrivate e Firefox o Chrome.

<!---HONumber=AcomDC_0309_2016-->