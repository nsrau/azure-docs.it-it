---
title: Introduzione ai dispositivi gemelli dell'hub IoT di Azure (Java) | Microsoft Docs
description: Come usare i dispositivi gemelli dell'hub IoT di Azure per aggiungere tag e quindi usare una query dell'hub IoT. Usare Azure IoT SDK per dispositivi per Java per implementare l'app per i dispositivi e Azure IoT SDK per servizi per Java per implementare un'app di servizio che aggiunge i tag ed esegue la query dell'hub IoT.
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
ms.date: 07/04/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3269cb65cfb59258d761f4c60278c598bc251325
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="get-started-with-device-twins-java"></a>Introduzione ai dispositivi gemelli (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In questa esercitazione vengono create due app console Java:

* **add-tags-query**, un'app back-end .Java che aggiunge tag ed effettua query sui dispositivi gemelli.
* **simulated-device**, un'app per dispositivi Java che si connette all'hub IoT e segnala la condizione di connettività usando una proprietà segnalata.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) contiene informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.

Per completare questa esercitazione, sono necessari:

* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente
* [Maven 3](https://maven.apache.org/install.html)
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se si preferisce creare l'identità del dispositivo a livello di programmazione, leggere la sezione corrispondente nell'articolo [Connettere il dispositivo all'hub IoT usando Java](iot-hub-java-java-getstarted.md#create-a-device-identity).

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea a un'app Java che aggiunge i metadati della posizione come tag al dispositivo gemello nell'hub IoT associato a **myDeviceId**. L'app esegue innanzitutto una query dell'hub IoT per i dispositivi che si trovano negli Stati Uniti, quindi per i dispositivi che segnalano una connessione di rete tramite cellulare.

1. Nel computer di sviluppo creare una cartella vuota denominata `iot-java-twin-getstarted`.

1. Nella cartella `iot-java-twin-getstarted` creare un progetto Maven denominato **add-tags-query** eseguendo questo comando al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella `add-tags-query`.

1. In un editor di testo aprire il file `pom.xml` nella cartella `add-tags-query` e aggiungere la dipendenza seguente al nodo **dependencies**. Questa dipendenza consente di usare il pacchetto **iot-service-client** nell'app per comunicare con l'hub IoT:

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

1. Salvare e chiudere il file `pom.xml`.

1. Aprire il file `add-tags-query\src\main\java\com\mycompany\app\App.java` in un editor di testo.

1. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituire `{youriothubconnectionstring}` con la stringa di connessione dell'hub IoT indicata nella sezione *Creare un hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Aggiornare la firma del metodo **main** affinché includa la clausola `throws`:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Aggiungere il codice seguente al metodo **main** per creare gli oggetti **DeviceTwin** e **DeviceTwinDevice**. L'oggetto **DeviceTwin** gestisce la comunicazione con l'hub IoT. L'oggetto **DeviceTwinDevice** rappresenta un dispositivo gemello con le relative proprietà e tag:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Aggiungere il blocco `try/catch` seguente al metodo **main**:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Per aggiornare i tag del dispositivo gemello **region** e **plant** nel dispositivo gemello, aggiungere il codice seguente nel blocco `try`:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

1. Per eseguire una query sui dispositivi gemelli nell'hub IoT, aggiungere il codice seguente al blocco `try` dopo il codice aggiunto nel passaggio precedente. Il codice esegue due query. Ogni query restituisce un massimo di 100 dispositivi:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

1. Salvare e chiudere il file `add-tags-query\src\main\java\com\mycompany\app\App.java`

1. Compilare l'app **add-tags-query** e correggere eventuali errori. Al prompt dei comandi passare alla cartella `add-tags-query` ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Creare un'app per dispositivi

In questa sezione si crea un'app console Java che imposta un valore di proprietà restituito che viene inviato all'IoT Hub.

1. Nella cartella `iot-java-twin-getstarted` creare un progetto Maven denominato **simulated-device** usando il comando seguente nel prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Al prompt dei comandi passare alla cartella `simulated-device`.

1. In un editor di testo aprire il file `pom.xml` nella cartella `simulated-device` e aggiungere le dipendenze seguenti al nodo **dependencies**. Questa dipendenza consente di usare il pacchetto **iot-device-client** nell'app per comunicare con l'hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
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

1. Aggiungere le variabili a livello di classe seguenti alla classe **App** . Sostituzione di `{youriothubname}` con il nome dell'hub IoT e `{yourdevicekey}` con il valore della chiave del dispositivo generato nella sezione *Creare un'identità del dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Questa app di esempio usa la variabile **protocol** quando crea un'istanza di un oggetto **DeviceClient**. Attualmente per usare le funzionalità del dispositivo gemello è necessario usare il protocollo MQTT.

1. Al metodo **main** aggiungere il codice seguente:
    * Creare un client del dispositivo per comunicare con l'IoT Hub.
    * Creare un **Device** oggetto per archiviare le proprietà dispositivi gemelli.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

1. Aggiungere il codice seguente al metodo **main** per creare una proprietà segnalata **connectivityType** e inviarla all'IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Alla fine del metodo **main** aggiungere il codice seguente. Attendere che il tasto **Invio** consenta all'IoT Hub di segnalare lo stato delle operazioni del dispositivo gemello:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Salvare e chiudere il file `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Compilare l'app **simulated-device** e correggere eventuali errori. Al prompt dei comandi passare alla cartella `simulated-device` ed eseguire il comando seguente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app console.

1. Al prompt dei comandi nella cartella `add-tags-query` eseguire il comando seguente per eseguire l'app del servizio **add-tags-query**:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![App del servizio hub IoT Java per aggiornare i valori dei tag ed eseguire query di dispositivo](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    È possibile visualizzare i tag **plant** e **region** aggiunti al dispositivo gemello. Solo la prima query restituisce il dispositivo, non la seconda.

1. Al prompt dei comandi nella cartella `simulated-device` eseguire il comando seguente per aggiungere la proprietà segnalata **connectivityType** al dispositivo gemello:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Il client del dispositivo aggiunge la proprietà segnalata **connectivityType**](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. Al prompt dei comandi nella cartella `add-tags-query` eseguire il comando seguente per eseguire l'app del servizio **add-tags-query** una seconda volta:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![App del servizio hub IoT Java per aggiornare i valori dei tag ed eseguire query di dispositivo](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Ora che il dispositivo ha inviato la proprietà **connectivityType** all'hub IoT, la seconda query restituisce il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come effettuare una query delle informazioni del dispositivo gemello usando il linguaggio di query simile a SQL dell'hub IoT.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](iot-hub-java-java-getstarted.md).
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare metodi diretti](iot-hub-java-java-direct-methods.md).

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

