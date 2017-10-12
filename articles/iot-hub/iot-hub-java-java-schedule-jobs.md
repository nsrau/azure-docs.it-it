---
title: Pianificare processi con l'hub IoT di Azure (Java) | Microsoft Docs
description: "Come pianificare un processo dell'hub IoT di Azure per chiamare un metodo diretto e impostare una proprietà desiderata su più dispositivi. Usare Azure IoT SDK per dispositivi per Java per implementare le app per dispositivi simulate e Azure IoT SDK per servizi per Java per implementare un'app di servizio che esegue il processo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: 9009afc83b40064c6dbf10100f514c681af9b96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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
* Metodi diretti: [Guida per sviluppatori dell'hub IoT - Metodi diretti](iot-hub-devguide-direct-methods.md) ed [Esercitazione: Usare metodi diretti](iot-hub-java-java-direct-methods.md)

Questa esercitazione illustra come:

* Creare un'app per dispositivi che implementa un metodo diretto denominato **lockDoor**. L'app per dispositivi riceve anche le modifiche di proprietà desiderate dall'app back-end.
* Creare un'app back-end che crea un processo per chiamare il metodo diretto **lockDoor** su più dispositivi. Un altro processo invia gli aggiornamenti di proprietà desiderati a più dispositivi.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Java e un'app back-end console Java:

**simulated-device** che si connette all'hub IoT, implementa il metodo diretto **lockDoor** e gestisce le modifiche di proprietà desiderate.

**schedule-jobs** che usa processi per chiamare il metodo diretto **lockDoor** e aggiornare le proprietà di dispositivi gemelli desiderate su più dispositivi.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) riporta informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente
* [Maven 3](https://maven.apache.org/install.html)
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se si preferisce creare l'identità del dispositivo a livello di programmazione, leggere la sezione corrispondente nell'articolo [Connettere il dispositivo all'hub IoT usando Java](iot-hub-java-java-getstarted.md#create-a-device-identity). È possibile anche usare lo strumento [iothub-explorer](https://github.com/Azure/iothub-explorer) per aggiungere un dispositivo all'hub IoT.

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea un'app console Java che usa i processi per:

* Chiamare il metodo diretto **lockDoor** su più dispositivi.
* Inviare le proprietà desiderate a più dispositivi.

Per creare l'app:

1. Nel computer di sviluppo creare una cartella vuota denominata `iot-java-schedule-jobs`.

1. Nella cartella `iot-java-schedule-jobs` creare un progetto Maven denominato **schedule-jobs** eseguendo il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella `schedule-jobs`.

1. In un editor di testo aprire il file `pom.xml` nella cartella `schedule-jobs` e aggiungere la dipendenza seguente al nodo **dependencies**. Questa dipendenza consente di usare il pacchetto **iot-service-client** nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Aggiungere il nodo **build** seguente dopo il nodo **dependencies**. Questa configurazione indica a Maven di usare Java 1.8 per compilare l'app:

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

1. Salvare e chiudere il file `pom.xml`.

1. Aprire il file `schedule-jobs\src\main\java\com\mycompany\app\App.java` in un editor di testo.

1. Aggiungere al file le istruzioni **import** seguenti:

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

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{youriothubconnectionstring}` con la stringa di connessione dell'hub IoT indicata nella sezione *Creare un hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Aggiungere il metodo seguente alla classe **App** per pianificare un processo con cui aggiornare le proprietà desiderate di **Building** e **Floor** nel dispositivo gemello:

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

1. Per pianificare un processo che chiama il metodo **lockDoor**, aggiungere il metodo seguente alla classe **App**:

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

1. Per monitorare un processo, aggiungere il metodo seguente alla classe **App**:

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

1. Per eseguire una query sui dettagli dei processi eseguiti, aggiungere il metodo seguente:

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

1. Aggiornare la firma del metodo **main** affinché includa la clausola `throws`:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Per eseguire e monitorare due processi in sequenza, aggiungere il codice seguente al metodo **main**:

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

1. Salvare e chiudere il file `schedule-jobs\src\main\java\com\mycompany\app\App.java`

1. Compilare l'app **schedule-jobs** e correggere eventuali errori. Al prompt dei comandi passare alla cartella `schedule-jobs` ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Creare un'app per dispositivi

In questa sezione si crea un'app console Java che gestisce le proprietà desiderate inviate dall'hub IoT e implementa la chiamata di metodo diretto.

1. Nella cartella `iot-java-schedule-jobs` creare un progetto Maven denominato **simulated-device** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella `simulated-device`.

1. In un editor di testo aprire il file `pom.xml` nella cartella `simulated-device` e aggiungere le dipendenze seguenti al nodo **dependencies**. Questa dipendenza consente di usare il pacchetto **iot-device-client** nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-device-client** usando la [ricerca di Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Aggiungere il nodo **build** seguente dopo il nodo **dependencies**. Questa configurazione indica a Maven di usare Java 1.8 per compilare l'app:

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

1. Salvare e chiudere il file `pom.xml`.

1. Aprire il file `simulated-device\src\main\java\com\mycompany\app\App.java` in un editor di testo.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituzione di `{youriothubname}` con il nome dell'hub IoT e di `{yourdevicekey}` con il valore della chiave del dispositivo generato nella sezione *Creare un'identità del dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Questa app di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**.

1. Per stampare le notifiche del dispositivo gemello nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Per stampare le notifiche del metodo diretto nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Per gestire chiamate di metodo diretto dall'hub IoT, aggiungere la classe annidata seguente alla classe **App**:

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

1. Aggiornare la firma del metodo **main** affinché includa la clausola `throws`:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Al metodo **main** aggiungere il codice seguente:
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

1. Per avviare i servizi client del dispositivo, aggiungere il codice seguente al metodo **main**:

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

1. Per attendere che l'utente prema il tasto **INVIO** prima della chiusura, aggiungere il codice seguente alla fine del metodo **main**:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Salvare e chiudere il file `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Compilare l'app **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella `simulated-device` ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app console.

1. Al prompt dei comandi nella cartella `simulated-device` eseguire il comando seguente per avviare l'app per dispositivi che ascolta le modifiche di proprietà desiderate e le chiamate di metodo diretto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Il client per dispositivi si avvia](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. Al prompt dei comandi nella cartella `schedule-jobs` eseguire il comando seguente per avviare l'app di servizio **schedule-jobs** in modo da eseguire due processi. Il primo imposta i valori di proprietà desiderati, il secondo chiama il metodo diretto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![L'app di servizio dell'hub IoT Java crea due processi](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. L'app per dispositivi gestisce la modifica di proprietà desiderata e la chiamata di metodo diretto:

    ![Il client per dispositivi risponde alle modifiche](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. È stata creata un'app back-end per l'esecuzione di due processi. Il primo processo ha impostato i valori di proprietà desiderati e il secondo processo ha chiamato un metodo diretto.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](iot-hub-java-java-getstarted.md).
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare metodi diretti](iot-hub-java-java-direct-methods.md).
