
1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio e selezionare **Avvia nuova istanza** dal menu di scelta rapida **Debug**.

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio aprirà la pagina Web predefinita per il servizio. Per impostazione predefinita, Visual Studio ospita il servizio mobile localmente in IIS Express.

2. Fare clic con il pulsante destro del mouse sull'icona di IIS Express nella barra delle applicazioni di Windows e verificare che il servizio mobile sia stato avviato.

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Nella pagina iniziale del servizio mobile, fare clic su **Provalo**.

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Viene visualizzata la pagina di documentazione sull'API, che può essere utilizzata per effettuare il test del servizio mobile.

	>[AZURE.NOTE]Durante l'esecuzione a livello locale, l'autenticazione non è necessaria per accedere a questa pagina. Durante l'esecuzione in Azure, per accedere a questa pagina è necessario immettere la chiave di applicazione come password (senza nome utente).

4. Fare clic sul collegamento **GET tables/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Viene visualizzata la pagina della risposta GET per l'API.

5. Fare clic su **Provalo**, quindi su **Invia**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Al servizio mobile locale viene inviata una richiesta GET di restituzione di tutte le righe nella tabella TodoItem. Poiché l'inizializzatore esegue il seeding della tabella, nel corpo del messaggio di risposta vengono restituiti due oggetti TodoItem. Per altre informazioni sugli inizializzatori, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET](./it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)


<!--HONumber=42-->
