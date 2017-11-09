---
title: Aggiornamento firmware del dispositivo con l'hub IoT di Azure (Java/Java) | Microsoft Docs
description: Come usare la gestione dei dispositivi nell'hub IoT di Azure per avviare un aggiornamento del firmware del dispositivo. Usare Azure IoT SDK per dispositivi per Java per implementare un'app per dispositivo simulato e un'app di servizio che attiva l'aggiornamento del firmware.
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 024c2e9bf580f97b412a85913ca29d757872556a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Usare la gestione dei dispositivi per avviare un aggiornamento del firmware del dispositivo (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo modello viene usato nell'implementazione dell'aggiornamento del firmware per l'[esempio di implementazione del dispositivo Raspberry Pi][lnk-rpi-implementation].

Questa esercitazione illustra come:

* Creare un'app console Java che chiama il metodo diretto **firmwareUpdate** nell'app per dispositivo simulato tramite l'hub IoT.
* Creare un'app console Java che simula il dispositivo e implementa il metodo diretto **firmwareUpdate**. Questo metodo avvia un processo in più fasi che attende di scaricare l'immagine del firmware, quindi la scarica e infine la applica. Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine di questa esercitazione si avranno due app di console Java:

**firmware-update**: chiama un metodo diretto nel dispositivo simulato, visualizza la risposta e periodicamente visualizza gli aggiornamenti delle proprietà segnalate

**simulated-device**: si connette all'hub IoT con l'identità del dispositivo precedentemente creata, riceve la chiamata del metodo diretto firmwareUpdate ed esegue una simulazione di aggiornamento del firmware

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente 
* [Maven 3](https://maven.apache.org/install.html) 
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione viene creata un'app console Java che avvia un aggiornamento del firmware remoto in un dispositivo. L'applicazione usa un metodo diretto per avviare l'aggiornamento e usa le query di dispositivo gemello per ottenere periodicamente lo stato di aggiornamento del firmware attivo.

1. Creare una cartella vuota denominata fw-get-started.

1. Nella cartella fw-get-started creare un progetto Maven denominato **firmware-update** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella firmware-update.

1. In un editor di testo aprire il file pom.xml nella cartella firmware-update e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. Salvare e chiudere il file pom.xml.

1. In un editor di testo aprire il file di origine firmware-update\src\main\java\com\mycompany\app\App.java.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{youriothubconnectionstring}** con la stringa di connessione dell'hub IoT indicata nella sezione *Creare un hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Per implementare un metodo che legge le proprietà segnalate da un dispositivo gemello, aggiungere la classe seguente alla classe **App**:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Per richiamare il metodo diretto firmwareUpdate sul dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Per eseguire il polling delle proprietà segnalate dal dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    ShowReportedProperties();
    ```

1. Per consentire all'utente di arrestare l'app, aggiungere il codice seguente al metodo **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Salvare e chiudere il file firmware-update\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app di back-end **firmware-update** e correggere eventuali errori. Al prompt dei comandi passare alla cartella firmware-update ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Simulare un dispositivo per gestire chiamate del metodo diretto
In questa sezione viene creata un'app console Java per il dispositivo simulato in grado di ricevere il metodo diretto firmwareUpdate. L'app viene quindi eseguita tramite un processo con più stati per simulare l'aggiornamento del firmware usando la proprietà reportedProperties per comunicare lo stato.

1. Nella cartella fw-get-started creare un progetto Maven denominato **simulated-device** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella simulated-device.

1. In un editor di testo aprire il file pom.xml nella cartella firmware-update e aggiungere la dipendenza seguente al nodo **dependencies** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
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
    import java.util.HashMap;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{yourdeviceconnectionstring}** con la stringa di connessione del dispositivo di cui si è preso nota nella sezione *Creare l'identità di un dispositivo*:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Per implementare la funzionalità del metodo diretto, fornire i callback aggiungendo le classi nidificate seguenti per la classe **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
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

1. Per implementare la funzionalità del dispositivo gemello, fornire i callback aggiungendo le classi nidificate seguenti per la classe **App**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Per implementare l'aggiornamento del firmware, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Per avviare i metodi diretti e le routine dei dispositivi gemelli, aggiungere il codice seguente al metodo **main**:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Per consentire all'utente di arrestare l'app, aggiungere il codice seguente alla fine del metodo **main**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella simulated-device ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **simulated-device** eseguire il comando seguente per iniziare l'ascolto del metodo diretto per l'aggiornamento del firmware.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Al prompt dei comandi nella cartella **firmware-update** eseguire il comando seguente per chiamare l'aggiornamento del firmware ed eseguire una query sui dispositivi gemelli nel dispositivo simulato dell'hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Nella console viene visualizzata la risposta del dispositivo simulato al metodo diretto.

    ![Firmware aggiornato][img-fwupdate]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device