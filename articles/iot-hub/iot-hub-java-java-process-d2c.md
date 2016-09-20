<properties
	pageTitle="Elaborare messaggi da dispositivo a cloud dell'hub IoT (Java) | Microsoft Azure"
	description="Seguire questa esercitazione Java per conoscere utili modelli di elaborazione dei messaggi da dispositivo a cloud dell'hub IoT."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT usando Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Altre esercitazioni, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Introduzione all'hub IoT] e illustra due modelli scalabili che è possibile usare per elaborare i messaggi da dispositivo a cloud:

- L'archiviazione affidabile dei messaggi da dispositivo a cloud nell'[Archivio BLOB di Azure]. Questo scenario si osserva comunemente quando si analizzano *percorsi non critici*, in cui i dati di telemetria vengono archiviati nei BLOB per essere usati come input per processi di analisi. Questi processi possono essere gestiti da strumenti quali [Data Factory di Azure] o lo stack [HDInsight (Hadoop)].

- L'elaborazione affidabile di messaggi da dispositivo a cloud *interattivi*. I messaggi da dispositivo a cloud sono detti interattivi quando sono trigger immediati per un set di azioni nel back-end dell'applicazione. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Al contrario, i messaggi di *punto dati* vengono semplicemente inseriti in un motore di analisi. Ad esempio, i dati di telemetria sulla temperatura di un dispositivo che devono essere archiviati per una successiva analisi costituiscono un messaggio di punti dati.

Dal momento che l'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs] per ricevere i messaggi da dispositivo a cloud, questa esercitazione usa un'istanza di [EventProcessorHost]. Questa istanza:

* Archivia in modo affidabile i messaggi di *punti dati* nei BLOB di Azure.
* Inoltra i messaggi *interattivi* da dispositivo a cloud a una [coda del bus di servizio] per l'elaborazione immediata.

Il bus di servizio garantisce un'elaborazione affidabile dei messaggi interattivi, perché fornisce checkpoint per ogni messaggio e la deduplicazione basata su intervalli di tempo.

> [AZURE.NOTE] Un'istanza di **EventProcessorHost** è solo uno dei modi possibili per elaborare i messaggi interattivi. Altre opzioni includono [Azure Service Fabric][lnk-service-fabric] e [Analisi di flusso di Azure][lnk-stream-analytics].

Al termine di questa esercitazione, vengono eseguite tre app console Java:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Introduzione all'hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi. Questa applicazione usa il protocollo AMQPS per comunicare con l'hub IoT.
* **process-d2c-messages** usa la classe [EventProcessorHost] per recuperare i messaggi dall'endpoint compatibile con Hub eventi. e quindi archivia in modo affidabile i messaggi di punti dati in un BLOB di Azure e inoltra i messaggi interattivi a una coda del bus di servizio.
* **process-interactive-messages** che rimuove i messaggi interattivi dalla coda del bus di servizio.

> [AZURE.NOTE] L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].

Il contenuto di questa esercitazione è direttamente applicabile ad altri modi di utilizzare i messaggi compatibili con Hub eventi, ad esempio i progetti [HDInsight (Hadoop)]. Per altre informazioni, vedere [Guida per gli sviluppatori dell'hub IoT di Azure - Da dispositivo a cloud].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Una versione di lavoro completa dell'esercitazione di [introduzione all'hub IoT].

+ Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.

+ Maven 3. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Maven per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. <br/>Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessaria una conoscenza di base dell'[Archiviazione di Azure] e del [bus di servizio di Azure].


## Inviare messaggi interattivi da un dispositivo simulato

In questa sezione viene modificata l'applicazione del dispositivo simulato creata nell'esercitazione di [introduzione all'hub IoT] per inviare messaggi interattivi da dispositivo a cloud all'hub IoT.

1. Usare un editor di testo per aprire il file simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java. Questo file contiene il codice dell'app **simulated-device** creata durante l'esercitazione di [introduzione all'hub IoT].

2. Aggiungere la classe annidata seguente alla classe **App**:

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Questa classe è simile alla classe **MessageSender** del progetto **simulated-device**. Le uniche differenze sono l'impostazione della proprietà di sistema **MessageId** e una proprietà personalizzata denominata **messageType**. Il codice assegna un identificatore univoco universale (UUID) alla proprietà **MessageId**. Il bus di servizio può usare l'UUID per deduplicare i messaggi ricevuti. L'esempio usa la proprietà **messageType** per distinguere i messaggi interattivi dai messaggi del punto dati. L'applicazione passa queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che il processore di eventi non debba deserializzare il messaggio per eseguirne il routing.

    > [AZURE.NOTE] È importante creare la proprietà **MessageId** da usare per la deduplicazione dei messaggi interattivi nel codice del dispositivo. Comunicazioni di rete intermittenti o altri errori, possono dare luogo a più ritrasmissioni dello stesso messaggio dal dispositivo. È anche possibile usare un ID messaggio semantico, ad esempio un hash dei campi dati del messaggio pertinenti, anziché un UUID.

3. Modificare il metodo **main** per consentire l'invio di messaggi interattivi e di punti dati, come illustrato nel frammento di codice seguente:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Salvare e chiudere il file simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling] \(Gestione degli errori temporanei).

5. Per compilare l'applicazione **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```
    mvn clean package -DskipTests
    ```

## Elaborare i messaggi da dispositivo a cloud

In questa sezione si crea un'app console di Java che elabora i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi] per consentire a un'applicazione di leggere i messaggi da dispositivo a cloud. Questa esercitazione usa la classe [EventProcessorHost] per elaborare i messaggi in un'app console. Per altre informazioni su come elaborare i messaggi da Hub eventi, vedere l'esercitazione [Introduzione all'Hub eventi].

Quando si implementa l'archiviazione affidabile dei messaggi di punti dati o si inoltrano messaggi interattivi, la difficoltà maggiore riguarda il fatto che l'elaborazione degli eventi fa affidamento sulla creazione di checkpoint dell'avanzamento da parte del consumer di messaggi. Inoltre, per ottenere una velocità effettiva elevata, durante la lettura da Hub eventi è necessario creare checkpoint in batch di grandi dimensioni. In questo modo, se si verifica un errore e viene ripristinato il checkpoint precedente, sarà possibile eseguire l'elaborazione duplicati per un numero maggiore di messaggi. Questa esercitazione illustra come sincronizzare le scritture di archiviazione di Azure e le finestre di deduplicazione del bus di servizio con i checkpoint **EventProcessorHost**.

Per scrivere messaggi in modo affidabile nell'archiviazione di Azure, l'esempio usa la funzionalità di commit dei singoli blocchi dei [BLOB in blocchi][Azure Block Blobs]. Il processore di eventi accumula i messaggi in memoria fino al momento di fornire un punto di arresto. Ad esempio, dopo che il buffer dei messaggi raggiunge un totale di 4 MB di dimensioni massime del blocco o dopo che scade l'intervallo del tempo di deduplicazione del bus di servizio. Prima del checkpoint, il codice esegue quindi il commit di un nuovo blocco al BLOB.

Il processore di eventi usa gli offset dei messaggi di Hub eventi come ID blocchi. Questo meccanismo consente al processore di eventi di eseguire un controllo della deduplicazione prima del commit del nuovo blocco alla risorsa di archiviazione, gestendo un possibile arresto anomalo tra il commit di un blocco e il checkpoint.

> [AZURE.NOTE] Questa esercitazione usa un solo account di archiviazione per scrivere tutti i messaggi recuperati dall'hub IoT. Per stabilire se nella soluzione in uso siano necessari più account di archiviazione di Azure, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

L'applicazione usa la funzionalità di deduplicazione del bus di servizio per evitare duplicati durante l'elaborazione dei messaggi interattivi. Il dispositivo simulato contrassegna ogni messaggio interattivo con una proprietà **MessageId** univoca. Ciò consente al bus di servizio di assicurare che, nella finestra temporale di deduplicazione specificata, non verranno mai recapitati ai destinatari due messaggi con lo stesso **MessageId**. Questa deduplicazione, insieme alla semantica di completamento fornita per ogni messaggio dalle code del bus di servizio, semplifica l'elaborazione affidabile dei messaggi interattivi.

Per fare in modo che nessun messaggio venga inviato di nuovo al di fuori della finestra di deduplicazione, il codice sincronizza il meccanismo di creazione di checkpoint di **EventProcessorHost** con la finestra di deduplicazione della coda del bus di servizio. Per eseguire la sincronizzazione, viene imposto un checkpoint almeno ogni volta che trascorre la finestra temporale di deduplicazione (in questa esercitazione, un'ora).

> [AZURE.NOTE] Questa esercitazione usa una sola coda del bus di servizio partizionata per elaborare tutti i messaggi interattivi recuperati dall'hub IoT. Per ulteriori informazioni su come usare le code del bus di servizio per soddisfare le esigenze di scalabilità della soluzione, vedere la documentazione sul [Bus di servizio di Azura].

### Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio

Per usare la classe [EventProcessorHost], è necessario un account di archiviazione di Azure per abilitare la registrazione delle informazioni sui checkpoint da parte di **EventProcessorHost**. È possibile usare un account di archiviazione esistente o seguire le istruzioni contenute in [Informazioni sugli account di archiviazione di Azure] per crearne uno nuovo. Prendere nota della stringa di connessione dell'account di archiviazione.

> [AZURE.NOTE] Quando si copia e incolla la stringa di connessione all'account di archiviazione, verificare che non siano presenti spazi al suo interno.

Per abilitare l'elaborazione affidabile dei messaggi interattivi, è necessaria anche una coda del bus di servizio. È possibile creare una coda a livello di codice con una finestra di deduplicazione di un'ora, come illustrato in [Introduzione alle code del bus di servizio][Service Bus queue]. In alternativa, è possibile usare il [portale di Azure classico][lnk-classic-portal], attenendosi alla procedura seguente:

1. Fare clic su **Nuovo** nell'angolo inferiore sinistro. Quindi fare clic su **Servizi app** > **Bus di servizio** > **Coda** > **Creazione personalizzata**. Immettere il nome **d2ctutorial**, selezionare un'area e usare uno spazio dei nomi esistente o crearne uno nuovo. Prendere nota del nome dello spazio dei nomi, che servirà più avanti nel corso dell'esercitazione. Nella pagina successiva selezionare **Abilita rilevamento duplicati** e impostare **Finestra temporale cronologia di rilevamento duplicata** su un'ora. Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione della coda.

    ![Creare una coda nel portale di Azure][30]

2. Nell'elenco delle code del bus di servizio fare clic su **d2ctutorial** e quindi su **Configura**. Creare due criteri di accesso condiviso, uno chiamato **send** con autorizzazioni di **Invio** e uno chiamato **listen** con autorizzazioni di **Ascolto**. Prendere nota dei valori della **chiave primaria** di entrambi i criteri, che serviranno più avanti nel corso dell'esercitazione. Al termine, fare clic su **Salva** nella parte inferiore.

    ![Configurare una coda nel portale di Azure][31]

### Creare il processore di eventi

In questa sezione viene creata un'applicazione Java per elaborare i messaggi dall'endpoint compatibile con gli hub eventi.

La prima attività consiste nell'aggiungere un progetto Maven denominato **process-d2c-messages** che riceve messaggi da dispositivo a cloud dall'endpoint compatibile con gli hub eventi IoT e li indirizza ad altri servizi back-end.

1. Nella cartella iot-java-get-started creata nell'esercitazione di [introduzione all'hub IoT] creare un progetto Maven denominato **process-d2c-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella process-d2c-messages.

3. In un editor di testo aprire il file pom.xml nella cartella process-d2c-messages e aggiungere le dipendenze seguenti al nodo **dependencies**. Queste dipendenze consentono di usare i pacchetti azure-eventhubs, azure-eventhubs-eph e azure-servicebus nell'applicazione per interagire con l'hub IoT e la coda del bus di servizio:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

L'attività successiva consiste nell'aggiungere al progetto una classe **ErrorNotificationHandler**.

1. Usare un editor di testo per creare un file process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java. Aggiungere al file il codice seguente per visualizzare i messaggi di errore dell'istanza di **EventProcesssorHost**:

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Salvare e chiudere il file ErrorNotificationHandler.java.

A questo punto è possibile aggiungere una classe che implementa l'interfaccia **IEventProcessor**. La classe **EventProcessorHost** chiama questa classe per elaborare i messaggi da dispositivo a cloud ricevuti dall'hub IoT. Il codice in questa classe implementa la logica per memorizzare in modo affidabile i messaggi in un contenitore BLOB e inoltrare i messaggi interattivi alla coda del bus di servizio.

Il metodo **onEvents** imposta la variabile **latestEventData** per tenere traccia del numero di sequenza e offset dell'ultimo messaggio letto da questo processore di eventi. Tenere presente che ogni processore è responsabile di una singola partizione. Il metodo **onEvents** riceve quindi un batch di messaggi dall'hub IoT e li elabora inviando messaggi interattivi alla coda del bus di servizio e aggiungendo messaggi dei punti dati al buffer di memoria **toAppend**. Se il buffer di memoria raggiunge il limite di 4 MB per i blocchi o se è trascorsa la finestra temporale di deduplicazione (in questa esercitazione, un'ora dopo l'ultimo checkpoint), viene attivato un checkpoint.

Il metodo **AppendAndCheckpoint** genera prima un **blockId** per il blocco da aggiungere al BLOB. Dal momento che l'archiviazione di Azure richiede che tutti gli ID blocco abbiano la stessa lunghezza, il metodo riempie l'offset con zero iniziali. Quindi, se un blocco con questo ID è già nel BLOB, il metodo lo sovrascrive con il contenuto del buffer corrente.

> [AZURE.NOTE] Per semplificare il codice, questa esercitazione usa un solo file BLOB per partizione per archiviare i messaggi. Una soluzione vera implementerebbe il rollover dei file, creando file aggiuntivi dopo un determinato intervallo di tempo o al raggiungimento di una certa dimensione. Tenere presente che il BLOB in blocchi di Azure non può superare i 195 GB.

L'attività successiva consiste nell'implementare l'interfaccia **IEventProcessor**:

1. Usare un editor di testo per creare un file process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java.

2. Aggiungere le seguenti definizioni di importazioni e classi nel file EventProcessor.java. La classe **EventProcessor** implementa l'interfaccia **IEventProcessor** che definisce il comportamento del client Hub eventi:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Aggiungere i metodi seguenti alla classe **EventProcessor** per implementare l'interfaccia **IEventProcessor**:

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Aggiungere le variabili a livello di classe seguenti alla classe **EventProcessor**:

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Aggiungere un metodo **AppendAndCheckPoint** con la firma seguente alla classe **EventProcessor**:

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Aggiungere il codice seguente al metodo **AppendAndCheckPoint** per recuperare il numero attuale di messaggi sequenza e offset nella partizione:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Nel metodo **AppendAndCheckPoint** usare il valore di offset corrente per creare un'istanza **BlockEntry** per il blocco successivo da salvare nel BLOB:

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Nel metodo **AppendAndCheckPoint** caricare l'ultimo set di messaggi del BLOB in blocchi e recuperare l'elenco di blocchi corrente:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Nel metodo **AppendAndCheckPoint** creare il blocco iniziale in un nuovo BLOB oppure aggiungere il blocco al BLOB esistente:

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Infine nel metodo **AppendAndCheckPoint** creare un checkpoint sulla partizione e prepararsi al salvataggio del blocco di messaggi successivo:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Nel metodo **onEvents** aggiungere il codice seguente per ricevere messaggi dall'endpoint dell'hub IoT e inoltrare i messaggi interattivi alla coda del bus di servizio. Chiamare quindi il metodo **AppendAndCheckPoint** quando il blocco è pieno o il timeout scade:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Infine, nel metodo **onEvents** aggiungere una clausola "else if" per chiamare il metodo **AppendAndCheckPoint** se il timeout scade mentre non sono presenti messaggi provenienti dall'hub IoT:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Salvare le modifiche nel file EventProcessor.java.

L'ultima attività del progetto **process-d2c-messages** consiste nell'aggiungere codice al metodo **main** che crea un'istanza **EventProcessorHost**.

1. Usare un editor di testo per aprire il file process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Aggiungere la variabili a livello di classe seguente alla classe **App**. Sostituire **{stringadiconnessioneaccountdiarchiviazione}** con la stringa di connessione all'account di archiviazione di Azure precedentemente annotata nella sezione [Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Aggiungere alla classe **App** le seguenti variabili a livello di classe e sostituire **{spaziodeinomidelbusdiservizio}** con lo spazio dei nomi del bus di servizio e **{chiavesenddelbusdiservizio}** con la chiave **send** della coda. Con i valori della chiave **listen** e lo spazio dei nomi annotati nella precedente sezione [Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Aggiungere le variabili a livello di classe seguenti alla classe **App**. Sostituire **{endpointcompatibilehubeventi}** con il nome dell'endpoint compatibile con l'hub eventi. Il nome dell'endpoint è simile a **ihs... spazio dei nomi**, pertanto è necessario rimuovere il prefisso **sb://** e il suffisso **.servicebus.windows.net/**. Sostituire **{nomecompatibileconhubeventi}** con il nome compatibile con l'hub eventi. Sostituire **{chiavehubiot}** con la chiave **proprietariohubiot**. Prendere nota di questi valori nella sezione sulla [creazione di un hub IoT][lnk-create-an-iot-hub] dell'esercitazione *Introduzione all'hub IoT di Azure per Java*:

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modificare la firma del metodo **main** come segue:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Aggiungere il codice seguente al metodo **main** per ottenere un riferimento al contenitore BLOB che archivia i messaggi:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Aggiungere il codice seguente al metodo **main** per ottenere un riferimento al servizio Bus di servizio:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. Nel metodo **main** configurare e creare un'istanza **EventProcessorHost**. l'opzione **setInvokeProcessorAfterReceiveTimeout** assicura che l'istanza **EventProcessorHost** richiami il metodo **onEvents** nell'interfaccia **IEventProcessor** anche in assenza di messaggi da elaborare. Il metodo **onEvents** richiama quindi il metodo **AppendAndCheckPoint** a ogni scadere del timeout.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Nel metodo **main** registrare l'implementazione di **IEventProcessor** con l'istanza **EventProcessorHost**:

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Infine, aggiungere la logica al metodo **main** per chiudere l'istanza **EventProcessorHost**:

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Salvare e chiudere il file process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

13. Per compilare l'applicazione **process-d2c-messages** con Maven, al prompt dei comandi nella cartella process-d2c-messages eseguire questo comando:

    ```
    mvn clean package -DskipTests
    ```

## Ricevere messaggi interattivi

In questa sezione si scrive un'app console di Java che riceve i messaggi interattivi dalla coda del bus di servizio.

La prima attività consiste nell'aggiungere un progetto Maven denominato **process-interactive-messages** che riceve i messaggi inviati nella coda del bus di servizio dalle istanze **EventProcessor**.

1. Nella cartella iot-java-get-started creata nell'esercitazione di [introduzione all'hub IoT] creare un progetto Maven denominato **process-interactive-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella process-interactive-messages.

3. In un editor di testo aprire il file pom.xml nella cartella process-interactive-messages e aggiungere la dipendenza seguente al nodo **dependencies**. Questa dipendenza consente di usare il pacchetto azure-servicebus nell'applicazione per interagire con la coda del bus di servizio:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

L'attività successiva consiste nell'aggiungere codice per recuperare i messaggi dalla coda del bus di servizio.

1. Usare un editor di testo per aprire il file process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Aggiungere le istruzioni `import` seguenti al file:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Aggiungere alla classe **App** le seguenti variabili a livello di classe e sostituire **{spaziodeinomidelbusdiservizio}** con lo spazio dei nomi del bus di servizio e **{chiavelistendelbusdiservizio}** con la chiave **listen** della coda. Con i valori della chiave **listen** e lo spazio dei nomi annotati nella precedente sezione [Effettuare il provisioning di un account di archiviazione di Azure e di una coda del bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Aggiungere la seguente classe nidificata alla classe **App** per ricevere i messaggi dalla coda:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modificare la firma del metodo **main** come segue:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Nel metodo **main** aggiungere il codice seguente per avviare l'ascolto dei nuovi messaggi:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Salvare e chiudere il file process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

8. Per compilare l'applicazione **process-interactive-messages** con Maven, al prompt dei comandi nella cartella process-interactive-messages eseguire questo comando:

    ```
    mvn clean package -DskipTests
    ```

## Eseguire le applicazioni

A questo punto è possibile eseguire le tre applicazioni.

1.	Per eseguire l'applicazione **process-interactive-messages**, in un prompt dei comandi o nella shell passare alla cartella process-interactive-messages ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire process-interactive-messages][processinteractive]

2.	Per eseguire l'applicazione **process-d2c-messages**, in un prompt dei comandi o nella shell passare alla cartella process-d2c-messages ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire process-d2c-messages][processd2c]

3.	Per eseguire l'applicazione **simulated-device**, in un prompt dei comandi o nella shell passare alla cartella simulated-device ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire simulated-device][simulateddevice]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel file BLOB, potrebbe essere necessario ridurre la costante **MAX\_BLOCK\_SIZE** nella classe **StoreEventProcessor** a un valore inferiore, ad esempio **1024**. Questa modifica è utile perché raggiungere il limite di dimensione del blocco con i dati inviati dal dispositivo simulato può richiedere tempo. Con una dimensione del blocco ridotta, la creazione e l'aggiornamento del BLOB richiedono meno tempo. Tuttavia, una dimensione del blocco maggiore rende l'applicazione più scalabile.

## Passaggi successivi

Questa esercitazione ha illustrato come elaborare in modo affidabile i messaggi di punti dati e interattivi da dispositivo a cloud usando la classe [EventProcessorHost].

L'esercitazione [Come inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d] descrive le modalità di invio dei messaggi ai dispositivi dal proprio back-end.

Per avere degli esempi di soluzioni complete che utilizzano l'hub IoT, vedere la [Azure IoT Suite][lnk-suite].

Per ulteriori informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per gli sviluppatori dell'hub IoT].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

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
[Introduzione all'hub IoT]: iot-hub-java-java-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Informazioni sugli account di archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introduzione all'Hub eventi]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hub eventi]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!---HONumber=AcomDC_0907_2016-->