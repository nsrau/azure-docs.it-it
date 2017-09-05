
1. Fare clic su **Servizi app**, selezionare il back-end dell'app per dispositivi mobili, fare clic su **Avvio rapido**, selezionare la piattaforma client (iOS, Android, Xamarin, Cordova).

![Portale di Azure con evidenziata la guida introduttiva alle app per dispositivi mobili][quickstart]

2. Se la connessione al database non è configurata, è necessario creare una connessione dati.

![Portale di Azure con app per dispositivi mobili, connessione a BD][connect]

  * Creare un nuovo server e un database SQL.

  ![Portale di Azure con app per dispositivi mobili, creazione di un nuovo BD e server][server]

  * Attendere la creazione della connessione dati.

  ![Portale di Azure con app per dispositivi mobili, notifica di creazione della connessione dati][notification]

  * La connessione dati deve avere esito positivo.

  ![Portale di Azure con app per dispositivi mobili, notifica di creazione della connessione dati][already-connection]

3. In **2. Creare un'API di tabella** selezionare Node.js per **Linguaggio back-end**. Accettare l'acknowledgment e fare clic su **Crea tabella TodoItem**. Verrà creata una nuova tabella *TodoItem* nel database. Tenere presente che il passaggio di un back-end esistente a Node.js sovrascriverà tutto il contenuto. Per creare invece un back-end .NET, [seguire queste istruzioni][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
