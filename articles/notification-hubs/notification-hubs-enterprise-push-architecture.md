<properties 
	pageTitle="Hub di notifica: architettura push aziendale" 
	description="Indicazioni sull'uso di Hub di notifica di Azure in un ambiente aziendale" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# Guida all'architettura push aziendale

Al giorno d'oggi, le aziende stanno gradualmente passando alla creazione di applicazioni per dispositivi mobili sia per gli utenti finali (esterni) che per i dipendenti (interni). Le aziende dispongono di sistemi back-end già esistenti, ovvero mainframe o applicazioni LoB che è necessario integrare nell'architettura delle applicazioni per dispositivi mobili. In questa Guida verrà illustrato come eseguire questa integrazione nel modo migliore possibile, fornendo possibili soluzioni per scenari comuni.

Una richiesta frequente riguarda l'invio di notifiche push agli utenti tramite l'applicazione per dispositivi mobili in uso quando si verifica un evento di interesse nei sistemi back-end. Una cliente di una banca, ad esempio, ha installato sul proprio iPhone l'app dei servizi bancari della banca stessa e desidera ricevere una notifica quando sul conto viene effettuato un addebito superiore a un determinato importo. Oppure, in uno scenario intranet, un dipendente del reparto finanziario ha installato sul proprio Windows Phone una app per l'approvazione dei budget e desidera ricevere una notifica quando gli viene inviata una richiesta di approvazione.
 
È probabile che l'elaborazione del conto o dell'approvazione venga eseguita in un qualche sistema back-end che deve avviare un'operazione push verso l'utente. È possibile che siano presenti diversi sistemi back-end che devono eseguire lo stesso tipo di logica per implementare un push quando un evento attiva una notifica. In questo caso la complessità è dovuta alla necessità di integrare numerosi back-end con un singolo sistema di push, dove gli utenti finali possono aver eseguito la sottoscrizione a diverse notifiche e dove possono essere usate più applicazioni mobili, ad esempio nel caso di app per dispositivi mobili intranet che possono ricevere notifiche da diversi sistemi back-end. I sistemi back-end non conoscono o non hanno necessità di conoscere la semantica e/o la tecnologia di push. Per questo motivo, una soluzione comunemente usata fino ad ora consiste nell'introdurre un componente che esegue il polling dei sistemi back-end per qualsiasi evento di interesse ed è responsabile dell'invio di messaggi push al client. In questo documento viene presa in considerazione una soluzione ottimizzata che prevede l'uso del bus di servizio di Azure: si tratta di un modello argomento/sottoscrizione in grado di ridurre la complessità assicurando al tempo stesso la scalabilità della soluzione.

Di seguito è descritta l'architettura generale della soluzione, descritta con numerose app per dispositivi mobili ma ugualmente applicabile nel caso in cui ne venga usata una soltanto.

## Architettura

![][1]

L'elemento chiave del diagramma dell'architettura è costituito dal bus di servizio di Azure, che offre un modello di programmazione per argomenti/sottoscrizioni. Per altre informazioni, vedere[ Come usare argomenti/sottoscrizioni del bus di servizio]. Il ricevitore, in questo caso il back-end Mobile (in genere [Servizi mobili di Azure], che avvia un'operazione push alle app per dispositivi mobili), non riceve messaggi direttamente dai sistemi back-end. È disponibile invece di un livello di astrazione intermedio fornito dal [bus di servizio di Azure] che consente al back-end Mobile di ricevere messaggi da uno o più sistemi back-end. È necessario creare un argomento del bus di servizio per ciascuno dei sistemi back-end, ad esempio Contabilità, Risorse umane e Finanza: si tratta fondamentalmente di "argomenti" di interesse che avvieranno l'invio di messaggi come notifiche push. I sistemi back-end invieranno messaggi a questi argomenti. Un back-end Mobile può sottoscrivere uno o più di tali argomenti creando una sottoscrizione del bus di servizio. Questo autorizza il back-end Mobile a ricevere una notifica dal sistema back-end corrispondente. Il back-end Mobile resta in ascolto dei messaggi inviati alle proprie sottoscrizioni e, non appena ne arriva uno, lo invia come notifica all'hub di notifica. L'hub di notifica invia infine il messaggio all'app per dispositivi mobili. Di seguito sono riepilogati i componenti chiave:

1. Sistema back-end (sistemi LoB/legacy)
	- Crea un argomento del bus di servizio
	- Invia un messaggio
2. Back-end Mobile
	- Crea una sottoscrizione al servizio
	- Riceve un messaggio (dal sistema back-end)
	- Invia la notifica al client (tramite Hub di notifica di Azure)
3. Applicazione per dispositivi mobili
	- Riceve e visualizza la notifica
		
###Vantaggi:

1. Il disaccoppiamento tra il ricevitore (app/servizio per dispositivi mobili tramite Hub di notifica) e il mittente(sistemi back-end) consente l'integrazione di sistemi back-end aggiuntivi con modifiche minime.
2. In questo modo, è possibile ricevere eventi da uno o più sistemi back-end anche nello scenario con più app per dispositivi mobili.  

## Esempio:

###Prerequisiti
È necessario completare le seguenti esercitazioni per acquisire familiarità con i concetti e con i comuni passaggi di creazione e configurazione:

1. [Come usare argomenti/sottoscrizioni del bus di servizio]: illustrati i dettagli relativi all'uso di argomenti/sottoscrizioni del bus di servizio, come creare uno spazio dei nomi che contiene argomenti/sottoscrizioni e come inviare e ricevere messaggi da questi ultimi. 
2. [Introduzione ad Hub di notifica]: illustra come configurare un'app di Windows Store e usare Hub di notifica per registrare e quindi ricevere le notifiche. 

###Codice di esempio

Il codice completo è disponibile nella pagina relativa agli [esempi di Hub di notifica]. Il codice è suddiviso in tre componenti:

1. **EnterprisePushBackendSystem**
	
	a. Il progetto usa il pacchetto *Nuget WindowsAzure.ServiceBus* ed è basato su quanto riportato in [Come usare argomenti/sottoscrizioni del bus di servizio].

	b. Si tratta di una app console C# per simulare un sistema LoB che avvia il messaggio da recapitare all'app per dispositivi mobili.
	
		static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }
	
	c. `CreateTopic` viene usato per creare l'argomento del bus di servizio a cui verranno inviati i messaggi.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

	d. `SendMessage` viene usato per inviare i messaggi a questo argomento del bus di servizio. Ai fini dell'esempio, viene semplicemente inviato, a cadenza periodica, un insieme di messaggi casuali all'argomento. Nella realtà, sarebbe presente un sistema back-end che invia i messaggi quando si verifica un evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages = 
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

	a. Il progetto usa i pacchetti Nuget *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* ed è basato su quanto riportato in [Come usare argomenti/sottoscrizioni del bus di servizio].

	b. Si tratta di un'altra app console C# che verrà eseguita come processo [Web di Azure]. Questa app deve infatti essere eseguita continuamente per ascoltare i messaggi dei sistemi LOB/back-end. L'app sarà parte del back-end Mobile.

	    static void Main(string[] args)
	    {
	        string connectionString =
	                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
	        // Create the subscription which will receive messages
	        CreateSubscription(connectionString);   
	
	        // Receive message
	        ReceiveMessageAndSendNotification(connectionString);
	    }

	c. `CreateSubscription` viene usato per creare una sottoscrizione al bus di servizio per l'argomento a cui il sistema back-end invia i messaggi. A seconda dello scenario di business, questo componente creerà una o più sottoscrizioni agli argomenti corrispondenti. Ad esempio, alcuni possono ricevere messaggi dal sistema Risorse umane, altri dal sistema Finanza e così via.

	    static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

	d. ReceiveMessageAndSendNotification viene usato per leggere il messaggio dall'argomento usando la relativa sottoscrizione. Se la lettura ha esito positivo, viene creata una notifica (nello scenario di esempio, una notifica di tipo avviso popup di Windows) da inviare all'applicazione per dispositivi mobili mediante Hub di notifica di Azure.

		static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
            
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription 
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            } 
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

	e. Per pubblicare questo elemento come **processo Web**, in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Pubblica come processo Web di Azure**.

	![][2]

	f. Selezionare il proprio profilo di pubblicazione e, se non è già presente, creare un nuovo sito Web di Azure che ospiterà questo processo Web, quindi fare clic su **Pubblica**.
	
	![][3]

	g. Configurare il processo per l'esecuzione continua, in modo che, quando si accede al portale di gestione d Azure, venga visualizzata una schermata simile alla seguente:

	![][4]


3. **EnterprisePushMobileApp**

	a. Si tratta di un'applicazione Windows Store che riceve notifiche di tipo avviso popup dal processo Web in esecuzione come parte del back-end Mobile e le visualizza. Si basa su quanto riportato in [Introduzione ad Hub di notifica - Esercitazione per Windows Universal].
	
	b. Verificare che l'applicazione sia abilitata alla ricezione di notifiche di tipo avviso popup.

	c. Assicurarsi che all'avvio dell'app, dopo aver sostituito *HubName* e *DefaultListenSharedAccessSignature*, venga chiamato il seguente codice di registrazione di Hub di notifica:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### Esecuzione di esempio:

1. Assicurarsi che il processo Web venga eseguito correttamente e che sia pianificato per l'esecuzione continua. 
2. Eseguire **EnterprisePushMobileApp** che avvierà l'app di Windows Store. 
3. Eseguire l'applicazione console **EnterprisePushBackendSystem** che simula il back-end LOB e avvia l'invio di messaggi. Verranno visualizzate notifiche di tipo avviso popup simili alle seguenti: 

	![][5]

4. All'inizio i messaggi sono stati inviati ad argomenti del bus di servizio, operazione monitorata da sottoscrizioni del bus di servizio nel processo Web. Una volta ricevuto un messaggio, è stata creata una notifica che è stata inviata all'app per dispositivi mobili. Per confermare l'elaborazione, è possibile esaminare i log del processo Web, selezionando Log in relazione al processo Web nel portale di gestione di Azure:

	![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[esempi di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples
[Servizi mobili di Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[bus di servizio di Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[ Come usare argomenti/sottoscrizioni del bus di servizio]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Come usare argomenti/sottoscrizioni del bus di servizio]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Web di Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Introduzione ad Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Introduzione ad Hub di notifica - Esercitazione per Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
 

<!---HONumber=July15_HO1-->