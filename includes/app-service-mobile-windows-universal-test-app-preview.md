
Tornare in Visual Studio e selezionare il progetto Shared Code Client App (con un nome simile a `<nome dell'app>.Shared`)

1. Espandere il file **App.xaml**, quindi aprire il file **App.xaml.cs**. Individuare la dichiarazione del membro `MobileService` inizializzato con un URL localhost. Impostare come commento la dichiarazione (con `CTRL+K,CTRL+C`) e rimuovere il commento alla dichiarazione (`CTRL+K,CTRL+U`) che esegue la connessione al servizio ospitato:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. Premere il tasto F5 per generare nuovamente il progetto e avviare l'app Windows Store, che dovrebbe essere il progetto di avvio predefinito.

2. Nell'app, immettere del testo significativo, ad esempio *Complete the tutorial*, nella casella di testo **Immettere un TodoItem**, quindi fare clic su **Salva**.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	Tale operazione invia una richiesta POST al nuovo back-end dell'app mobile ospitato in Azure.

3. Interrompere l'operazione di debug e modificare il progetto di avvio predefinito nella soluzione universale Windows all'app Windows Phone Store (fare clic con il pulsante destro del mouse sul progetto `<nome dell'app>.WindowsPhone` e fare clic su **Imposta come progetto di avvio**), quindi premere di nuovo F5.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Tenere presente che i dati salvati dal passaggio precedente vengono caricati dall'app mobile dopo l'avvio dell'app Windows.


<!--HONumber=49-->