## Ricevere messaggi con EventProcessorHost

[EventProcessorHost] è una classe .NET che semplifica la ricezione di messaggi da Hub eventi mediante la gestione di checkpoint persistenti e ricezioni parallele da tali hub. Usando [EventProcessorHost], è possibile suddividere gli eventi tra più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare [EventProcessorHost] per un singolo ricevitore. L'esempio di [elaborazione di eventi scalata orizzontalmente] mostra come utilizzare [EventProcessorHost] con più ricevitori.

Per poter usare [EventProcessorHost], è necessario avere un [account di Archiviazione di Azure]\:

1. Accedere al [portale di gestione di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi dati**, quindi su **Archiviazione** e infine su **Creazione rapida** e digitare un nome per l'account di archiviazione. Selezionare l'area desiderata e quindi fare clic su **Crea account di archiviazione**.

    ![][11]

3. Fare clic sull'account di archiviazione appena creato e quindi su **Gestisci chiavi di accesso**:

    ![][12]

    Copiare la chiave di accesso da usare più avanti in questa esercitazione.

4. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Receiver**.

    ![][14]

5. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.

	Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

6. Cercare `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, fare clic su **Installa** e accettare le condizioni per l'uso.

    ![][13]

	Viene scaricato e installato il <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost</a>, con tutte le relative dipendenze, e viene aggiunto un riferimento a tale pacchetto.

7. Fare clic con il pulsante destro del mouse sul progetto **Receiver**, scegliere **Aggiungi** e quindi **Classe**. Assegnare alla nuova classe il nome **SimpleEventProcessor** e quindi fare clic su **OK** per crearla.

8. Aggiungere le istruzioni seguenti all'inizio del file SimpleEventProcessor.cs:

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	Sostituire quindi il corpo della classe con il codice seguente:

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;

	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }

	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
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

9. All'inizio della classe **Program** aggiungere le istruzioni `using` seguenti:

		using Microsoft.ServiceBus.Messaging;
		using Microsoft.Threading;
		using System.Threading.Tasks;

	Modificare quindi il metodo **Main** nella classe **Program** come illustrato di seguito, sostituendo il nome dell'hub eventi, la stringa di connessione, nonché l'account di archiviazione e la chiave copiata nelle sezioni precedenti:

        static void Main(string[] args)
        {
          string eventHubConnectionString = "{event hub connection string}";
          string eventHubName = "{event hub name}";
          string storageAccountName = "{storage account name}";
          string storageAccountKey = "{storage account key}";
          string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
              storageAccountName, storageAccountKey);

          string eventProcessorHostName = Guid.NewGuid().ToString();
          EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
          Console.WriteLine("Registering EventProcessor...");
          eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

          Console.WriteLine("Receiving. Press enter key to stop worker.");
          Console.ReadLine();
          eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]Questa esercitazione usa una singola istanza di [EventProcessorHost]. Per aumentare la velocità effettiva, è consigliabile eseguire più istanze di [EventProcessorHost], come illustrato nell'esempio di [elaborazione di eventi scalata orizzontalmente]. In questi casi, le varie istanze si coordinano automaticamente tra loro per ottenere il bilanciamento del carico relativo agli eventi ricevuti. Se si vuole che ognuno dei vari ricevitori elabori *tutti* gli eventi, è necessario usare il concetto **ConsumerGroup**. Quando si ricevono eventi da più macchine, potrebbe risultare utile specificare nomi per le istanze di [EventProcessorHost] in base alle macchine (o ai ruoli) in cui sono distribuite. Per altre informazioni su questi argomenti, vedere [Panoramica di Hub eventi] e [Guida alla programmazione di Hub eventi].

<!-- Links -->
[Panoramica di Hub eventi]: http://azure.microsoft.com/documentation/articles/event-hubs-overview/
[elaborazione di eventi scalata orizzontalmente]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[account di Archiviazione di Azure]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[portale di gestione di Azure]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Guida alla programmazione di Hub eventi]: http://azure.microsoft.com/documentation/articles/event-hubs-programming-guide/
[Async Await in Console Apps]: http://blogs.msdn.com/b/pfxteam/archive/2012/01/20/10259049.aspx
[AsyncPump.cs]: http://blogs.msdn.com/cfs-file.ashx/__key/communityserver-components-postattachments/00-10-25-90-49/AsyncPump_2E00_cs

<!---HONumber=July15_HO5-->