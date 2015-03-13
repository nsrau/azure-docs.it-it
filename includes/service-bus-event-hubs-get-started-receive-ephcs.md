## Ricevere messaggi con EventProcessorHost

**EventProcessorHost** è una classe .NET che semplifica la ricezione di messaggi da hub eventi mediante la gestione di checkpoint persistenti e ricezioni parallele dagli hub eventi. Usando **EventProcessorHost**, è possibile suddividere eventi su più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare **EventProcessorHost** per un ricevitore singolo. L'[esempio di elaborazione di eventi scalata orizzontalmente] mostra come usare **EventProcessorHost** con più ricevitori.

Per altre informazioni sui modelli di ricezione degli hub eventi, vedere la [Guida per gli sviluppatori di Hub eventi di Azure].

[EventProcessorHost] è una classe .NET che semplifica la ricezione di messaggi da hub eventi mediante la gestione di checkpoint persistenti e ricezioni parallele da tali hub eventi. Usando [EventProcessorHost], è possibile suddividere eventi su più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare [EventProcessorHost] per un ricevitore singolo. L'[esempio di elaborazione di eventi scalata orizzontalmente] mostra come usare [EventProcessorHost] con più ricevitori.

Per poter usare [EventProcessorHost],è necessario avere un [account di archiviazione di Azure]:

1. Accedere al portale di gestione di [Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi dati**, quindi su **Archiviazione**, su **Creazione rapida**, quindi digitare un nome per l'account di archiviazione. Selezionare l'area desiderata e quindi fare clic su **Crea account di archiviazione**.

   	![][11]

3. Fare clic sull'account di archiviazione appena creato, quindi su **Gestisci chiavi di accesso**:

   	![][12]

	Copiare la chiave di accesso per usarla in seguito.

4. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Receiver**.

   	![][14]

5. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

	Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

6. Cercare  `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, quindi fare clic su **Installa** e accettare le condizioni per l'utilizzo. 

	![][13]

	Viene scaricato e installato il <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost</a>, con tutte le relative dipendenze e vi viene aggiunto un riferimento.

7. Creare una nuova classe denominata **SimpleEventProcessor** e aggiungere le istruzioni seguenti all'inizio del file:

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	Aggiungere quindi il codice seguente come corpo della classe:

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5)) 
                { 
                    await context.CheckpointAsync(); 
                    this.checkpointStopWatch.Restart(); 
                } 
	        }
	    }

	Questa classe verrà chiamata da **EventProcessorHost** per elaborare eventi ricevuti dall'hub eventi. Si noti che la classe `SimpleEventProcessor` usa un cronometro per chiamare periodicamente il metodo checkpoint sul contesto di **EventProcessorHost**. Questo assicura che, se il ricevitore viene riavviato, non perderà più di cinque minuti di lavoro di elaborazione.

8. All'inizio della classe **Program** aggiungere le seguenti istruzioni `using`:

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	Aggiungere quindi il codice seguente nel metodo **Main**, sostituendo il nome dell'hub eventi, la stringa di connessione, nonché l'account di archiviazione e la chiave copiata nelle sezioni precedenti:

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] Questa esercitazione usa una singola istanza di [EventProcessorHost]. Per aumentare la velocità effettiva, è consigliabile eseguire più istanze di [EventProcessorHost], come illustrato nell'[esempio di elaborazione di eventi scalata orizzontalmente]. In questi casi, le varie istanze si coordinano automaticamente tra loro per ottenere il bilanciamento del carico relativo agli eventi ricevuti. Se si vuole che ognuno di più ricevitori elabori *all* gli eventi, è necessario usare il concetto **ConsumerGroup**. Quando si ricevono eventi da più macchine, potrebbe risultare utile specificare nomi per le istanze di [EventProcessorHost] in base alle macchine (o ai ruoli) in cui sono distribuite. Per altre informazioni su questi argomenti, vedere [Panoramica di Hub eventi] e [Guida alla programmazione di Hub eventi].

<!-- Links -->
[Panoramica di Hub eventi]: http://msdn.microsoft.com/library/azure/dn821413.aspx
[Esempio di elaborazione di eventi scalata orizzontalmente]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Account di archiviazione di Azure]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Guida alla programmazione di Hub eventi]: http://msdn.microsoft.com/library/azure/dn789972.aspx<!--HONumber=42-->
