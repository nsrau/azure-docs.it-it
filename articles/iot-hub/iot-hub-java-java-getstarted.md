---
title: Introduzione all'hub IoT di Azure (Java) | Microsoft Docs
description: Informazioni su come inviare messaggi da dispositivo a cloud all'hub IoT di Azure usando IoT SDK per Java. Creare un dispositivo simulato e app di servizio per registrare il dispositivo, inviare messaggi e leggere messaggi dall'hub IoT.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88782515b28de361338879a209e483f4fc7ab648
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-java"></a>Connettere il dispositivo all'hub IoT usando Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione si avranno tre app di console Java:

* **create-device-identity**, che crea un'identità di dispositivo e una chiave di sicurezza associata per connettere l'app per dispositivi.
* **read-d2c-messages**, che visualizza i dati di telemetria inviati dall'app per dispositivi.
* **simulated-device**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo MQTT.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente 
* [Maven 3](https://maven.apache.org/install.html) 
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Prendere infine nota del valore presente in **Chiave primaria**. Fare clic su **Endpoint** e sull'endpoint predefinito **Eventi**. Nel pannello **Proprietà** prendere nota del valore presente in **Nome compatibile con l'hub eventi** e dell'indirizzo **Endpoint compatibile con l'hub eventi**. Questi tre valori sono necessari quando si crea l'app **read-d2c-messages**.

![Pannello Messaggistica dell'hub IoT nel portale di Azure][6]

L'hub IoT è stato creato. Si conoscono il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, la chiave primaria dell'hub IoT, il nome e l'endpoint compatibili con l'hub eventi necessari per completare l'esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo
In questa sezione si scriverà un'app console Java che crea un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione **Registro di identità** della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Quando si esegue questa app console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una cartella vuota denominata iot-java-get-started. Nella cartella iot-java-get-started creare un progetto Maven denominato **create-device-identity** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla cartella create-device-identity.

3. In un editor di testo aprire il file pom.xml nella cartella create-device-identity e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven][lnk-maven-service-search].

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{yourhubconnectionstring}** con i valori annotati prima:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    ```
[!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

8. Modificare la firma del metodo **main** includendo le eccezioni come segue:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```

9. Aggiungere il codice seguente come corpo del metodo **main** . Questo codice crea un dispositivo denominato *javadevice* nel registro delle identità dell'hub IoT, se non esiste già. Quindi visualizza l'ID e la chiave del dispositivo che serviranno più avanti:

    ```java
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    // Create a device that's enabled by default, 
    // with an autogenerated key.
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      // If the device already exists.
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }

    // Display information about the
    // device you created.
    System.out.println("Device Id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Salvare e chiudere il file App.java.

11. Per compilare l'app **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

12. Per eseguire l'app **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prendere nota dei valori di **Device ID** e **Device key**, che saranno necessari più avanti quando si crea un'app che si connette all'hub IoT come dispositivo.

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia le chiavi e gli ID dispositivo da usare come credenziali di sicurezza e un flag di abilitazione/disabilitazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'app deve archiviare altri metadati specifici del dispositivo, dovrà usare un'archiviazione specifica per app. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Ricezione di messaggi da dispositivo a cloud

In questa sezione si crea un'app console di Java che legge i messaggi da dispositivo a cloud dall'hub IoT. Un hub IoT espone un endpoint compatibile con l'[hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'esercitazione [Elaborare messaggi dispositivo a cloud][lnk-process-d2c-tutorial] illustra come elaborare i messaggi dispositivo a cloud su vasta scala. L'esercitazione [Introduzione all'hub eventi][lnk-eventhubs-tutorial] offre altre informazioni su come elaborare i messaggi da hub eventi ed è applicabile agli endpoint compatibili con l'hub eventi dell'hub IoT.

> [!NOTE]
> L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQP.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un progetto Maven denominato **read-d2c-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla cartella read-d2c-messages.

3. In un editor di testo aprire il file pom.xml nella cartella read-d2c-messages e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto eventhubs-client nell'app per la lettura dall'endpoint compatibile con l'hub eventi:

    ```xml
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.15.0</version> 
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```

7. Aggiungere la variabili a livello di classe seguente alla classe **App** . Sostituire **{youriothubkey}**, **{youreventhubcompatibleendpoint}** e **{youreventhubcompatiblename}** con i valori annotati in precedenza:

    ```java
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Aggiungere il metodo **receiveMessages** seguente alla **classe App**. Questo metodo crea un'istanza **EventHubClient** per connettersi all'endpoint compatibile con Hub eventi e quindi crea in modo asincrono un'istanza **PartitionReceiver** per la lettura da una partizione di Hub eventi. Esegue il ciclo ininterrottamente e stampa i dettagli del messaggio finché l'applicazione non termina.

    ```java
    // Create a receiver on a partition.
    private static EventHubClient receiveMessages(final String partitionId) {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      } catch (Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        // Create a receiver using the
        // default Event Hubs consumer group
        // that listens for messages from now on.
        client.createReceiver(EventHubClient.DEFAULT_CONSUMER_GROUP_NAME, partitionId, Instant.now())
          .thenAccept(new Consumer<PartitionReceiver>() {
            public void accept(PartitionReceiver receiver) {
              System.out.println("** Created receiver on partition " + partitionId);
              try {
                while (true) {
                  Iterable<EventData> receivedEvents = receiver.receive(100).get();
                  int batchSize = 0;
                  if (receivedEvents != null) {
                    System.out.println("Got some evenst");
                    for (EventData receivedEvent : receivedEvents) {
                      System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s",
                        receivedEvent.getSystemProperties().getOffset(),
                        receivedEvent.getSystemProperties().getSequenceNumber(),
                        receivedEvent.getSystemProperties().getEnqueuedTime()));
                      System.out.println(String.format("| Device ID: %s",
                        receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                      System.out.println(String.format("| Message Payload: %s",
                        new String(receivedEvent.getBytes(), Charset.defaultCharset())));
                      batchSize++;
                    }
                  }
                  System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId, batchSize));
                }
              } catch (Exception e) {
                System.out.println("Failed to receive messages: " + e.getMessage());
              }
            }
          });
        } catch (Exception e) {
          System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

   > [!NOTE]
   > Questo metodo usa un filtro quando crea il ricevitore e quindi il ricevitore legge solo i messaggi inviati all'hub IoT dopo che l'esecuzione del ricevitore è iniziata. Questa tecnica è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente. In un ambiente di produzione, il codice deve verificare di avere elaborato tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

9. Modificare la firma del metodo **main** includendo l'eccezione come segue:

    ```java
    public static void main( String[] args ) throws IOException
    ```

10. Aggiungere il codice seguente al metodo **main** nella classe **App**. Questo codice crea le due istanze **EventHubClient** e **PartitionReceiver** e consente di chiudere l'app al termine dell'elaborazione dei messaggi:

    ```java
    // Create receivers for partitions 0 and 1.
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    } catch (Exception e) {
      System.exit(1);
    }
    ```

    > [!NOTE]
    > Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1".

11. Salvare e chiudere il file App.java.

12. Per compilare l'app **read-d2c-messages** con Maven, eseguire questo comando al prompt dei comandi nella cartella read-d2c-messages:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Creare un'app per dispositivi
In questa sezione si crea un'app console di Java che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un progetto Maven denominato **simulated-device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla cartella simulated-device.

3. In un editor di testo aprire il file pom.xml nella cartella simulated-device e aggiungere le dipendenze seguenti al nodo **dependencies** . Queste dipendenze consentono di usare il pacchetto iothub-java-client nell'app per comunicare con l'hub IoT e per serializzare gli oggetti Java in JSON:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-device-client** usando la [ricerca di Maven][lnk-maven-device-search].

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file simulated-device\src\main\java\com\mycompany\app\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.google.gson.Gson;

    import java.io.*;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituzione di **{youriothubname}** con il nome dell'hub IoT e di **{yourdevicekey}** con il valore della chiave del dispositivo generato nella sezione *Creare un'identità del dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Questa app di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**. È possibile usare il protocollo MQTT, AMQP o HTTPS per comunicare con l'hub IoT.

8. Aggiungere la classe **TelemetryDataPoint** annidata seguente nella classe **App** per specificare i dati di telemetria inviati dal dispositivo all'hub IoT:

    ```java
    private static class TelemetryDataPoint {
      public String deviceId;
      public double temperature;
      public double humidity;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Aggiungere la classe **EventCallback** annidata seguente nella classe **App** per visualizzare lo stato di acknowledgement restituito dall'hub IoT quando elabora un messaggio proveniente dall'app per dispositivi. Questo metodo invia anche una notifica al thread principale dell'app quando il messaggio è stato elaborato:
   
    ```java
    private static class EventCallback implements IotHubEventCallback {
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

    ```java
    private static class MessageSender implements Runnable {
      public void run()  {
        try {
          double minTemperature = 20;
          double minHumidity = 60;
          Random rand = new Random();
    
          while (true) {
            double currentTemperature = minTemperature + rand.nextDouble() * 15;
            double currentHumidity = minHumidity + rand.nextDouble() * 20;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.temperature = currentTemperature;
            telemetryDataPoint.humidity = currentHumidity;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            msg.setProperty("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
            msg.setMessageId(java.util.UUID.randomUUID().toString()); 
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

    Questo metodo invia un nuovo messaggio da dispositivo a cloud un secondo dopo che l'hub IoT ha riconosciuto il messaggio precedente. Il messaggio contiene un oggetto serializzato JSON con l'ID dispositivo e numeri generati casualmente per simulare un sensore temperatura e un sensore umidità.

11. Sostituire il metodo **main** con il codice seguente che crea un thread per inviare messaggi da dispositivo a cloud all'hub IoT:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.closeNow();
    }
    ```

12. Salvare e chiudere il file App.java.

13. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella read-d2c eseguire questo comando per iniziare a monitorare la prima partizione dell'hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![App del servizio hub IoT per Java per monitorare i messaggi dispositivo a cloud][7]

2. Al prompt dei comandi nella cartella simulated-device eseguire il comando seguente per iniziare a inviare i dati di telemetria all'hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![App del dispositivo hub IoT per Java per inviare i messaggi da dispositivo a cloud][8]

3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub IoT:

    ![Riquadro Utilizzo del portale di Azure con il numero dei messaggi inviati all'hub IoT][43]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Questa identità del dispositivo è stata usata per consentire all'app per dispositivi di inviare messaggi da dispositivo a cloud all'hub IoT. È stata anche creata un'app che visualizza i messaggi ricevuti dall'hub IoT.

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Connessione del dispositivo][lnk-connect-device]
* [Introduzione alla gestione dei dispositivi][lnk-device-management]
* [Introduzione ad Azure IoT Edge][lnk-iot-edge]

Per informazioni sull'estensione della soluzione IoT e l'elaborazione di messaggi dispositivo a cloud su vasta scala, vedere l'esercitazione [Elaborare messaggi dispositivo a cloud][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

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
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
