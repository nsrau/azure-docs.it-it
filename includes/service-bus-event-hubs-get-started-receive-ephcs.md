## Ricevere messaggi con EventProcessorHost

[EventProcessorHost][] è una classe .NET che semplifica la ricezione di messaggi da Hub eventi mediante la gestione di checkpoint persistenti e ricezioni parallele da tali hub. Usando [EventProcessorHost][], è possibile suddividere gli eventi tra più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare [EventProcessorHost][] per un singolo ricevitore. L'esempio di [elaborazione di eventi scalata orizzontalmente][] mostra come usare [EventProcessorHost][] con più ricevitori.

Per usare [EventProcessorHost][] è necessario avere un [account di archiviazione di Azure][]\:

1. Accedere al [portale di Azure classico][] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi dati**, quindi su **Archiviazione** e infine su **Creazione rapida** e digitare un nome per l'account di archiviazione. Selezionare l'area desiderata e quindi fare clic su **Crea account di archiviazione**.

    ![][11]

3. Fare clic sull'account di archiviazione appena creato e quindi su **Gestisci chiavi di accesso**:

    ![][12]

    Copiare la chiave di accesso primaria da usare più avanti in questa esercitazione.

4. In Visual Studio creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **Receiver**.

    ![][14]

5. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

6. Fare clic sulla scheda **Sfoglia**, quindi cercare `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Assicurarsi che il nome del progetto (**Receiver**) sia specificato nella casella **Versione/i **. Fare clic su **Installa** e accettare le condizioni per l'utilizzo.

    ![][13]  

	Visual Studio scarica e installa il [pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) e aggiunge un riferimento al pacchetto con tutte le relative dipendenze.

7. Fare clic con il pulsante destro del mouse sul progetto **Receiver**, scegliere **Aggiungi** e quindi **Classe**. Assegnare alla nuova classe il nome **SimpleEventProcessor** e quindi fare clic su **Aggiungi** per crearla.

	![][15]

8. Aggiungere le istruzioni seguenti all'inizio del file SimpleEventProcessor.cs:

	```
	using Microsoft.ServiceBus.Messaging;
	using System.Diagnostics;
	```

	Sostituire quindi il corpo della classe con il codice seguente:

	```
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

	        //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	        if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
	    }
	}
    ```

	Questa classe verrà chiamata da **EventProcessorHost** per elaborare eventi ricevuti dall'hub eventi. Si noti che la classe `SimpleEventProcessor` usa un cronometro per chiamare periodicamente il metodo checkpoint sul contesto di **EventProcessorHost**. Questo assicura che, se il ricevitore viene riavviato, non perderà più di cinque minuti di lavoro di elaborazione.

9. Nella classe **Program** aggiungere l'istruzione `using` seguente all'inizio del file:

	```
	using Microsoft.ServiceBus.Messaging;
	```

	Sostituire quindi il metodo `Main` nella classe `Program` con il codice seguente, sostituendo il nome dell'hub eventi e la stringa di connessione a livello di spazio dei nomi salvata in precedenza, nonché l'account di archiviazione e la chiave copiata nelle sezioni precedenti.

    ```
	static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
	```

> [AZURE.NOTE] Questa esercitazione usa una singola istanza di [EventProcessorHost][]. Per aumentare la velocità effettiva, è consigliabile eseguire più istanze di [EventProcessorHost][], come illustrato nell'esempio di [elaborazione di eventi scalata orizzontalmente][]. In questi casi, le varie istanze si coordinano automaticamente tra loro per ottenere il bilanciamento del carico relativo agli eventi ricevuti. Se si vuole che ognuno dei vari ricevitori elabori *tutti* gli eventi, è necessario usare il concetto **ConsumerGroup**. Quando si ricevono eventi da più macchine, potrebbe risultare utile specificare nomi per le istanze di [EventProcessorHost][] in base alle macchine (o ai ruoli) in cui sono distribuite. Per altre informazioni su questi argomenti, vedere [Panoramica di Hub eventi di Azure][] e [Guida alla programmazione di Hub eventi][].

<!-- Links -->
[Panoramica di Hub eventi di Azure]: event-hubs-overview.md
[Guida alla programmazione di Hub eventi]: event-hubs-programming-guide.md
[elaborazione di eventi scalata orizzontalmente]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[account di archiviazione di Azure]: ../storage/storage-create-storage-account-classic-portal.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[portale di Azure classico]: http://manage.windowsazure.com

<!-- Images -->  

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp1.png
[15]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp2.png

<!---HONumber=AcomDC_0824_2016-->