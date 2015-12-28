<properties
	pageTitle="Creare un client Java di hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per compilare un client Java di hub IoT che utilizza l’SDK per dispositivi IoT di Microsoft Azure per Java * per comunicare con l'hub IoT di Azure."
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
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Creare un'applicazione client hub IoT di Azure con Java

In questo articolo viene descritto come creare un'applicazione client che utilizza l’[SDK per dispositivi Microsoft Azure IoT per Java][lnk-java-sdk] per comunicare con l’hub IoT di Azure. L'esercitazione illustra come creare e compilare il progetto utilizzando lo strumento [Maven][apache-maven]. È possibile seguire queste istruzioni in un computer Windows o Linux.

È possibile visualizzare il [documenti API Java][lnk-java-api-docs] come riferimento.

## Installazione

Vedere [Preparare l'ambiente di sviluppo][devbox-setup] per informazioni sui prerequisiti e sulla configurazione dell'ambiente di sviluppo in Windows o Linux.

> [AZURE.NOTE]È importante completare i passaggi descritti in [Preparare l'ambiente di sviluppo][devbox-setup] prima di iniziare questa esercitazione per installare i prerequisiti e aggiungere i file JAR necessari al repository Maven locale.

## Creare il progetto

1. Nello strumento di riga di comando, eseguire il comando seguente per creare un nuovo progetto Maven vuoto in un percorso appropriato nel computer di sviluppo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Si tratta di un lungo comando singolo. Assicurarsi di copiare il comando completo se si desidera incollarlo nello strumento di riga di comando.

    Questo comando crea una cartella di progetto denominata *iot-device* che presenta la struttura di progetto Maven standard. Per ulteriori informazioni, vedere [Maven in 5 minuti][maven-five-minutes] sul sito Web di Apache.

2. Aprire il file **pom.xml** nella cartella iot-device con un editor di testo.

3. Aggiungere la seguente nuovo sezione **dependency** dopo quella esistente per includere le librerie client necessarie:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Aggiungere la seguente sezione **build** dopo la sezione **dependencies** in modo che il file JAR finale includa le dipendenze e che il manifesto identifichi la classe **main**.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Salvare il file **pom.xml**.

## Creazione dell'applicazione

1. Aprire il file **App.java** nella cartella iot-device/src/main/java/com/mycompany/app con un editor di testo.

2. Aggiungere le seguenti istruzioni **import**, che includono le librerie per dispositivi IoT, dopo l’istruzione **package**:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Aggiungere la seguente classe **EventCallback** come classe annidata all'interno della classe **App**. Il metodo **execute** nella classe **EventCallback** viene richiamato quando il dispositivo riceve un riconoscimento dall'hub IoT in risposta a un messaggio da dispositivo a cloud.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Aggiungere la seguente classe **MessageCallback** come classe annidata all'interno della classe **App**. Il metodo **execute** nella classe **MessageCallback** consente di inviare un messaggio di feedback all'hub IoT in risposta a un messaggio da cloud a dispositivo ricevuto dal dispositivo.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Sostituire il metodo **main** esistente con il codice seguente che:

  - Crea un’istanza **DeviceClient**.
  - Inizializza il callback del messaggio per i messaggi da cloud a dispositivo.
  - Apre **DeviceClient** per abilitarlo all’invio di messaggi da dispositivo a cloud e ricevere messaggi da cloud a dispositivo.
  - Invia dieci messaggi di esempio all'hub IoT.

    Sostituire `<your device connection string>` con una stringa di connessione di dispositivo valida. È possibile eseguire il provisioning di un dispositivo e recuperare la relativa stringa di connessione utilizzando lo strumento [DeviceExplorer][lnk-device-explorer] o lo strumento [Esplora hub IoT][lnk-iothub-explorer].

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Per compilare il codice e comprimerlo in un file JAR, eseguire il comando seguente in un prompt dei comandi nella cartella del progetto **iot-device**:

    ```
    mvn package
    ```

7. Per eseguire l’applicazione, eseguire il comando seguente in un prompt dei comandi nella cartella del progetto **iot-device**:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. È possibile utilizzare lo strumento [DeviceExplorer][lnk-device-explorer] per monitorare i messaggi da dispositivo a cloud che l'hub IoT riceve e inviare messaggi da cloud a dispositivo al proprio dispositivo dall'hub IoT.

## Modifica della granularità di registrazione

Per modificare la granularità di registrazione, includere la riga seguente nel file `config.properties`:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]È possibile leggere una spiegazione dei diversi [livelli di registrazione](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html).

Quindi, impostare la proprietà JVM `java.util.logging.config.file={Path to your config.properties file}`.

Per registrare i frame AMQP, impostare la variabile di ambiente `PN_TRACE_FRM=1` comando nell'ambiente in uso.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1217_2015-->