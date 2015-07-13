
1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio e selezionare **Avvia nuova istanza** dal menu di scelta rapida **Debug**.

    Visual Studio aprirà la pagina Web predefinita per il servizio. Per impostazione predefinita, Visual Studio ospita il back-end dell'app per dispositivi mobili localmente in IIS Express.

2. Fare clic con il pulsante destro del mouse sull'icona di IIS Express nella barra delle applicazioni di Windows e verificare che il back-end dell'app per dispositivi mobili sia stato avviato.

	 ![verifica del servizio mobile nella barra delle applicazioni](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Nella pagina di avvio del back-end .NET fare clic su **Provalo**.

    Viene visualizzata la pagina di documentazione sull'API, che può essere usata per eseguire il test dell'app per dispositivi mobili.

	>[AZURE.NOTE]Durante l'esecuzione a livello locale, l'autenticazione non è necessaria per accedere a questa pagina. Durante l'esecuzione in Azure, per accedere a questa pagina è necessario immettere la chiave di applicazione come password (senza nome utente).

4. Fare clic sul collegamento **GET tables/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Viene visualizzata la pagina della risposta GET per l'API.

5. Fare clic su **Provalo**, quindi su **Invia**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Al back-end dell'app per dispositivi mobili locale viene inviata una richiesta GET di restituzione di tutte le righe nella tabella TodoItem. Poiché l'inizializzatore esegue il seeding della tabella, nel corpo del messaggio di risposta vengono restituiti due oggetti TodoItem. Per maggiori informazioni sugli inizializzatori, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=62-->