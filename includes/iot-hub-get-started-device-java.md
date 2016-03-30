## Creare un'app di dispositivo simulato

In questa sezione si creerà un'app console di Java che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un nuovo progetto Maven denominato **simulated-device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella simulated-device.

3. Usando un editor di testo, aprire il file pom.xml nella cartella simulated-device e aggiungere le dipendenze seguenti al nodo **dependencies**. Ciò consente di usare il pacchetto iothub-java-client nell'applicazione per comunicare con l'hub IoT e di serializzare gli oggetti Java in JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
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
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{youriothubname}** con il nome dell'hub IoT e **{yourdeviceid}** e **{yourdevicekey}** con i valori del dispositivo generati nella sezione *Creare un'identità del dispositivo*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
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
        System.out.println("IoT Hub responded to message with status " + status.name());
      
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
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Questo metodo invia un nuovo messaggio da dispositivo a cloud un secondo dopo che l'hub IoT ha riconosciuto il messaggio precedente. Il messaggio contiene un oggetto serializzato JSON con deviceId e un numero generato casualmente per simulare un sensore di velocità del vento.

11. Sostituire il metodo **main** con il codice seguente che crea un thread per inviare messaggi da dispositivo a cloud all'hub IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Salvare e chiudere il file App.java.

13. Per compilare l'applicazione **simulated-device** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0323_2016-->