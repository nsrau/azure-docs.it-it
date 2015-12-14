
1. Nella visualizzazione Solution, o **Esplora soluzioni** in Visual Studio, fare clic con il pulsante destro del mouse sulla cartella **Componenti**, fare clic su **Altri componenti**, cercare il componente **Client di Google Cloud Messaging** e aggiungerlo al progetto.

2. Aprire il nuovo file di progetto ToDoActivity.cs e aggiungere l'istruzione using seguente alla classe:

		using Gcm.Client;

3. Nella classe **ToDoActivity** aggiungere il nuovo codice seguente:

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	Questa operazione consente di accedere all'istanza del client mobile dal processo del servizio del gestore push.

4.	Aggiungere il codice seguente al metodo **OnCreate**, dopo aver creato **MobileServiceClient**:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

La classe **ToDoActivity** è ora pronta per l'aggiunta di notifiche push.

<!---HONumber=AcomDC_1203_2015-->