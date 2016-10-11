<properties
	pageTitle="Introduzione all'hub IoT di Azure per Java | Microsoft Azure"
	description="Esercitazione introduttiva all'hub IoT di Azure con Java. Usare l'hub IoT di Azure e Java con gli SDK IoT di Microsoft Azure per implementare una soluzione Internet delle cose."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# Introduzione all'hub IoT di Azure per Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione si hanno tre applicazioni console Java:

* **create-device-identity**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **read-d2c-messages**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **simulated-device**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo AMQPS.

> [AZURE.NOTE] L'articolo [SDK Hub IoT][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel backend della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.

+ Maven 3. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Maven per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Crea subito il tuo account Azure gratuito][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Infine prendere nota del valore presente in **Chiave primaria** e quindi fare clic su **Messaggistica**. Nel pannello **Messaggistica** prendere nota del valore presente in **Nome compatibile con l'hub eventi** e in **Endpoint compatibile con l'hub eventi**. Questi tre valori servono quando si creerà l'applicazione **read-d2c-messages**.

![Pannello Messaggistica dell'hub IoT nel portale di Azure][6]

Si è appena creato l'hub IoT e si conoscono il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, la chiave primaria dell'hub IoT, il nome compatibile con l'Hub eventi e l'endpoint compatibile con l'Hub eventi necessari per completare l'esercitazione.

## Creare un'identità del dispositivo

In questa sezione si scrive un'app console di Java che consente di creare una nuova identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro delle identità del dispositivo. Fare riferimento alla sezione **Registro identità del dispositivo** della [Guida per sviluppatori di hub IoT][lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una nuova cartella vuota denominata iot-java-get-started. Nella cartella iot-java-get-started creare un nuovo progetto Maven denominato **create-device-identity** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella create-device-identity.

3. In un editor di testo aprire il file pom.xml nella cartella create-device-identity e aggiungere la dipendenza seguente al nodo **dependencies**. Ciò consente di usare il pacchetto iothub-service-sdk nell'applicazione:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```
    
4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{yourhubconnectionstring}** con i valori annotati prima:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modificare la firma del metodo **main** includendo le eccezioni come segue:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Aggiungere il codice seguente come corpo del metodo **main**. Questo codice crea un dispositivo denominato *javadevice* nel registro delle identità dell'hub IoT, se non esiste già. Quindi visualizza l'ID e la chiave del dispositivo che serviranno più avanti:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Salvare e chiudere il file App.java.

11. Per compilare l'applicazione **create-device-identity** con Maven, eseguire questo comando al prompt dei comandi nella cartella create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Per eseguire l'applicazione **create-device-identity** con Maven, eseguire questo comando al prompt dei comandi nella cartella create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prendere nota dei valori di **Device id** e **Device key**, che saranno necessari più avanti quando si creerà un'applicazione che si connette all'hub IoT come dispositivo.

> [AZURE.NOTE] Il registro delle identità dell'hub IoT consente di archiviare solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia le chiavi e gli ID dispositivo da usare come credenziali di sicurezza e un flag di abilitazione/disabilitazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per sviluppatori di hub IoT][lnk-devguide-identity].

## Ricezione di messaggi da dispositivo a cloud

In questa sezione si crea un'app console di Java che legge i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con l'[Hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi da dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'[Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial] illustra come elaborare i messaggi da dispositivo a cloud su vasta scala. L'esercitazione [Introduzione all'Hub eventi][lnk-eventhubs-tutorial] fornisce altre informazioni su come elaborare i messaggi da Hub eventi ed è applicabile agli endpoint compatibili con Hub eventi dell'hub IoT.

> [AZURE.NOTE] L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQPS.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un nuovo progetto Maven denominato **read-d2c-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella read-d2c-messages.

3. In un editor di testo aprire il file pom.xml nella cartella read-d2c-messages e aggiungere la dipendenza seguente al nodo **dependencies**. Ciò consente di usare il pacchetto eventhubs-client nell'applicazione per la lettura dall'endpoint compatibile con Hub eventi:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**. Sostituire **{youriothubkey}**, **{youreventhubcompatibleendpoint}** e **{youreventhubcompatiblename}** con i valori annotati in precedenza:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Aggiungere il metodo **receiveMessages** seguente alla classe **App**. Questo metodo crea un'istanza **EventHubClient** per connettersi all'endpoint compatibile con l'Hub eventi e quindi crea in modo asincrono un'istanza **PartitionReceiver** per la lettura da una partizione di Hub eventi. Esegue il ciclo ininterrottamente e stampa i dettagli del messaggio finché l'applicazione non termina.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Questo metodo usa un filtro quando crea il ricevitore e quindi il ricevitore legge solo i messaggi inviati all'hub IoT dopo che l'esecuzione del ricevitore è iniziata. Ciò è utile in un ambiente di test perché consente di visualizzare il set corrente di messaggi. In un ambiente di produzione il codice deve verificare di avere elaborato tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

9. Modificare la firma del metodo **main** includendo l'eccezione come segue:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Aggiungere il codice seguente al metodo **main** nella classe **App**. Questo codice crea due istanze **EventHubClient** e **PartitionReceiver** e consente di chiudere l'applicazione al termine dell'elaborazione dei messaggi:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1".

11. Salvare e chiudere il file App.java.

12. Per compilare l'applicazione **read-d2c-messages** con Maven, al prompt dei comandi nella cartella read-d2c-messages eseguire questo comando:

    ```
    mvn clean package -DskipTests
    ```

## Creare un'app di dispositivo simulato

In questa sezione si crea un'app console di Java che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un nuovo progetto Maven denominato **simulated-device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella simulated-device.

3. In un editor di testo aprire il file pom.xml nella cartella simulated-device e aggiungere le dipendenze seguenti al nodo **dependencies**. Ciò consente di usare il pacchetto iothub-java-client nell'applicazione per comunicare con l'hub IoT e di serializzare gli oggetti Java in JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.8</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{youriothubname}** con il nome dell'hub IoT e **{yourdevicekey}** con il valore della chiave del dispositivo generato nella sezione *Creare un'identità del dispositivo*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Questa applicazione di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**. È possibile usare il protocollo HTTPS o AMQPS per comunicare con l'hub IoT.

8. Aggiungere la classe **TelemetryDataPoint** annidata seguente nella classe **App** per specificare i dati di telemetria inviati dal dispositivo all'hub IoT:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Aggiungere la classe **EventCallback** annidata seguente nella classe **App** per visualizzare lo stato di acknowledgement restituito dall'hub IoT quando elabora un messaggio proveniente dal dispositivo simulato. Questo metodo invia anche una notifica al thread principale dell'applicazione quando il messaggio è stato elaborato:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Aggiungere la classe **MessageSender** annidata seguente nella classe **App**. Il metodo **run** in questa classe genera dati di telemetria di esempio da inviare all'hub IoT e attende un acknowledgement prima di inviare il messaggio successivo:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Questo metodo invia un nuovo messaggio da dispositivo a cloud un secondo dopo che l'hub IoT ha riconosciuto il messaggio precedente. Il messaggio contiene un oggetto serializzato JSON con deviceId e un numero generato casualmente per simulare un sensore di velocità del vento.

11. Sostituire il metodo **main** con il codice seguente che crea un thread per inviare messaggi da dispositivo a cloud all'hub IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Salvare e chiudere il file App.java.

13. Per compilare l'applicazione **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei][lnk-transient-faults].

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella read-d2c eseguire questo comando per iniziare a monitorare la prima partizione dell'hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Applicazione client Java del servizio hub IoT per il monitoraggio dei messaggi da dispositivo a cloud][7]

2. Al prompt dei comandi nella cartella simulated-device eseguire il comando seguente per iniziare a inviare i dati di telemetria all'hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Applicazione client Java per dispositivi dell'hub IoT per l'invio dei messaggi da dispositivo a cloud][8]

3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub:

    ![Riquadro Utilizzo del portale di Azure con il numero dei messaggi inviati all'hub IoT][43]

## Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel portale ed è stata quindi creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di inviare all'hub messaggi da dispositivo a cloud. È stata anche creata un'app che visualizza i messaggi ricevuti dall'hub.

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Connessione del dispositivo][lnk-connect-device]
- [Introduzione alla gestione dei dispositivi][lnk-device-management]
- [Introduzione a Gateway SDK][lnk-gateway-SDK]

Per informazioni sull'estensione della soluzione IoT e l'elaborazione di messaggi da dispositivo a cloud su vasta scala, vedere [Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1005_2016-->