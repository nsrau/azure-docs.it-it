
1. Fare clic su di **servizi App** pulsante, selezionare l'App per dispositivi mobili di back-end, **Guida introduttiva**, quindi selezionare la piattaforma (iOS, Android, Xamarin, Cordova) dei client.

    ![Portale di Azure con avvio rapido di applicazioni mobili evidenziato][quickstart]

2. Se non è configurata una connessione al database, crearne uno attenendosi alla seguente:

    ![Portale di Azure con App Mobile Connect al database][connect]

    a. Creare un nuovo database SQL e server.

    ![Portale di Azure con applicazioni per dispositivi mobili creare server e un nuovo database][server]

    b. Attendere la creazione della connessione dati.

    ![Notifica del portale di Azure di creazione della connessione dati][notification]

    c. La connessione dati deve avere esito positivo.

    ![Notifica del portale di Azure, "già disponibile una connessione dati"][already-connection]

3. In **2. Creare un'API di tabella** selezionare Node.js per **Linguaggio back-end**. 
 
4. Accettare il riconoscimento e quindi selezionare **tabella TodoItem creare**.  
    Questa azione crea una nuova tabella elemento attività da eseguire nel database. 

    >[!IMPORTANT]
    > Il passaggio di un back-end esistente a Node.js sovrascrive tutti i contenuti. Per creare invece un back-end .NET, vedere [di lavoro con il server back-end .NET SDK per App per dispositivi mobili][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
