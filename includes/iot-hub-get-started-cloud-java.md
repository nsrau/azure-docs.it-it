## Creare un'identità del dispositivo

In questa sezione si scriverà un'app console di Java che consente di creare una nuova identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro delle identità del dispositivo. Fare riferimento alla sezione **Registro identità del dispositivo** della [Guida per sviluppatori di hub IoT][lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console, viene generato un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una nuova cartella vuota denominata iot-java-get-started. Nella cartella iot-java-get-started creare un nuovo progetto Maven denominato **create-device-identity** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella create-device-identity.

3. Usando un editor di testo, aprire il file pom.xml nella cartella create-device-identity e aggiungere la dipendenza seguente al nodo **dependencies**. Ciò consente di usare il pacchetto iothub-service-sdk nell'applicazione:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
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

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{yourhubname}** e **{yourhubkey}** con i valori annotati prima:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Modificare la firma del metodo **main** per includere le eccezioni visualizzate sotto:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Aggiungere il codice seguente come corpo del metodo **main**. Questo codice crea un dispositivo denominato *javadevice* nel registro delle identità dell'hub IoT, se non esiste già. Quindi visualizza l'ID e la chiave del dispositivo che serviranno più avanti:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId);
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

11. Per compilare l'applicazione **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Per eseguire l'applicazione **create-device-identity** con Maven, eseguire il comando seguente al prompt dei comandi nella cartella create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prendere nota dei valori di **Device id** e **Device key**, che saranno necessari più avanti quando si creerà un'applicazione che si connette all'hub IoT come dispositivo.

> [AZURE.NOTE] Il registro delle identità dell'hub IoT consente di archiviare solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per sviluppatori di hub IoT][lnk-devguide-identity].

## Ricezione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Java che legge i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi da dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'esercitazione [Elaborare messaggi da dispositivo a cloud][lnk-processd2c-tutorial] illustra come elaborare messaggi da un dispositivo al cloud su vasta scala. L'esercitazione [Introduzione a Hub eventi][lnk-eventhubs-tutorial] fornisce altre informazioni su come elaborare i messaggi da Hub eventi ed è applicabile agli endpoint compatibili con Hub eventi dell'hub IoT.

1. Nella cartella iot-java-get-started creata nella sezione *Creare un'identità del dispositivo* creare un nuovo progetto Maven denominato **read-d2c-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella read-d2c-messages.

3. Usando un editor di testo, aprire il file pom.xml nella cartella read-d2c-messages e aggiungere la dipendenza seguente al nodo **dependencies**. Ciò consente di usare il pacchetto eventhubs-client nell'applicazione per la lettura dall'endpoint compatibile con Hub eventi:

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Usando un editor di testo, aprire il file read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**:

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Aggiungere la classe annidata seguente nella classe **App**. L'applicazione crea due thread per eseguire **MessageReceiver** per leggere i messaggi dalle due partizioni nell'hub eventi:

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Aggiungere il costruttore seguente alla classe **MessageReceiver**:

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Aggiungere il metodo **run** seguente alla classe **MessageReceiver**: Questo metodo crea un'istanza di **EventHubReceiver** per la lettura da una partizione di Hub eventi. Esegue il ciclo ininterrottamente e stampa i dettagli del messaggio nella console finché **stopThread** è true.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Questo metodo usa un filtro quando crea il ricevitore e quindi il ricevitore legge solo i messaggi inviati all'hub IoT dopo che l'esecuzione del ricevitore è iniziata. Ciò è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente, ma in un ambiente di produzione il codice deve verificare di elaborare tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Come elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-processd2c-tutorial].

11. Modificare la firma del metodo **main** per includere le eccezioni visualizzate sotto:

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Aggiungere il codice seguente al metodo **main** nella classe **App**. Questo codice crea un'istanza di **EventHubClient** per la connessione all'endpoint compatibile con Hub eventi nell'hub IoT. Crea quindi due thread per la lettura dalle due partizioni. Sostituire **{youriothubkey}**, **{youreventhubcompatiblenamespace}** e **{youreventhubcompatiblename}** con i valori annotati in precedenza. Il valore del segnaposto **{youreventhubcompatiblenamespace}** proviene dall'**Endpoint compatibile con l'hub eventi** ed è espresso nel formato **xxxxnamespace.servicebus.windows.net**.

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1". Se l'hub IoT è stato creato con un altro piano tariffario, è consigliabile modificare il codice per creare un elemento **MessageReceiver** per ogni partizione.

13. Salvare e chiudere il file App.java.

14. Per compilare l'applicazione **read-d2c-messages** con Maven, eseguire questo comando al prompt dei comandi nella cartella read-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0204_2016-->