---
title: Pianificare processi con l'hub IoT di Azure (Java) | Microsoft Docs
description: Come pianificare un processo dell'hub IoT di Azure per chiamare un metodo diretto e impostare una proprietà desiderata su più dispositivi. Usare Azure IoT SDK per dispositivi per Java per implementare le app per dispositivi simulate e Azure IoT SDK per servizi per Java per implementare un'app di servizio che esegue il processo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: bbb78dcd36ec986cefc1d57e01396f285a6b30dd
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161937"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Pianificare e trasmettere processi (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usare l'hub IoT per pianificare e tenere traccia dei processi che aggiornano milioni di dispositivi. Usare i processi per:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Un processo esegue il wrapping di una di queste azioni e tiene traccia dell'esecuzione rispetto a un set di dispositivi. Una query di dispositivi gemelli definisce il set di dispositivi su cui il processo viene eseguito. Ad esempio, un'applicazione back-end può usare un processo per chiamare un metodo diretto su 10.000 dispositivi che riavvia i dispositivi stessi. È necessario specificare il set di dispositivi con una query di dispositivi gemelli e pianificare il processo in modo che venga eseguito in un secondo momento. L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo diretto di riavvio.

Per altre informazioni su queste funzionalità, vedere:

* Dispositivo gemello e proprietà: [Introduzione ai dispositivi gemelli](iot-hub-java-java-twin-getstarted.md)

* Metodi diretti: [Guida per gli sviluppatori dell'hub Internet-metodi diretti](iot-hub-devguide-direct-methods.md) ed [esercitazione: Usare metodi diretti](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione illustra come:

* Creare un'app per dispositivi che implementa un metodo diretto denominato **lockDoor**. L'app per dispositivi riceve anche le modifiche di proprietà desiderate dall'app back-end.

* Creare un'app back-end che crea un processo per chiamare il metodo diretto **lockDoor** su più dispositivi. Un altro processo invia gli aggiornamenti di proprietà desiderati a più dispositivi.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Java e un'app back-end console Java:

**simulated-device** che si connette all'hub IoT, implementa il metodo diretto **lockDoor** e gestisce le modifiche di proprietà desiderate.

**schedule-jobs** che usa processi per chiamare il metodo diretto **lockDoor** e aggiornare le proprietà di dispositivi gemelli desiderate su più dispositivi.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) riporta informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.

## <a name="prerequisites"></a>Prerequisiti

* [Java se Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Assicurarsi di selezionare **Java 8** in **supporto a lungo termine** per ottenere i download per JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Per aggiungere un dispositivo all'hub IoT, è anche possibile usare l'[estensione IoT dell'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea un'app console Java che usa i processi per:

* Chiamare il metodo diretto **lockDoor** su più dispositivi.

* Inviare le proprietà desiderate a più dispositivi.

Per creare l'app:

1. Nel computer di sviluppo creare una cartella vuota denominata Internet degli altri- **Java-Schedule-Jobs**.

2. Nella cartella Internet delle cose **-Java-Schedule-Jobs** creare un progetto Maven denominato **Schedule-** Jobs usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Al prompt dei comandi passare alla cartella **Schedule-Jobs** .

4. Usando un editor di testo, aprire il file **POM. XML** nella cartella **Schedule-Jobs** e aggiungere la dipendenza seguente al nodo **dipendenze** . Questa dipendenza consente di usare il pacchetto **iot-service-client** nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Aggiungere il nodo **build** seguente dopo il nodo **dependencies**. Questa configurazione indica a Maven di usare Java 1.8 per compilare l'app:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Salvare e chiudere il file **pom.xml**.

7. Usando un editor di testo, aprire il file **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** .

8. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{youriothubconnectionstring}` con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Aggiungere il metodo seguente alla classe **App** per pianificare un processo con cui aggiornare le proprietà desiderate di **Building** e **Floor** nel dispositivo gemello:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Per pianificare un processo che chiama il metodo **lockDoor**, aggiungere il metodo seguente alla classe **App**:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Per monitorare un processo, aggiungere il metodo seguente alla classe **App**:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Per eseguire una query sui dettagli dei processi eseguiti, aggiungere il metodo seguente:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Aggiornare la firma del metodo **main** affinché includa la clausola `throws`:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Per eseguire e monitorare due processi in modo sequenziale, sostituire il codice nel metodo **Main** con il codice seguente:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Salvare e chiudere il file **Schedule-jobs\src\main\java\com\mycompany\app\App.Java**

17. Compilare l'app **schedule-jobs** e correggere eventuali errori. Al prompt dei comandi passare alla cartella **Schedule-Jobs** ed eseguire il comando seguente:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Creare un'app per dispositivi

In questa sezione si crea un'app console Java che gestisce le proprietà desiderate inviate dall'hub IoT e implementa la chiamata di metodo diretto.

1. Nella cartella Internet delle cose **-Java-Schedule-Jobs** creare un progetto Maven denominato **Simulated-Device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Al prompt dei comandi passare alla cartella **Simulated-Device** .

3. Usando un editor di testo, aprire il file **POM. XML** nella cartella **Simulated-Device** e aggiungere le dipendenze seguenti al nodo **dipendenze** . Questa dipendenza consente di usare il pacchetto **iot-device-client** nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-device-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Aggiungere la dipendenza seguente al nodo **dipendenze** . Questa dipendenza configura un NOP per la facciata di registrazione di Apache [SLF4J](https://www.slf4j.org/) , che viene usata da Device client SDK per implementare la registrazione. Questa configurazione è facoltativa, ma se la si omette, è possibile che venga visualizzato un avviso nella console quando si esegue l'app. Per ulteriori informazioni sulla registrazione in Device client SDK, vedere la pagina relativa alla [registrazione](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)degli *esempi per il file Leggimi di Azure per dispositivi SDK per Java* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Aggiungere il nodo **build** seguente dopo il nodo **dependencies**. Questa configurazione indica a Maven di usare Java 1.8 per compilare l'app:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Salvare e chiudere il file **pom.xml**.

7. Usando un editor di testo, aprire il file **Simulated-device\src\main\java\com\mycompany\app\app.Java.** .

8. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{yourdeviceconnectionstring}` con la stringa di connessione del dispositivo copiata in precedenza nella sezione [registrare un nuovo dispositivo nell'hub delle](#register-a-new-device-in-the-iot-hub) cose:

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Questa app di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**.

10. Per stampare le notifiche del dispositivo gemello nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Per stampare le notifiche del metodo diretto nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Per gestire chiamate di metodo diretto dall'hub IoT, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Aggiornare la firma del metodo **main** affinché includa la clausola `throws`:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Sostituire il codice nel metodo **Main** con il codice seguente per:
    * Creare un client del dispositivo per comunicare con l'IoT Hub.
    * Creare un oggetto **Device** per archiviare le proprietà dei dispositivi gemelli.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Per avviare i servizi client del dispositivo, aggiungere il codice seguente al metodo **main**:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Per attendere che l'utente prema il tasto **INVIO** prima della chiusura, aggiungere il codice seguente alla fine del metodo **main**:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Salvare e chiudere il file **Simulated-device\src\main\java\com\mycompany\app\app.Java.** .

18. Compilare l'app **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella **Simulated-Device** ed eseguire il comando seguente:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app console.

1. Al prompt dei comandi nella cartella **Simulated-Device** eseguire il comando seguente per avviare l'app del dispositivo in ascolto delle modifiche delle proprietà desiderate e delle chiamate al metodo diretto:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Il client per dispositivi si avvia](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Al prompt dei comandi nella cartella `schedule-jobs` eseguire il comando seguente per avviare l'app di servizio **schedule-jobs** in modo da eseguire due processi. Il primo imposta i valori di proprietà desiderati, il secondo chiama il metodo diretto:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![L'app di servizio dell'hub IoT Java crea due processi](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. L'app per dispositivi gestisce la modifica di proprietà desiderata e la chiamata di metodo diretto:

   ![Il client per dispositivi risponde alle modifiche](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](quickstart-send-telemetry-java.md).

* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare i metodi diretti](quickstart-control-device-java.md).
