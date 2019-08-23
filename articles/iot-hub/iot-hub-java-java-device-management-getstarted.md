---
title: Introduzione alla gestione dei dispositivi dell'hub IoT di Azure (Java) | Microsoft Docs
description: Come usare la gestione dei dispositivi dell'hub IoT di Azure per riavviare un dispositivo remoto. Usare Azure IoT SDK per dispositivi per Java per implementare un'app per dispositivo simulato che include un metodo diretto e Azure IoT SDK per servizi per Java per implementare un'app del servizio che richiama il metodo diretto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 7fed6caa719824dd51b19789a7066ffaea13d336
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907793"
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

* Mostra la risposta alla chiamata del metodo diretto inviata dal dispositivo simulato.

* Consente di visualizzare le proprietà segnalate aggiornate.

> [!NOTE]
> Per informazioni sugli SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Java SE 8. [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) descrive come installare Java per questa esercitazione in Windows o Linux.

* Maven 3. [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) descrive come installare [Maven](https://maven.apache.org/what-is-maven.html) per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione si crea un'app console Java che:

1. Richiama il metodo diretto reboot nell'app per dispositivo simulato.

2. Visualizza la risposta.

3. Esegue il polling delle proprietà segnalate inviate dal dispositivo per determinare quando viene completato il riavvio.

Quest'app console si connette all'hub IoT per richiamare il metodo diretto e leggere le proprietà segnalate.

1. Creare una cartella vuota denominata **DM-Get-Started**.

2. Nella cartella **DM-Get-Started** creare un progetto Maven denominato **trigger-riavvia** usando il comando seguente al prompt dei comandi:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Al prompt dei comandi passare alla cartella **trigger-reboot** .

4. Usando un editor di testo, aprire il file **POM. XML** nella cartella **trigger-reboot** e aggiungere la dipendenza seguente al nodo **dipendenze** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

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

7. Usando un editor di testo, aprire il file di origine **trigger-reboot\src\main\java\com\mycompany\app\app.Java.** .

8. Aggiungere al file le istruzioni **import** seguenti:

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

9. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{youriothubconnectionstring}` con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Per implementare un thread che legge le proprietà segnalate da un dispositivo gemello ogni 10 secondi, aggiungere la seguente classe nidificata alla classe **App**:

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

11. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Per richiamare il metodo diretto di riavvio sul dispositivo simulato, sostituire il codice nel metodo **Main** con il codice seguente:

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

13. Per avviare il thread per eseguire il polling delle proprietà segnalate dal dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Per consentire all'utente di arrestare l'app, aggiungere il codice seguente al metodo **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Salvare e chiudere il file **trigger-reboot\src\main\java\com\mycompany\app\app.Java.** .

16. Compilare l'app di back-end **trigger riavvio** e correggere eventuali errori. Al prompt dei comandi passare alla cartella **trigger-reboot** ed eseguire il comando seguente:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione si crea un'app console Java che simula un dispositivo. L'app è in attesa della chiamata al metodo diretto di riavvio dall'hub IoT e risponde immediatamente. L'app viene quindi sospesa per un periodo di tempo per simulare il processo di riavvio prima di usare una proprietà segnalata per notificare all'app di back-end **trigger-reboot** il completamento del riavvio.

1. Nella cartella **DM-Get-Started** creare un progetto Maven denominato Simulated **-Device** usando il comando seguente al prompt dei comandi:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla cartella **Simulated-Device** .

3. Usando un editor di testo, aprire il file **POM. XML** nella cartella **Simulated-Device** e aggiungere la dipendenza seguente al nodo **dipendenze** . Questa dipendenza consente di usare il pacchetto iot-service-client nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-device-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Aggiungere la dipendenza seguente al nodo **dipendenze** . Questa dipendenza configura un NOP per la facciata di registrazione di Apache [SLF4J](https://www.slf4j.org/) , che viene usata da Device client SDK per implementare la registrazione. Questa configurazione è facoltativa, ma se viene omessa, è possibile che venga visualizzato un avviso nella console quando si esegue l'app. Per ulteriori informazioni sulla registrazione in Device client SDK, vedere la pagina relativa alla [registrazione](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) degli *esempi per il file Leggimi di Azure per dispositivi SDK per Java* .

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

7. Usando un editor di testo, aprire il file di origine **Simulated-device\src\main\java\com\mycompany\app\app.Java.** .

8. Aggiungere al file le istruzioni **import** seguenti:

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

9. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{yourdeviceconnectionstring}` con la stringa di connessione del dispositivo annotata nella sezione [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub) tutto:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Per implementare un gestore di callback per gli eventi di stato del metodo diretto, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Per implementare un gestore di callback per gli eventi di stato del dispositivo gemello, aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Per implementare un gestore di callback per gli eventi delle proprietà, aggiungere la classe nidificata seguente alla classe **App**:

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

13. Per implementare un thread per simulare il riavvio del dispositivo, aggiungere la classe nidificata seguente alla classe **App**. Il thread rimane inattivo per cinque secondi e quindi imposta la proprietà segnalata **lastReboot**:

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

14. Per implementare il metodo diretto nel dispositivo, aggiungere la classe nidificata seguente alla classe **App**. Quando l'app simulata riceve una chiamata al metodo diretto di **riavvio** , restituisce un riconoscimento al chiamante e quindi avvia un thread per elaborare il riavvio:

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

15. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Per creare un'istanza di un **DeviceClient**, sostituire il codice nel metodo **Main** con il codice seguente:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Per avviare l'ascolto delle chiamate al metodo diretto, aggiungere il codice seguente al metodo **main**:

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

18. Per arrestare il simulatore del dispositivo, aggiungere il codice seguente al metodo **main**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

20. Compilare l'app **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella **Simulated-Device** ed eseguire il comando seguente:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Eseguire le app

A questo punto si è pronti per eseguire le app.

1. Al prompt dei comandi nella cartella **Simulated-Device** eseguire il comando seguente per iniziare ad ascoltare le chiamate al metodo di riavvio dall'hub Internet:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![App per il dispositivo simulato dell'hub IoT per Java per ascoltare le chiamate al metodo diretto di riavvio](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Eseguire il comando seguente al prompt dei comandi nella cartella **trigger-reboot** per chiamare il metodo Reboot sul dispositivo simulato dall'hub Internet:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![App del servizio hub IoT per Java per chiamare il metodo diretto di riavvio](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Il dispositivo simulato risponde alla chiamata al metodo diretto di riavvio:

    ![L'app del dispositivo simulato dell'hub IoT per Java risponde alla chiamata al metodo diretto](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
