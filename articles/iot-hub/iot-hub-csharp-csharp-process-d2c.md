<properties
	pageTitle="Elaborare messaggi da dispositivo a cloud dell'hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per conoscere utili modelli di elaborazione dei messaggi da dispositivo a cloud dell'hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/19/2016"
     ms.author="dobett"/>

# Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Altre esercitazioni, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Introduzione all'hub IoT] e illustra due modelli scalabili che è possibile usare per elaborare i messaggi da dispositivo a cloud:

- L'archiviazione affidabile dei messaggi da dispositivo a cloud nell'[Archivio BLOB di Azure]. Questo scenario è molto comune quando si analizzano *percorsi non critici*, in cui i dati di telemetria vengono archiviati nei BLOB per essere usati come input per processi di analisi. Questi processi possono essere gestiti da strumenti quali [Data Factory di Azure] o lo stack [HDInsight (Hadoop)].

- L'elaborazione affidabile di messaggi da dispositivo a cloud *interattivi*. I messaggi da dispositivo a cloud sono detti interattivi quando sono trigger immediati per un set di azioni nel back-end dell'applicazione. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Al contrario, i messaggi di *punto dati* vengono semplicemente inseriti in un motore di analisi. Ad esempio, i dati di telemetria sulla temperatura di un dispositivo che devono essere archiviati per una successiva analisi costituiscono un messaggio di punti dati.

Dal momento che l'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs] per ricevere i messaggi da dispositivo a cloud, questa esercitazione usa un'istanza di [EventProcessorHost]. Questa istanza:

* Archivia in modo affidabile i messaggi di *punti dati* nei BLOB di Azure.
* Inoltra i messaggi *interattivi* da dispositivo a cloud a una [coda del bus di servizio] per l'elaborazione immediata.

Il bus di servizio garantisce un'elaborazione affidabile dei messaggi interattivi, perché fornisce checkpoint per ogni messaggio e la deduplicazione basata su intervalli di tempo.

> [AZURE.NOTE] Un'istanza di **EventProcessorHost** è solo uno dei modi possibili per elaborare i messaggi interattivi. Altre opzioni includono [Azure Service Fabric][lnk-service-fabric] e [Analisi di flusso di Azure][lnk-stream-analytics].

Al termine di questa esercitazione verranno eseguite tre applicazioni console di Windows:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Introduzione all'hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi. Questa applicazione usa il protocollo AMQPS per comunicare con l'hub IoT.
* **ProcessDeviceToCloudMessages**, che usa la classe [EventProcessorHost] per recuperare i messaggi dall'endpoint compatibile con Hub eventi e quindi archivia in modo affidabile i messaggi di punti dati in un BLOB di Azure e inoltra i messaggi interattivi a una coda del bus di servizio.
* **ProcessD2CInteractiveMessages**, che rimuove i messaggi interattivi dalla coda del bus di servizio.

> [AZURE.NOTE] L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni dettagliate su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e, in generale, su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].

Il contenuto di questa esercitazione è direttamente applicabile ad altri modi di utilizzare i messaggi compatibili con Hub eventi, ad esempio i progetti [HDInsight (Hadoop)]. Per altre informazioni, vedere [Guida per gli sviluppatori dell'hub IoT di Azure - Da dispositivo a cloud].

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessaria una conoscenza di base dell'[Archiviazione di Azure] e del [bus di servizio di Azure].


## Inviare messaggi interattivi da un dispositivo simulato

In questa sezione verrà modificata l'applicazione del dispositivo simulato creata nell'esercitazione [Introduzione all'hub IoT di Azure] per inviare messaggi interattivi da dispositivo a cloud all'hub IoT.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Programma**.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Questo metodo è molto simile al metodo **SendDeviceToCloudMessagesAsync** nel progetto **SimulatedDevice**. Le uniche differenze sono l'impostazione della proprietà di sistema **MessageId** e una proprietà utente denominata **messageType**. Il codice assegna un identificatore univoco globale (GUID) alla proprietà **MessageId**. Il bus di servizio può usare il GUID per deduplicare i messaggi ricevuti. L'esempio usa la proprietà **messageType** per distinguere i messaggi interattivi dai messaggi del punto dati. L'applicazione passa queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che il processore di eventi non debba deserializzare il messaggio per eseguirne il routing.

    > [AZURE.NOTE] È importante creare la proprietà **MessageId** da usare per la deduplicazione dei messaggi interattivi nel codice del dispositivo. Comunicazioni di rete intermittenti o altri errori, possono dare luogo a più ritrasmissioni dello stesso messaggio dal dispositivo. È anche possibile usare un ID messaggio semantico, ad esempio un hash dei campi dati del messaggio pertinenti, anziché un GUID.

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling] \(Gestione degli errori temporanei).

## Elaborare i messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Windows che elabora i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi] per consentire a un'applicazione di leggere i messaggi da dispositivo a cloud. Questa esercitazione usa la classe [EventProcessorHost] per elaborare i messaggi in un'app console. Per altre informazioni su come elaborare i messaggi da Hub eventi, vedere l'esercitazione [Introduzione all'Hub eventi].

La difficoltà maggiore quando si implementa l'archiviazione affidabile dei messaggi di punti dati o si inoltrano messaggi interattivi è che l'elaborazione degli eventi di Hub eventi si basa sul consumer di messaggi per creare checkpoint dell'avanzamento. Per ottenere una velocità effettiva elevata, durante la lettura da Hub eventi è necessario creare checkpoint in batch di grandi dimensioni. In questo modo, se si verifica un errore e viene ripristinato il checkpoint precedente, sarà possibile eseguire l'elaborazione duplicati per un numero maggiore di messaggi. Questa esercitazione illustra come sincronizzare le scritture di archiviazione di Azure e le finestre di deduplicazione del bus di servizio con i checkpoint di **EventProcessorHost**.

Per scrivere messaggi in modo affidabile nell'archiviazione di Azure, l'esempio usa la funzionalità di commit dei singoli blocchi dei [BLOB in blocchi][Azure Block Blobs]. Il processore di eventi accumula i messaggi nella memoria, fino al momento di eseguire un checkpoint, ad esempio dopo che il buffer di messaggi accumulato raggiunge la dimensione massima per i blocchi pari a 4 MB o al termine della finestra temporale di deduplicazione del bus di servizio. Prima del checkpoint, il codice esegue quindi il commit di un nuovo blocco al BLOB.

Il processore di eventi usa gli offset dei messaggi di Hub eventi come ID blocchi. Questo gli consente di eseguire un controllo della deduplicazione prima del commit del nuovo blocco alla risorsa di archiviazione, gestendo un possibile arresto anomalo tra il commit di un blocco e il checkpoint.

> [AZURE.NOTE] Questa esercitazione usa un solo account di archiviazione per scrivere tutti i messaggi recuperati dall'hub IoT. Per stabilire se nella soluzione in uso siano necessari più account di archiviazione di Azure, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

L'applicazione usa la funzionalità di deduplicazione del bus di servizio per evitare duplicati durante l'elaborazione dei messaggi interattivi. Il dispositivo simulato contrassegna ogni messaggio interattivo con una proprietà **MessageId** univoca. Ciò consente al bus di servizio di assicurare che, nella finestra temporale di deduplicazione specificata, non verranno mai recapitati ai destinatari due messaggi con lo stesso **MessageId**. Questa deduplicazione, insieme alla semantica di completamento fornita per ogni messaggio dalle code del bus di servizio, semplifica l'elaborazione affidabile dei messaggi interattivi.

Per fare in modo che nessun messaggio venga inviato di nuovo al di fuori della finestra di deduplicazione, il codice sincronizza il meccanismo di creazione di checkpoint di **EventProcessorHost** con la finestra di deduplicazione della coda del bus di servizio. A questo scopo, viene imposto un checkpoint almeno ogni volta che trascorre la finestra temporale di deduplicazione (in questa esercitazione, un'ora).

> [AZURE.NOTE] Questa esercitazione usa una sola coda del bus di servizio partizionata per elaborare tutti i messaggi interattivi recuperati dall'hub IoT. Per ulteriori informazioni su come usare le code del bus di servizio per soddisfare le esigenze di scalabilità della soluzione, vedere la documentazione sul [Bus di servizio di Azura].

### Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio
Per usare la classe [EventProcessorHost], è necessario un account di archiviazione di Azure per abilitare la registrazione delle informazioni sui checkpoint da parte di **EventProcessorHost**. È possibile usare un account di archiviazione esistente o seguire le istruzioni contenute in [Informazioni sugli account di archiviazione di Azure] per crearne uno nuovo. Prendere nota della stringa di connessione dell'account di archiviazione.

> [AZURE.NOTE] Quando si copia e incolla la stringa di connessione all'account di archiviazione, verificare che non siano presenti spazi al suo interno.

Per abilitare l'elaborazione affidabile dei messaggi interattivi, è necessaria anche una coda del bus di servizio. È possibile creare una coda a livello di codice con una finestra di deduplicazione di un'ora, come illustrato in [Introduzione alle code del bus di servizio][Service Bus queue]. In alternativa, è possibile usare il [portale di Azure classico][lnk-classic-portal], attenendosi alla procedura seguente:

1. Fare clic su **Nuovo** nell'angolo inferiore sinistro. Quindi fare clic su **Servizi app** > **Bus di servizio** > **Coda** > **Creazione personalizzata**. Immettere il nome **d2ctutorial**, selezionare un'area e usare uno spazio dei nomi esistente o crearne uno nuovo. Nella pagina successiva selezionare **Abilita rilevamento duplicati** e impostare **Finestra temporale cronologia di rilevamento duplicata** su un'ora. Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione della coda.

    ![Creare una coda nel portale di Azure][30]

2. Nell'elenco delle code del bus di servizio fare clic su **d2ctutorial** e quindi su **Configura**. Creare due criteri di accesso condiviso, uno chiamato **send** con autorizzazioni di **Invio** e uno chiamato **listen** con autorizzazioni di **Ascolto**. Al termine, fare clic su **Salva** nella parte inferiore.

    ![Configurare una coda nel portale di Azure][31]

3. Selezionare**Dashboard** nella parte superiore e quindi fare clic su **Informazioni di connessione** nella parte inferiore. Prendere nota delle due stringhe di connessione.

    ![Dashboard della coda nel portale di Azure][32]

### Creare il processore di eventi

1. Nella soluzione corrente di Visual Studio fare clic su **File** > **Aggiungi** > **Nuovo progetto** per creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **ProcessDeviceToCloudMessages** e fare clic su **OK**.

    ![Nuovo progetto in Visual Studio][10]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProcessDeviceToCloudMessages** e quindi scegliere **Gestisci pacchetti NuGet**. Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet**.

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e quindi accettare le condizioni per l'utilizzo. Verrà scaricato, installato e aggiunto un riferimento al [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) con tutte le dipendenze.

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
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
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

    La classe **EventProcessorHost** chiama questa classe per elaborare i messaggi da dispositivo a cloud ricevuti dall'hub IoT. Il codice in questa classe implementa la logica per memorizzare in modo affidabile i messaggi in un contenitore BLOB e inoltrare i messaggi interattivi alla coda del bus di servizio.

    Il metodo **OpenAsync** inizializza la variabile **currentBlockInitOffset** che tiene traccia dell'offset corrente del primo messaggio letto da questo processore di eventi. Tenere presente che ogni processore è responsabile di una singola partizione.

    Il metodo **ProcessEventsAsync** riceve un batch di messaggi dall'hub IoT e li elabora inviando messaggi interattivi alla coda del bus di servizio e aggiungendo messaggi dei punti dati al buffer di memoria denominato **toAppend**. Se il buffer di memoria raggiunge il limite di 4 MB per i blocchi o è trascorsa la finestra temporale di deduplicazione del bus di servizio dopo l'ultimo checkpoint (in questa esercitazione, un'ora), viene attivato un checkpoint.

    Il metodo **AppendAndCheckpoint** genera prima un ID blocco per il blocco da aggiungere. Dal momento che l'archiviazione di Azure richiede che tutti gli ID blocco abbiano la stessa lunghezza, il metodo riempie l'offset con zero iniziali: `currentBlockInitOffset.ToString("0000000000000000000000000")`. Quindi, se un blocco con questo ID è già nel BLOB, il metodo lo sovrascrive con il contenuto corrente del buffer.

    > [AZURE.NOTE] Per semplificare il codice, questa esercitazione usa un solo file BLOB per partizione per archiviare i messaggi. Una soluzione vera implementerebbe il rollover dei file, creando file aggiuntivi dopo un determinato intervallo di tempo o al raggiungimento di una certa dimensione (il BLOB in blocchi di Azure non può superare i 195 GB).

8. All'inizio della classe **Program** aggiungere l'istruzione **using** seguente:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modificare il metodo **Main** nella classe **Program** come mostrato di seguito. Sostituire la stringa di connessione **iothubowner** all'hub IoT, dall'esercitazione [Introduzione all'hub IoT di Azure], la stringa di connessione di archiviazione e la stringa di connessione del bus di servizio con le autorizzazioni **Invio** per la coda denominata **d2ctutorial**:

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
In questa sezione si scriverà un'app console di Windows che riceve i messaggi interattivi dalla coda del bus di servizio. Per altre informazioni su come progettare una soluzione che usi il bus di servizio, vedere [Applicazione .NET multilivello che usa code del bus di servizio][].

1. Nella soluzione di Visual Studio corrente creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Assegnare al progetto il nome **ProcessD2CInteractiveMessages**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProcessD2CInteractiveMessages** e quindi scegliere **Gestisci pacchetti NuGet**. Viene visualizzata la finestra **Gestione pacchetti NuGet**.

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e accettare le condizioni per l'utilizzo. Verrà quindi scaricato e installato il [bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) e verrà aggiunto un riferimento ad esso.

4. All'inizio del file **Program.cs** aggiungere le istruzioni **using** seguenti:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Aggiungere infine le righe seguenti al metodo **Main**. Sostituire la stringa di connessione con autorizzazioni **Ascolto** per la coda denominata **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

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

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Avvia** per i progetti **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Premere **F5** per avviare le tre applicazioni console. L'applicazione **ProcessD2CInteractiveMessages** deve elaborare tutti i messaggi interattivi inviati dall'applicazione **SimulatedDevice**.

  ![Tre applicazioni console][50]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel file BLOB, potrebbe essere necessario ridurre la costante **MAX\_BLOCK\_SIZE** nella classe **StoreEventProcessor** a un valore inferiore, ad esempio **1024**. Questo perché raggiungere il limite di dimensione del blocco con i dati inviati dal dispositivo simulato può richiedere tempo. Con una dimensione del blocco ridotta, la creazione e l'aggiornamento del BLOB richiedono meno tempo. Tuttavia, una dimensione del blocco maggiore rende l'applicazione più scalabile.

## Passaggi successivi

Questa esercitazione ha illustrato come elaborare in modo affidabile i messaggi di punti dati e interattivi da dispositivo a cloud usando la classe [EventProcessorHost].

L'esercitazione [Come inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d] descrive le modalità di invio dei messaggi ai dispositivi dal proprio back-end.

Per avere degli esempi di soluzioni complete che utilizzano l'hub IoT, vedere la [Azure IoT Suite][lnk-suite].

Per ulteriori informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per gli sviluppatori dell'hub IoT].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Archivio BLOB di Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Data Factory di Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[coda del bus di servizio]: ../service-bus/service-bus-dotnet-get-started-with-queues.md

[Guida per gli sviluppatori dell'hub IoT di Azure - Da dispositivo a cloud]: iot-hub-devguide.md#d2c

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio di Azura]: https://azure.microsoft.com/documentation/services/service-bus/
[bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per gli sviluppatori dell'hub IoT]: iot-hub-devguide.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Introduzione all'hub IoT di Azure]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Informazioni sugli account di archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introduzione all'Hub eventi]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hub eventi]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guida alla programmazione di Hub eventi]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Applicazione .NET multilivello che usa code del bus di servizio]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0720_2016-->