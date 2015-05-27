
1. In Visualizzazione soluzione espandere la cartella **Componenti** nell'app Xamarin.Android e assicurarsi che il pacchetto Servizi mobili di Azure sia installato. 

2. Fare clic con il pulsante destro del mouse sulla cartella **Componenti**, fare clic su **Altri componenti**, cercare il componente **Client di Google Cloud Messaging** e aggiungerlo al progetto.

1. Aprire il nuovo file di progetto ToDoActivity.cs e aggiungere l'istruzione using seguente alla classe:

		using Gcm.Client;

2. Nella classe **ToDoActivity** aggiungere il nuovo codice seguente:

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

	Questa operazione consente di accedere all'istanza del client di Servizi mobili dal processo del servizio.

3. Modificare la dichiarazione client di Servizi mobili esistente impostando il valore su pubblico, come indicato di seguito:

		public MobileServiceClient client { get; private set; }

4.	Aggiungere il codice seguente al metodo **OnCreate**, dopo aver creato **MobileServiceClient**:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

La classe **ToDoActivity** è ora pronta per l'aggiunta di notifiche push.
<!--HONumber=54-->