## Elaborazione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Windows che elabora i messaggi da dispositivo a cloud dall'hub IoT. L'hub IOT espone un endpoint compatibile con [Hub eventi][Event Hubs Overview] per leggere i messaggi da dispositivo a cloud. Questa esercitazione usa [EventProcessorHost] per elaborare i messaggi in un'app console. Per altre informazioni su come elaborare i messaggi da Hub eventi, è possibile fare riferimento all'esercitazione [Introduzione all'Hub eventi].

La difficoltà maggiore quando si implementa l'Archiviazione affidabile dei messaggi dei punti dati o si inoltrano quelli interattivi è che l'elaborazione degli eventi di Hub eventi si basa sul consumer di messaggi per creare punti di controllo per l'avanzamento. Inoltre, per raggiungere una velocità effettiva elevata, durante la lettura da Hub eventi, i punti di controllo devono essere eseguiti in batch di grandi dimensioni, creando la possibilità di un'elaborazione duplicata per un gran numero di messaggi. In questa esercitazione verrà illustrato come sincronizzare le scritture di archiviazione di Azure e le finestre di deduplicazione del bus di servizio con i punti di controllo dell'host processore di eventi.

Per scrivere messaggi in modo affidabile nella risorsa di archiviazione di Azure, verrà usata la funzionalità [BLOB in blocchi][Azure Block Blobs] di commit dei singoli blocchi. Il processore di eventi accumula i messaggi nella memoria, fino al momento di eseguire un punto di controllo (ad esempio, dopo che il buffer accumulato è maggiore della dimensione massima per i blocchi pari a 4 MB o dopo che sono trascorse le finestre temporali di deduplicazione del bus di servizio). Prima della creazione dei punti di controllo, viene quindi eseguito il commit di un nuovo blocco al BLOB.

Il processore di eventi usa gli offset dei messaggi di Hub eventi come ID blocchi. Ciò consente di eseguire un controllo della deduplicazione prima del commit del nuovo blocco alla risorsa di archiviazione, gestendo un possibile arresto anomalo del sistema tra un blocco di cui è stato eseguito il commit e il punto di controllo.

> [AZURE.NOTE]Questa esercitazione usa un solo account di archiviazione per scrivere tutti i messaggi recuperati dall'hub IoT. Per stabilire se nella soluzione adottata sono necessari più account di archiviazione di Azure, vedere [Linee guida per la scalabilità in Archiviazione di Azure].

Per evitare duplicati durante l'elaborazione dei messaggi interattivi, verrà illustrata la funzionalità di deduplicazione del bus di servizio. Assegnando a ogni messaggio interattivo un elemento `MessageId` univoco, il bus di servizio può assicurare che, nella finestra temporale di deduplicazione specificata, non verranno mai recapitati ai destinatari due messaggi con lo stesso `MessageId`. Questa deduplicazione, insieme alla semantica di completamento fornita per ogni messaggio dalle code del bus di servizio, semplifica considerevolmente l'elaborazione affidabile dei messaggi interattivi.

Per assicurarsi che nessun messaggio venga inviato di nuovo al di fuori della finestra di deduplicazione, il meccanismo di creazione dei punti di controllo dell'host processore di eventi viene sincronizzato con la finestra di deduplicazione delle code del bus di servizio. A questo scopo, viene imposto un punto di controllo almeno ogni volta che trascorre la finestra temporale (in questa esercitazione, 1 ora).

> [AZURE.NOTE]Questa esercitazione usa una sola coda del bus di servizio partizionata per elaborare tutti i messaggi interattivi recuperati dall'hub IoT. In base alle esigenze della soluzione, vedere la [documentazione del bus di servizio] per altre informazioni su come usare le code del bus di servizio.

### Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio
Per poter usare [EventProcessorHost], è necessario un account di archiviazione di Azure. È possibile usarne uno esistente o seguire le istruzioni in [Informazioni sull'archiviazione di Azure] per crearne uno nuovo. Prendere nota della stringa di connessione dell'account di archiviazione.

Per abilitare l'elaborazione affidabile dei messaggi interattivi, sarà necessaria anche una coda del bus di servizio. È possibile creare una coda a livello di programmazione con una finestra di deduplicazione di 1 ora, come illustrato in [Come usare le code del bus di servizio][Service Bus Queue], o usare il [portale di gestione di Azure], seguendo questi passaggi:

1. Fare clic su **NUOVO** in basso a sinistra, quindi su **Servizi app**, su **Bus di servizio**, su **Coda** e infine su **Personalizzata**, selezionare il nome **d2ctutorial**, quindi selezionare una finestra di deduplicazione di 1 ora.

    ![][30]

2. Fare clic sul nome della coda (**d2ctutorial**), quindi su **Configura** e creare due criteri di accesso condiviso, uno chiamato **send** con le autorizzazioni **Invio** e uno chiamato **listen** con le autorizzazioni **Attesa**. Al termine, fare clic su **Salva** nella parte inferiore della pagina.

    ![][31]

3. Fare clic su **Dashboard** in alto, quindi su **Informazioni di connessione** in basso e prendere nota delle due stringhe di connessione.

    ![][32]

### Creare il processore di eventi

1. Nella soluzione corrente di Visual Studio fare clic su **File -> Aggiungi -> Progetto** per creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **ProcessDeviceToCloudMessages**.

    ![][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.

    Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

3. Cercare `WindowsAzure.ServiceBus`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà scaricato, installato e aggiunto un riferimento al [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) con tutte le dipendenze.

4. Cercare `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà scaricato, installato e aggiunto un riferimento al [pacchetto NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) con tutte le dipendenze.

5. Fare clic con il pulsante destro del mouse sul progetto **ProcessDeviceToCloudMessages**, fare clic su **Aggiungi** e quindi su **Classe**. Assegnare alla nuova classe il nome **StoreEventProcessor** e quindi fare clic su **OK** per crearla.

6. Aggiungere le istruzioni seguenti all'inizio del file SimpleEventProcessor.cs:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Sostituire quindi il corpo della classe con il codice seguente:

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
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

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

    Questa classe verrà chiamata da **EventProcessorHost** per elaborare i messaggi da dispositivo a cloud ricevuti dall'hub IoT e implementa la logica principale per l'archiviazione affidabile dei messaggi in un contenitore BLOB e per l'inoltro dei messaggi interattivi alla coda del bus di servizio. Il metodo `OpenAsync()` inizializza la variabile `currentBlockInitOffset` che tiene traccia dell'offset corrente del primo messaggio letto da questo processore di eventi. Si tenga presente che ogni processore è responsabile di una sola partizione.

    Il metodo `ProcessEventsAsync()` riceve un batch di messaggi dall'hub IoT, che vengono elaborati nel modo seguente. I messaggi interattivi vengono inviati alla coda del bus di servizio, mentre i messaggi dei punti dati vengono aggiunti al buffer di memoria `toAppend`. Se il buffer di memoria raggiunge il limite per i blocchi (4 MB) o è trascorsa la finestra temporale di deduplicazione del bus di servizio dopo l'ultimo punto di controllo (in questa esercitazione, 1 ora), viene attivato un punto di controllo.

    Il metodo `AppendAndCheckpoint()` genera prima un ID blocco per il blocco da aggiungere. Poiché l'archiviazione di Azure richiede che tutti gli ID blocco abbiano la stessa lunghezza, l'offset viene riempito con zero iniziali (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Quindi, se un blocco con questo ID è già nel BLOB, il metodo lo sovrascrive con quello corrente.

> [AZURE.NOTE]Per semplificare il codice, questa esercitazione usa un solo file BLOB per partizione per archiviare i messaggi. Una soluzione vera implementerebbe il rollover dei file, creando altri file quando raggiungono una determinata dimensione (il BLOB di Azure non può superare i 195 GB) o dopo un determinato intervallo di tempo (ad esempio, `fileName_{partitionId}_{date-time}`).

7. All'inizio della classe **Program** aggiungere le istruzioni `using` seguenti:

        using Microsoft.ServiceBus.Messaging;

    Modificare quindi il metodo **Main** per la classe **Program** come illustrato di seguito, sostituendo la stringa di connessione **iothubowner** dell'hub IoT (dall'esercitazione [Introduzione all'hub IoT]), la stringa di connessione di archiviazione e la stringa di connessione del bus di servizio con le autorizzazioni **Invio** per la coda **d2ctutorial**:

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

> [AZURE.NOTE]Per semplicità, questa esercitazione usa una singola istanza di [EventProcessorHost]. Per altre informazioni sull'elaborazione di messaggi da dispositivo a cloud, vedere [Guida alla programmazione di Hub eventi] e l'esercitazione [Elaborare messaggi da dispositivo a cloud].

## Ricevere messaggi interattivi
In questa sezione si scriverà un'app console di Windows che riceve messaggi interattivi dalla coda del bus di servizio. Per altre informazioni sull'architettura di una soluzione che usa il bus di servizio, vedere [Compilare applicazioni a più livelli con il bus di servizio].

1. Nella soluzione Visual Studio corrente, creare un nuovo progetto di app desktop di Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **ProcessD2cInteractiveMessages**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

    Verrà visualizzata la finestra Gestisci pacchetti NuGet.

3. Cercare `WindowsAzure.Service Bus`, fare clic su **Installa** e accettare le condizioni per l'uso.

    Verrà scaricato, installato e aggiunto un riferimento al [bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) con tutte le dipendenze.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs**:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Aggiungere infine le righe seguenti al metodo **Main**, sostituendo la stringa di connessione con le autorizzazioni **Attesa** per la coda **d2ctutorial**:

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

<!-- Links -->

[Informazioni sull'archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introduzione all'Hub eventi]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Linee guida per la scalabilità in Archiviazione di Azure]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[documentazione del bus di servizio]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Guida alla programmazione di Hub eventi]: ../event-hubs/event-hubs-programming-guide.md

[Azure Preview Portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[portale di gestione di Azure]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=Oct15_HO1-->