## Elaborazione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Windows che elabora i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi] per consentire a un'applicazione di leggere i messaggi da dispositivo a cloud. Questa esercitazione usa la classe [EventProcessorHost] per elaborare i messaggi in un'app console. Per altre informazioni su come elaborare i messaggi da Hub eventi, è possibile fare riferimento all'esercitazione [Introduzione all'Hub eventi].

La difficoltà maggiore quando si implementa l'archiviazione affidabile dei messaggi di punti dati o si inoltrano messaggi interattivi è che l'elaborazione degli eventi di Hub eventi si basa sul consumer di messaggi per creare checkpoint dell'avanzamento. Per ottenere una velocità effettiva elevata, durante la lettura da Hub eventi è necessario creare checkpoint in batch di grandi dimensioni. In questo modo, se si verifica un errore e viene ripristinato il checkpoint precedente sarà possibile eseguire l'elaborazione duplicati per un numero maggiore di messaggi. Questa esercitazione illustra come sincronizzare le scritture di archiviazione di Azure e le finestre di deduplicazione del bus di servizio con i checkpoint di **EventProcessorHost**.

Per scrivere messaggi in modo affidabile nell'archiviazione di Azure, l'esempio fa uso della funzionalità di commit dei singoli blocchi dei [BLOB in blocchi][Azure Block Blobs]. Il processore di eventi accumula i messaggi nella memoria, fino al momento di eseguire un checkpoint, ad esempio dopo che il buffer di messaggi accumulato raggiunge la dimensione massima per i blocchi pari a 4 MB o al termine della finestra temporale di deduplicazione del bus di servizio. Prima della creazione dei checkpoint, il codice esegue il commit di un nuovo blocco al BLOB.

Il processore di eventi usa gli offset dei messaggi di Hub eventi come ID blocchi. Questo gli consente di eseguire un controllo della deduplicazione prima del commit del nuovo blocco alla risorsa di archiviazione, gestendo un possibile arresto anomalo tra il commit di un blocco e il checkpoint.

> [AZURE.NOTE] Questa esercitazione usa un solo account di archiviazione per scrivere tutti i messaggi recuperati dall'hub IoT. Per stabilire se nella soluzione adottata è necessario usare più account di archiviazione di Azure, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

L'applicazione usa la funzionalità di deduplicazione del bus di servizio per evitare duplicati durante l'elaborazione dei messaggi interattivi. Il dispositivo simulato assegna a ogni messaggio interattivo un **MessageId** univoco per consentire al bus di servizio di fare in modo che, nella finestra temporale di deduplicazione specificata, non vengano mai recapitati ai destinatari due messaggi con lo stesso **MessageId**. Questa deduplicazione, insieme alla semantica di completamento fornita per ogni messaggio dalle code del bus di servizio, semplifica l'elaborazione affidabile dei messaggi interattivi.

Per fare in modo che nessun messaggio venga inviato di nuovo al di fuori della finestra di deduplicazione, il codice sincronizza il meccanismo di creazione di checkpoint di **EventProcessorHost** con la finestra di deduplicazione della coda del bus di servizio. A questo scopo, viene imposto un checkpoint almeno ogni volta che trascorre la finestra temporale di deduplicazione (in questa esercitazione, un'ora).

> [AZURE.NOTE] Questa esercitazione usa una sola coda del bus di servizio partizionata per elaborare tutti i messaggi interattivi recuperati dall'hub IoT. Vedere la [documentazione del bus di servizio] per altre informazioni su come usare le code del bus di servizio per soddisfare le esigenze di scalabilità della soluzione.

### Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio
Per usare la classe [EventProcessorHost], è necessario un account di archiviazione di Azure per abilitare la registrazione delle informazioni sui checkpoint da parte di **EventProcessorHost**. È possibile usare un account di archiviazione esistente o seguire le istruzioni in [Informazioni sugli account di archiviazione di Azure] per crearne uno nuovo. Prendere nota della stringa di connessione dell'account di archiviazione.

Per abilitare l'elaborazione affidabile dei messaggi interattivi, è necessaria anche una coda del bus di servizio. È possibile creare una coda a livello di programmazione con una finestra di deduplicazione di 1 ora, come illustrato in [Come usare le code del bus di servizio][Service Bus Queue], o usare il [portale di Azure], seguendo questa procedura:

1. Fare clic su **NUOVO** nell'angolo inferiore sinistro, quindi scegliere **Servizi app**, **Bus di servizio**, **Coda** e infine **Creazione personalizzata**. Immettere il nome **d2ctutorial**, selezionare un'area, usare uno spazio dei nomi esistente o crearne uno nuovo, quindi nella pagina successiva selezionare **Abilita rilevamento duplicati** e impostare la **Finestra temporale cronologia di rilevamento duplicata** su un'ora. Fare quindi clic sul segno di spunta per salvare la configurazione della coda.

    ![][30]

2. Nell'elenco delle code del bus di servizio, fare clic su **d2ctutorial** e quindi su **Configura**. Creare due criteri di accesso condiviso, uno chiamato **send** con autorizzazioni **Invio** e uno chiamato **listen** con autorizzazioni **Attesa**. Al termine, fare clic su **Salva** nella parte inferiore della pagina.

    ![][31]

3. Fare clic su **Dashboard** in alto, quindi su **Informazioni di connessione** in basso e prendere nota delle due stringhe di connessione.

    ![][32]

### Creare il processore di eventi

1. Nella soluzione corrente di Visual Studio fare clic su **File**, **Aggiungi**, **Nuovo progetto** per creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **ProcessDeviceToCloudMessages**.

    ![][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProcessDeviceToCloudMessages** e quindi scegliere **Gestisci pacchetti NuGet**. Verrà visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e accettare le condizioni per l'utilizzo. Verrà scaricato, installato e aggiunto un riferimento al [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) con tutte le dipendenze.

4. Cercare **Microsoft Azure Service Bus Event Hub - EventProcessorHost**, fare clic su **Installa** e accettare le condizioni per l'utilizzo. Viene scaricato, installato, il [pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con tutte le relative dipendenze, e viene aggiunto un riferimento a tale pacchetto.

5. Fare clic con il pulsante destro del mouse sul progetto **ProcessDeviceToCloudMessages**, fare clic su **Aggiungi** e quindi su **Classe**. Assegnare alla nuova classe il nome **StoreEventProcessor** e quindi fare clic su **OK** per crearla.

6. Aggiungere le istruzioni seguenti all'inizio del file StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Sostituire il corpo della classe con il codice seguente:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La classe **EventProcessorHost** chiama questa classe per elaborare i messaggi da dispositivo a cloud ricevuti dall'hub IoT. Il codice in questa classe implementa la logica per memorizzare in modo affidabile i messaggi in un contenitore BLOB e inoltrare i messaggi interattivi alla coda del bus di servizio. Il metodo **OpenAsync** inizializza la variabile **currentBlockInitOffset** che tiene traccia dell'offset corrente del primo messaggio letto da questo processore di eventi. Tenere presente che ogni processore è responsabile di una singola partizione.
    
    Il metodo **ProcessEventsAsync** riceve un batch di messaggi dall'hub IoT e li elabora inviando messaggi interattivi alla coda del bus di servizio e aggiungendo messaggi di punti dati nel buffer di memoria denominato **toAppend**. Se il buffer di memoria raggiunge il limite di 4 MB per i blocchi o è trascorsa la finestra temporale di deduplicazione del bus di servizio dopo l'ultimo checkpoint (in questa esercitazione, un'ora), viene attivato un checkpoint.

    Il metodo **AppendAndCheckpoint** genera prima un ID blocco per il blocco da aggiungere. Dal momento che l'archiviazione di Azure richiede che tutti gli ID blocco abbiano la stessa lunghezza, il metodo riempie l'offset con zero iniziali: `currentBlockInitOffset.ToString("0000000000000000000000000")`. Quindi, se un blocco con questo ID è già nel BLOB, il metodo lo sovrascrive con il contenuto corrente del buffer.

    > [AZURE.NOTE] Per semplificare il codice, questa esercitazione usa un solo file BLOB per partizione per archiviare i messaggi. Una soluzione vera implementerebbe il rollover dei file, creando file aggiuntivi quando raggiungono una determinata dimensione (il BLOB in blocchi di Azure non può superare i 195 GB) o dopo un determinato intervallo di tempo.

8. All'inizio della classe **Program** aggiungere le istruzioni **using** seguenti:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modificare il metodo **Main** nella classe **Program** come illustrato di seguito, sostituendo la stringa di connessione **iothubowner** dell'hub IoT (dall'[Esercitazione: Introduzione all'hub IoT di Azure]), la stringa di connessione di archiviazione e la stringa di connessione del bus di servizio con le autorizzazioni **Invio** per la coda **d2ctutorial**:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```
    
    > [AZURE.NOTE] Per semplicità, questa esercitazione usa una singola istanza della classe [EventProcessorHost]. Per altre informazioni, vedere [Guida alla programmazione di Hub eventi].

## Ricevere messaggi interattivi
In questa sezione si scriverà un'app console di Windows che riceve i messaggi interattivi dalla coda del bus di servizio. Fare riferimento a [creazione di applicazioni a più livelli con il Bus di servizio][] per ulteriori informazioni su come progettare una soluzione che utilizza il Bus di servizio.

1. Nella soluzione Visual Studio corrente, creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **ProcessD2cInteractiveMessages**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProcessD2CInteractiveMessages** e quindi scegliere **Gestisci pacchetti NuGet**. Verrà visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e accettare le condizioni per l'utilizzo. Verrà quindi scaricato e installato il [bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) e verrà aggiunto un riferimento ad esso.

4. All'inizio del file **Program.cs** aggiungere le istruzioni **using** seguenti:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Aggiungere infine le righe seguenti al metodo **Main**, sostituendo la stringa di connessione con le autorizzazioni **Attesa** per la coda **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

<!-- Links -->
[Informazioni sugli account di archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Introduzione all'Hub eventi]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hub eventi]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guida alla programmazione di Hub eventi]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portale di Azure]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[creazione di applicazioni a più livelli con il Bus di servizio]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Esercitazione: Introduzione all'hub IoT di Azure]: iot-hub-csharp-csharp-getstarted.md
[documentazione del bus di servizio]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0204_2016-->