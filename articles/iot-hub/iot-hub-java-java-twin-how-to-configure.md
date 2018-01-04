---
title: "Usare le proprietà di un dispositivo gemello dell'hub IoT di Azure (Java) | Microsoft Docs"
description: "Come usare le proprietà di un dispositivo gemello dell'hub IoT di Azure per configurare dispositivi. Usare Azure IoT SDK per dispositivi per Java per implementare un'app per dispositivo simulato e Azure IoT SDK per servizi per Java per implementare un'app di servizio che modifica la configurazione di un dispositivo usando un dispositivo gemello."
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
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 72b57a2495d1a03a57923fb171ee17c0f870ddc7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Usare le proprietà desiderate per configurare i dispositivi

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al termine di questa esercitazione si avranno due app di console Java:

* **simulated-device**, un'app per dispositivo simulata che attende un aggiornamento della configurazione desiderata e segnala lo stato di un processo di aggiornamento della configurazione simulata.
* **set-configuration**, un'app back-end che imposta la configurazione desiderata in un dispositivo ed esegue query sul processo di aggiornamento della configurazione.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] contiene informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente 
* [Maven 3](https://maven.apache.org/install.html) 
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Se è stata seguita l'esercitazione [Introduzione ai dispositivi gemelli][lnk-twin-tutorial] sono già disponibili un hub IoT e un'identità del dispositivo denominata **myDeviceId**. È quindi possibile ignorare la sezione [Creare l'app per dispositivo simulata][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Creare l'app per dispositivo simulata
In questa sezione si crea un'app console Java che si connette all'hub come **myDeviceId**, attende un aggiornamento della configurazione desiderata e quindi segnala gli aggiornamenti nel processo di aggiornamento della configurazione simulata.

1. Creare una cartella vuota denominata dt-get-started.

1. Nella cartella dt-get-started creare un progetto Maven denominato **simulated-device** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

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
    > È possibile cercare la versione più recente di **iot-device-client** usando [Maven search][lnk-maven-device-search].

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
    import java.util.Scanner;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{yourdeviceconnectionstring}** con la stringa di connessione del dispositivo indicata nella sezione *Create a device identity* (Creare un'identità del dispositivo):

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Per implementare un gestore di callback per gli eventi di stato del dispositivo gemello, (for debugging purposes), aggiungere la classe nidificata seguente alla classe **App**:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Aggiungere la classe annidata seguente alla classe **App** :

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > La classe TelemetryConfig estende la [device class][lnk-java-device-class] per ottenere l'accesso ai callback delle proprietà desiderate.

1. Modificare la firma del metodo **main** escludendo le eccezioni seguenti:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Aggiungere il codice seguente al metodo **main** per creare un'istanza di **DeviceClient** e **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Aggiungere il codice seguente al metodo **main** per avviare i servizi del dispositivo gemello:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Aggiungere il codice seguente al metodo **main** per arrestare il simulatore del dispositivo quando necessario:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app di back-end **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella simulated-device ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Questa esercitazione non simula alcun comportamento per gli aggiornamenti della configurazione simultanei. Alcuni processi di aggiornamento della configurazione potrebbero consentire modifiche della configurazione di destinazione mentre l'aggiornamento è in esecuzione, altre potrebbero doverle accodare e altri ancora rifiutarle con una condizione di errore. È importante tenere in considerazione il comportamento desiderato per il processo di configurazione specifico e aggiungere la logica appropriata prima di iniziare la modifica della configurazione.
   > 
   > 

## <a name="create-the-service-app"></a>Creare l'app di servizio
In questa sezione si crea un'app console Java che aggiorna le *proprietà desiderate* nel dispositivo gemello associato a **myDeviceId** con un nuovo oggetto di configurazione di telemetria. Viene quindi effettuata una query dei dispositivi gemelli archiviati nell'hub IoT e viene visualizzata la differenza tra la configurazione desiderata e quella segnalata del dispositivo.

1. Nella cartella dt-get-started creare un progetto Maven denominato **set-configuration** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella set-configuration.

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
    > È possibile cercare la versione più recente di **iot-service-client** usando [Maven search][lnk-maven-service-search].

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

1. Usando un editor di testo, aprire il file di origine set-configuration\src\main\java\com\mycompany\app\App.java.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire **{youriothubconnectionstring}** con la stringa di connessione dell'hub IoT indicata nella sezione *Creare un hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Per eseguire query e aggiornare i dispositivi gemelli sul dispositivo simulato, aggiungere il codice seguente al metodo **main**:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Salvare e chiudere il file set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Compilare l'app di back-end **set-configuration** e correggere eventuali errori. Al prompt dei comandi passare alla cartella set-configuration ed eseguire il comando seguente:

    `mvn clean package -DskipTests`
   
    Questo codice recupera il dispositivo gemello per **myDeviceId** e ne aggiorna le proprietà desiderate con un nuovo oggetto di configurazione dei dati di telemetria.
    Ogni 10 secondi esegue una query dei dispositivi gemelli archiviati nell'hub IoT e stampa le configurazioni dei dati di telemetria desiderate e segnalate. Vedere il [linguaggio di query dell'hub IoT][lnk-query] per informazioni su come generare report avanzati in tutti i dispositivi.
   
   > [!IMPORTANT]
   > Questa applicazione esegue una query dell'hub IoT ogni 10 secondi a scopo illustrativo fino a che il dispositivo non è stato aggiornato. Usare le query per generare i report destinati all'utente in più dispositivi e non per rilevare le modifiche. Se la soluzione richiede notifiche in tempo reale degli eventi del dispositivo, usare le [notifiche relative al dispositivo gemello][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Tra l'operazione relativa al report del dispositivo e il risultato della query si verifica un ritardo fino a un minuto, che consente all'infrastruttura della query di funzionare su una scala molto ampia.  Per recuperare visualizzazioni coerenti di un solo dispositivo gemello, usare il metodo **getDeviceTwin** nella classe **DeviceTwin**.
   > 
   > 

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. Eseguire questo comando al prompt dei comandi nella cartella simulated-device per iniziare ad ascoltare le chiamate al dispositivo gemello dell'hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Eseguire questo comando al prompt dei comandi nella cartella set-configuration per eseguire una query e aggiornare i dispositivi gemelli sul dispositivo simulato dell'hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Il dispositivo simulato risponde alla chiamata al metodo diretto di riavvio:

    ![L'app del dispositivo simulato dell'hub IoT per Java risponde alla chiamata al dispositivo gemello][img-deviceconfigured]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata impostata una configurazione desiderata come *proprietà desiderate* da un back-end della soluzione ed è stata scritta un'app per dispositivo per rilevare tale modifica e simulare un processo di aggiornamento in più fasi che segnala lo stato tramite le proprietà segnalate.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-iothub-getstarted].
* Per pianificare o eseguire operazioni su grandi set di dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-schedule-jobs].
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Use direct methods][lnk-methods-tutorial] (Usare metodi diretti).

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
