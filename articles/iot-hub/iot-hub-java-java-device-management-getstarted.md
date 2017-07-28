---
title: Introduzione alla gestione dei dispositivi dell'hub IoT di Azure (Java) | Microsoft Docs
description: Come usare la gestione dei dispositivi dell'hub IoT di Azure per riavviare un dispositivo remoto. Usare Azure IoT SDK per dispositivi per Java per implementare un'app per dispositivo simulato che include un metodo diretto e Azure IoT SDK per servizi per Java per implementare un'app del servizio che richiama il metodo diretto.
services: iot-hub
documentationcenter: .java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: f4690097da1b56a7688754294ab63ec88a457761
ms.contentlocale: it-it
ms.lasthandoff: 05/19/2017


---

# <a name="get-started-with-device-management-java"></a>Introduzione alla gestione dei dispositivi (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.
* Creare un'app per dispositivo simulato che implementa un metodo diretto per riavviare il dispositivo. I metodi diretti vengono richiamati dal cloud.
* Creare un'app che richiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT. Questa app controlla quindi le proprietà segnalate dal dispositivo per vedere quando l'operazione di riavvio viene completata.

Al termine di questa esercitazione si avranno due app di console Java:

**simulated-device**. Questa app:

* Si connette all'hub IoT con l'identità del dispositivo creato in precedenza.
* Riceve una chiamata del metodo diretto per il riavvio.
* Simula un riavvio fisico.
* Segnala l'ora dell'ultimo riavvio con una proprietà segnalata.

**trigger-reboot**. Questa app:

* Chiama un metodo diretto nell'app per dispositivo simulato.
* Mostra la risposta alla chiamata del metodo diretto inviata dal dispositivo simulato
* Consente di visualizzare le proprietà segnalate aggiornate.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.

Per completare questa esercitazione, sono necessari:

* Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.
* Maven 3.  <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare [Maven][lnk-maven] per questa esercitazione in Windows o Linux.
* [Versione di Node.js: 0.10.0 o successiva](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione viene creata un'app console Java che chiama il metodo diretto di riavvio nell'app per dispositivo simulato e mostra la risposta. L'app esegue quindi il polling delle proprietà segnalate inviate dal dispositivo per determinare quando il riavvio è stato completato. Quest'app console si connette all'hub IoT per richiamare il metodo diretto e leggere le proprietà segnalate.

1. Creare una cartella vuota denominata dm-get-started.

1. Nella cartella dm-get-started creare un progetto Maven denominato **trigger-reboot** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella trigger-reboot.

1. In un editor di testo aprire il file pom.xml nella cartella trigger-reboot e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven][lnk-maven-service-search].

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

1. Salvare e chiudere il file pom.xml.

1. Usando un editor di testo, aprire il file di origine trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{youriothubconnectionstring}** con la stringa di connessione dell'hub IoT indicata nella sezione *Creare un hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Per implementare un thread che legge le proprietà segnalate da un dispositivo gemello ogni 10 secondi, aggiungere la seguente classe nidificata alla classe **App**:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Per richiamare il metodo diretto di riavvio sul dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Per avviare il thread per eseguire il polling delle proprietà segnalate dal dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Per consentire all'utente di arrestare l'app, aggiungere il codice seguente al metodo **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Salvare e chiudere il file trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app di back-end **trigger riavvio** e correggere eventuali errori. Al prompt dei comandi passare alla cartella trigger-reboot ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione si crea un'app console Java che simula un dispositivo. L'app è in attesa della chiamata al metodo diretto di riavvio dall'hub IoT e risponde immediatamente. L'app viene quindi sospesa per un periodo di tempo per simulare il processo di riavvio prima di usare una proprietà segnalata per notificare all'app di back-end **trigger-reboot** il completamento del riavvio.

1. Nella cartella dm-get-started creare un progetto Maven denominato **simulated-device** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella simulated-device.

1. In un editor di testo aprire il file pom.xml nella cartella simulated-device e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-device-client** usando la [ricerca di Maven][lnk-maven-device-search].

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

1. Salvare e chiudere il file pom.xml.

1. Usando un editor di testo, aprire il file di origine simulated-device\src\main\java\com\mycompany\app\App.java.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{yourdeviceconnectionstring}** con la stringa di connessione del dispositivo indicata nella sezione *Create a device identity* (Creare un'identità del dispositivo):

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Per implementare un gestore di callback per gli eventi di stato del metodo diretto, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Per implementare un gestore di callback per gli eventi di stato del dispositivo gemello, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Per implementare un gestore di callback per gli eventi delle proprietà, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Per implementare un thread per simulare il riavvio del dispositivo, aggiungere la classe nidificata seguente alla classe **App**. Il thread rimane inattivo per cinque secondi e quindi imposta la proprietà segnalata **lastReboot**:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Per implementare il metodo diretto nel dispositivo, aggiungere la classe nidificata seguente alla classe **App**. Quando l'app simulata riceve una chiamata al metodo diretto di **riavvio**, restituisce una conferma al chiamante e quindi avvia un thread per elaborare il riavvio:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Aggiungere il codice seguente al metodo **main** per creare un'istanza di **DeviceClient**:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Nel metodo **main** aggiungere il codice seguente per avviare l'ascolto delle chiamate al metodo diretto:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Aggiungere il codice seguente al metodo **main** per arrestare il simulatore del dispositivo:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app di back-end **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella simulated-device ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. Eseguire questo comando al prompt dei comandi nella cartella simulated-device per iniziare ad ascoltare le chiamate ai metodi di riavvio dell'hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![App per il dispositivo simulato dell'hub IoT per Java per ascoltare le chiamate al metodo diretto di riavvio][1]

1. Eseguire questo comando al prompt dei comandi nella cartella trigger-reboot per chiamare un metodo di riavvio sul dispositivo simulato dell'hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![App del servizio hub IoT per Java per chiamare il metodo diretto di riavvio][2]

1. Il dispositivo simulato risponde alla chiamata al metodo diretto di riavvio:

    ![L'app del dispositivo simulato dell'hub IoT per Java risponde alla chiamata al metodo diretto][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
