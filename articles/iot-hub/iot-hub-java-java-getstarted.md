---
title: Introduzione all&quot;hub IoT di Azure (Java) | Microsoft Docs
description: "Come inviare messaggi da un dispositivo a un hub IoT cloud di Azure tramite gli SDK IoT di Azure per Java. È necessario creare un&quot;app di simulazione del dispositivo per inviare messaggi, un&quot;app di servizio per registrare il dispositivo nel registro di identità e un&quot;app di servizio per leggere i messaggi dal dispositivo al cloud nell&quot;hub IoT."
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
ms.date: 02/14/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d4eb942db51af9c8136e9e0f5f8683cc15679d08
ms.openlocfilehash: 5bfbe4cfac202592ddd745c5f959cb791fe17ba8


---
# <a name="get-started-with-azure-iot-hub-java"></a>Introduzione all'hub IoT di Azure (Java)
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione si avranno tre app di console Java:

* **create-device-identity**, che crea un'identità di dispositivo e una chiave di sicurezza associata per connettere l'app per dispositivo simulato.
* **read-d2c-messages**, che consente di visualizzare i dati di telemetria inviati dall'app per dispositivo simulato.
* **simulated-device**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo MQTT.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.
* Maven 3.  <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Maven per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Prendere infine nota del valore presente in **Chiave primaria**. Fare clic su **Endpoint** e sull'endpoint predefinito **Eventi**. Nel pannello **Proprietà** prendere nota del valore presente in **Nome compatibile con l'hub eventi** e dell'indirizzo **Endpoint compatibile con l'hub eventi**. Questi tre valori sono necessari quando si crea l'app **read-d2c-messages**.

![Pannello Messaggistica dell'hub IoT nel portale di Azure][6]

L'hub IoT è stato creato. Si conoscono il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, la chiave primaria dell'hub IoT, il nome e l'endpoint compatibili con l'hub eventi necessari per completare l'esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo
In questa sezione si scriverà un'app console Java che crea un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione **Registro di identità** della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Quando si esegue questa app console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una cartella vuota denominata iot-java-get-started. Nella cartella iot-java-get-started creare un progetto Maven denominato **create-device-identity** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Al prompt dei comandi passare alla cartella create-device-identity.
3. In un editor di testo aprire il file pom.xml nella cartella create-device-identity e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iothub-service-sdk nell'app:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.11</version>
    </dependency>
    ```
4. Salvare e chiudere il file pom.xml.
5. Usando un editor di testo, aprire il file create-device-identity\src\main\java\com\mycompany\app\App.java.
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
9. Aggiungere il codice seguente come corpo del metodo **main** . Questo codice crea un dispositivo denominato *javadevice* nel registro delle identità dell'hub IoT, se non esiste già. Quindi visualizza l'ID e la chiave del dispositivo che serviranno più avanti:
   
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
    System.out.println("Device ID: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Salvare e chiudere il file App.java.
11. Per compilare l'app **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:
    
    ```
    mvn clean package -DskipTests
    ```
12. Per eseguire l'app **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Prendere nota dei valori di **Device ID** e **Device key**, che saranno necessari più avanti quando si crea un'app che si connette all'hub IoT come dispositivo.

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia le chiavi e gli ID dispositivo da usare come credenziali di sicurezza e un flag di abilitazione/disabilitazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'app deve archiviare altri metadati specifici del dispositivo, dovrà usare un'archiviazione specifica per app. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Ricezione di messaggi da dispositivo a cloud
In questa sezione si crea un'app console di Java che legge i messaggi da dispositivo a cloud dall'hub IoT. Un hub IoT espone un endpoint compatibile con l'[hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'esercitazione [Elaborare messaggi dispositivo a cloud][lnk-process-d2c-tutorial] illustra come elaborare i messaggi dispositivo a cloud su vasta scala. L'esercitazione [Introduzione all'hub eventi][lnk-eventhubs-tutorial] offre altre informazioni su come elaborare i messaggi da hub eventi ed è applicabile agli endpoint compatibili con l'hub eventi dell'hub IoT.

> [!NOTE]
> L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQP.
> 
> 

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un progetto Maven denominato **read-d2c-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Al prompt dei comandi passare alla cartella read-d2c-messages.
3. In un editor di testo aprire il file pom.xml nella cartella read-d2c-messages e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto eventhubs-client nell'app per la lettura dall'endpoint compatibile con l'hub eventi:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.10.0</version> 
    </dependency>
    ```
4. Salvare e chiudere il file pom.xml.
5. Usando un editor di testo, aprire il file read-d2c-messages\src\main\java\com\mycompany\app\App.java.
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
7. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{youriothubkey}**, **{youreventhubcompatibleendpoint}** e **{youreventhubcompatiblename}** con i valori annotati in precedenza:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Aggiungere il metodo **receiveMessages** seguente alla **classe App**. Questo metodo crea un'istanza **EventHubClient** per connettersi all'endpoint compatibile con Hub eventi e quindi crea in modo asincrono un'istanza **PartitionReceiver** per la lettura da una partizione di Hub eventi. Esegue il ciclo ininterrottamente e stampa i dettagli del messaggio finché l'applicazione non termina.
   
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
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
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
   
   > [!NOTE]
   > Questo metodo usa un filtro quando crea il ricevitore e quindi il ricevitore legge solo i messaggi inviati all'hub IoT dopo che l'esecuzione del ricevitore è iniziata. Questa tecnica è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente. In un ambiente di produzione, il codice deve verificare di avere elaborato tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].
   > 
   > 
9. Modificare la firma del metodo **main** includendo l'eccezione come segue:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Aggiungere il codice seguente al metodo **main** nella classe **App**. Questo codice crea le due istanze **EventHubClient** e **PartitionReceiver** e consente di chiudere l'app al termine dell'elaborazione dei messaggi:
    
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
    
    > [!NOTE]
    > Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1".
    > 
    > 
11. Salvare e chiudere il file App.java.
12. Per compilare l'app **read-d2c-messages** con Maven, eseguire questo comando al prompt dei comandi nella cartella read-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione si crea un'app console di Java che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un progetto Maven denominato **simulated-device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Al prompt dei comandi passare alla cartella simulated-device.
3. In un editor di testo aprire il file pom.xml nella cartella simulated-device e aggiungere le dipendenze seguenti al nodo **dependencies** . Queste dipendenze consentono di usare il pacchetto iothub-java-client nell'app per comunicare con l'hub IoT e per serializzare gli oggetti Java in JSON:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.16</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Salvare e chiudere il file pom.xml.
5. Usando un editor di testo, aprire il file simulated-device\src\main\java\com\mycompany\app\App.java.
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
7. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituzione di **{youriothubname}** con il nome dell'hub IoT e di **{yourdevicekey}** con il valore della chiave del dispositivo generato nella sezione *Creare un'identità del dispositivo*:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Questa app di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**. È possibile usare il protocollo MQTT, AMQP o HTTP per comunicare con l'hub IoT.
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
9. Aggiungere la classe **EventCallback** annidata seguente nella classe **App** per visualizzare lo stato di acknowledgement restituito dall'hub IoT quando elabora un messaggio proveniente dall'app per dispositivo simulato. Questo metodo invia anche una notifica al thread principale dell'app quando il messaggio è stato elaborato:
   
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
13. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella read-d2c eseguire questo comando per iniziare a monitorare la prima partizione dell'hub IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![App del servizio hub IoT per Java per monitorare i messaggi dispositivo a cloud][7]
2. Al prompt dei comandi nella cartella simulated-device eseguire il comando seguente per iniziare a inviare i dati di telemetria all'hub IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![App del dispositivo hub IoT per Java per inviare i messaggi da dispositivo a cloud][8]
3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub IoT:
   
    ![Riquadro Utilizzo del portale di Azure con il numero dei messaggi inviati all'hub IoT][43]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Questa identità del dispositivo è stata usata per consentire all'app per dispositivo simulato di inviare all'hub IoT messaggi dispositivo a cloud. È stata anche creata un'app che visualizza i messaggi ricevuti dall'hub IoT. 

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Connessione del dispositivo][lnk-connect-device]
* [Introduzione alla gestione dei dispositivi][lnk-device-management]
* [Introduzione all'IoT SDK per gateway][lnk-gateway-SDK]

Per informazioni sull'estensione della soluzione IoT e l'elaborazione di messaggi dispositivo a cloud su vasta scala, vedere l'esercitazione [Elaborare messaggi dispositivo a cloud][lnk-process-d2c-tutorial].

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Jan17_HO1-->


