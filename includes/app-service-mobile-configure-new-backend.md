
1. Fare clic sul pulsante **Servizi app** e selezionare il back-end delle app per dispositivi mobili, quindi **Avvio rapido** e infine la piattaforma client (iOS, Android, Xamarin o Cordova).

    ![Portale di Azure con Avvio rapido per le app per dispositivi mobili evidenziato][quickstart]

2. Se non è configurata la connessione a un database, crearne una seguendo questa procedura:

    ![Portale di Azure con connessione a un database per le app per dispositivi mobili][connect]

    a. Creare un nuovo server e un nuovo database SQL.

    ![Portale di Azure con creazione di un nuovo database e un nuovo server per le app per dispositivi mobili][server]

    b. Attendere la creazione della connessione dati.

    ![Notifica del completamento della creazione della connessione dati nel portale di Azure][notification]

    c. La connessione dati deve avere esito positivo.

    ![Notifica "Esiste già una connessione dati" nel portale di Azure][already-connection]

3. In **2. Creare un'API di tabella** selezionare Node.js per **Linguaggio back-end**. 
 
4. Accettare l'acknowledgment e quindi selezionare **Crea tabella TodoItem**.  
    Con questa azione viene creata una nuova tabella di attività nel database. 

    >[!IMPORTANT]
    > Il passaggio di un back-end esistente a Node.js sovrascrive tutto il contenuto. Per creare invece un back-end .NET, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
