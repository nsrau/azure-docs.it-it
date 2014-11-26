1.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio e selezionare **Avvia nuova istanza** dal menu di scelta rapida **Debug**.

    ![avvio del progetto di servizio mobile a livello locale][avvio del progetto di servizio mobile a livello locale]

    Visual Studio aprirà la pagina Web predefinita per il servizio. Per impostazione predefinita, Visual Studio ospita il servizio mobile localmente in IIS Express.

2.  Fare clic con il pulsante destro del mouse sull'icona di IIS Express nella barra delle applicazioni di Windows e verificare che il servizio mobile sia stato avviato.

    ![verifica del servizio mobile nella barra delle applicazioni][verifica del servizio mobile nella barra delle applicazioni]

3.  Nella pagina di avvio del servizio mobile, fare clic su **Provalo**.

    ![pagina di avvio del servizio mobile][pagina di avvio del servizio mobile]

    Viene visualizzata la pagina di documentazione sull'API, che può essere utilizzata per effettuare il test del servizio mobile.

    > [WACOM.NOTE]Durante l'esecuzione a livello locale, l'autenticazione non è necessaria per accedere a questa pagina. Durante l'esecuzione in Azure, per accedere a questa pagina è necessario immettere la chiave di applicazione come password (senza nome utente).

4.  Fare clic sul collegamento **GET tables/TodoItem**.

    ![][0]

    Viene visualizzata la pagina della risposta GET per l'API.

5.  Fare clic su **Provalo**, quindi su **Invia**.

    ![][1]

    Al servizio mobile locale viene inviata una richiesta GET di restituzione di tutte le righe nella tabella TodoItem. Poiché l'inizializzatore esegue il seeding della tabella, nel corpo del messaggio di risposta vengono restituiti due oggetti TodoItem. Per maggiori informazioni sugli inizializzatori, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET][Come modificare un modello di dati in un servizio mobile back-end .NET].

    ![][2]

  [avvio del progetto di servizio mobile a livello locale]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png
  [verifica del servizio mobile nella barra delle applicazioni]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png
  [pagina di avvio del servizio mobile]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png
  [0]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png
  [1]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png
  [Come modificare un modello di dati in un servizio mobile back-end .NET]: ./it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
  [2]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png
