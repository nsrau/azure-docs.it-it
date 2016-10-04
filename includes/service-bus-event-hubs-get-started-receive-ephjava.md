## Ricevere messaggi con EventProcessorHost in Java

EventProcessorHost è una classe Java che semplifica la ricezione di eventi da Hub eventi tramite la gestione di checkpoint persistenti e ricezioni parallele da tali hub. Usando EventProcessorHost è possibile suddividere gli eventi tra più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare EventProcessorHost per un ricevitore singolo.

###Creare un account di archiviazione

Per poter usare EventProcessorHost è necessario avere un [account di archiviazione di Azure][]\:

1. Accedere al [portale di Azure classico][] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi dati**, quindi su **Archiviazione** e infine su **Creazione rapida** e digitare un nome per l'account di archiviazione. Selezionare l'area desiderata e quindi fare clic su **Crea account di archiviazione**.

    ![][11]

3. Fare clic sull'account di archiviazione appena creato e quindi su **Gestisci chiavi di accesso**:

    ![][12]

    Copiare la chiave di accesso primaria da usare più avanti in questa esercitazione.

###Creare un progetto Java usando EventProcessorHost

La libreria client Java per Hub eventi è disponibile per l'uso in progetti Maven dal [Repository centrale Maven][Maven Package] ed è possibile farvi riferimento con la seguente dichiarazione di dipendenza all'interno del file di progetto Maven:

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>{VERSION}</version>
</dependency>
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs-eph</artifactId>
	<version>{VERSION}</version>
</dependency>
```
 
Per diversi tipi di ambienti di compilazione, è possibile ottenere in modo esplicito i più recenti file JAR rilasciati dal [Repository centrale Maven][Maven Package] o dal [punto di distribuzione rilascio in GitHub](https://github.com/Azure/azure-event-hubs/releases).

1. Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. La classe verrà chiamata ```ErrorNotificationHandler```.

	``` Java
	import java.util.function.Consumer;
	import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

	public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
	{
		@Override
		public void accept(ExceptionReceivedEventArgs t)
		{
			System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
		}
	}
	```

2. Usare il codice seguente per creare una nuova classe denominata ```EventProcessor```.

	```Java
	import com.microsoft.azure.eventhubs.EventData;
	import com.microsoft.azure.eventprocessorhost.CloseReason;
	import com.microsoft.azure.eventprocessorhost.IEventProcessor;
	import com.microsoft.azure.eventprocessorhost.PartitionContext;

	public class EventProcessor implements IEventProcessor
	{
		private int checkpointBatchingCount = 0;

		@Override
		public void onOpen(PartitionContext context) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
		}

		@Override
		public void onClose(PartitionContext context, CloseReason reason) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
		}
		
		@Override
		public void onError(PartitionContext context, Throwable error)
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
		}

		@Override
		public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
			int messageCount = 0;
			for (EventData data : messages)
			{
				System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
						data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
				messageCount++;
				
				this.checkpointBatchingCount++;
				if ((checkpointBatchingCount % 5) == 0)
				{
					System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
						data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
					context.checkpoint(data);
				}
			}
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
		}
	}
	```

3. Creare una classe finale denominata ```EventProcessorSample``` con il codice seguente.

	```Java
	import com.microsoft.azure.eventprocessorhost.*;
	import com.microsoft.azure.servicebus.ConnectionStringBuilder;
	import com.microsoft.azure.eventhubs.EventData;

	public class EventProcessorSample
	{
		public static void main(String args[])
		{
			final String consumerGroupName = "$Default";
			final String namespaceName = "----ServiceBusNamespaceName-----";
			final String eventHubName = "----EventHubName-----";
			final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
			final String sasKey = "---SharedAccessSignatureKey----";

			final String storageAccountName = "---StorageAccountName----";
			final String storageAccountKey = "---StorageAccountKey----";
			final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
			
			ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
			
			EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
			
			System.out.println("Registering host named " + host.getHostName());
			EventProcessorOptions options = new EventProcessorOptions();
			options.setExceptionNotification(new ErrorNotificationHandler());
			try
			{
				host.registerEventProcessor(EventProcessor.class, options).get();
			}
			catch (Exception e)
			{
				System.out.print("Failure while registering: ");
				if (e instanceof ExecutionException)
				{
					Throwable inner = e.getCause();
					System.out.println(inner.toString());
				}
				else
				{
					System.out.println(e.toString());
				}
			}

			System.out.println("Press enter to stop");
			try
			{
				System.in.read();
				host.unregisterEventProcessor();
				
				System.out.println("Calling forceExecutorShutdown");
				EventProcessorHost.forceExecutorShutdown(120);
			}
			catch(Exception e)
			{
				System.out.println(e.toString());
				e.printStackTrace();
			}
			
			System.out.println("End of sample");
		}
	}
	```

4. Sostituire i campi seguenti con i valori usati durante la creazione dell'Hub eventi e dell'account di archiviazione.

	``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";

	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";

	final String storageAccountName = "---StorageAccountName----"
	final String storageAccountKey = "---StorageAccountKey----";
	```

> [AZURE.NOTE] Questa esercitazione usa una singola istanza di EventProcessorHost. Per aumentare la velocità effettiva è consigliabile eseguire più istanze di EventProcessorHost. In questi casi, le varie istanze si coordinano automaticamente tra loro per ottenere il bilanciamento del carico relativo agli eventi ricevuti. Se si vuole che ognuno dei vari ricevitori elabori *tutti* gli eventi, è necessario usare il concetto **ConsumerGroup**. Quando si ricevono eventi da più macchine, potrebbe risultare utile specificare nomi per le istanze di EventProcessorHost in base alle macchine (o ai ruoli) in cui sono distribuite.

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[account di archiviazione di Azure]: ../storage/storage-create-storage-account.md
[portale di Azure classico]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

<!---HONumber=AcomDC_0928_2016-->