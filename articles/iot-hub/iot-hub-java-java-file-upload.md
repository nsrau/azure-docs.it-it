---
title: Caricare file dai dispositivi nell'hub IoT di Azure con Java | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Java. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 3658b57d003ddc5429c6857f88044376fe1aaa93
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548984"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Caricare file da un dispositivo al cloud con l'hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice contenuto nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT](iot-hub-java-java-c2d.md) e illustra come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file in [Archiviazione BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

* Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.

* Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

Le esercitazioni [Inviare dati di telemetria a un hub IoT (Java)](quickstart-send-telemetry-java.md) e [Inviare messaggi da cloud a dispositivo con l'hub IoT (Java)](iot-hub-java-java-c2d.md) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT. L'esercitazione [Configurare il routing dei messaggi con l'IoT Hub](tutorial-routing.md) illustra come archiviare in modo affidabile i messaggi da dispositivo a cloud nell'archivio BLOB di Azure. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Ad esempio: 

* File di grandi dimensioni che contengono immagini
* Video
* Dati di vibrazione campionati ad alta frequenza
* Qualche tipo di dati pre-elaborati.

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.yml). Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

Al termine di questa esercitazione verranno eseguite due app console Java:

* **simulated-device**, una versione modificata dell'app creata nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT]. Ciò consente di caricare un file nell'archivio tramite un URI con firma di accesso condiviso fornito dall'hub IoT.

* **read-file-upload-notification**, che riceve le notifiche di caricamento file dall'hub IoT.

> [!NOTE]
> L'hub IoT supporta numerose piattaforme e linguaggi (inclusi C, .NET e Javascript) tramite gli Azure IoT SDK per dispositivi. Vedere il [Centro per sviluppatori di IoT di Azure](https://azure.microsoft.com/develop/iot) per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Java SE Development Kit 8](https://aka.ms/azure-jdks) più recente

* [Maven 3](https://maven.apache.org/install.html)

* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene modificata l'app per dispositivi creata in [Inviare messaggi da cloud a dispositivo con l'hub IoT](iot-hub-java-java-c2d.md) per caricare un file nell'hub IoT.

1. Copiare un file di immagine nella cartella `simulated-device` e rinominarlo `myimage.png`.

2. Aprire il file `simulated-device\src\main\java\com\mycompany\app\App.java` in un editor di testo.

3. Aggiungere la dichiarazione di variabile alla classe **App**:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Per elaborare i messaggi di richiamata dello stato di caricamento del file, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Per caricare le immagini nell'hub IoT, aggiungere il metodo seguente alla classe **App**:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Modificare il metodo **main** per chiamare il metodo **uploadFile** come illustrato nel frammento di codice seguente:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Usare il comando seguente per compilare l'app **simulated-device** e verificare la presenza di eventuali errori:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione viene creata un'app console Java che riceve messaggi di notifica di caricamento file dall'hub IoT.

Per completare questa sezione è necessaria la stringa di connessione **iothubowner**. È possibile trovare la stringa di connessione nel [portale di Azure](https://portal.azure.com/) nel pannello **Criteri di accesso condiviso**.

1. Creare un progetto Maven denominato **read-file-upload-notification** usando questo comando al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella `read-file-upload-notification`.

3. In un editor di testo aprire il file `pom.xml` nella cartella `read-file-upload-notification` e aggiungere la dipendenza seguente al nodo **dependencies**. L'aggiunta della dipendenza consente di usare il pacchetto **iothub-java-service-client** nell'applicazione per comunicare con il servizio hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Salvare e chiudere il file `pom.xml`.

5. Aprire il file `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` in un editor di testo.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App** :

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Per stampare le informazioni relative al caricamento del file nella console, aggiungere la classe annidata seguente alla classe **App**:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Per avviare il thread che rimane in ascolto delle notifiche di caricamento del file, aggiungere il codice seguente al metodo **main**:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Salvare e chiudere il file `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

11. Usare il comando seguente per compilare l'app **read-file-upload-notification** e verificare la presenza di eventuali errori:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

Al prompt dei comandi nella cartella `read-file-upload-notification` eseguire il comando seguente:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Al prompt dei comandi nella cartella `simulated-device` eseguire il comando seguente:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Lo screenshot seguente presenta l'output dell'app **simulated-device**:

![Output dell'app simulated-device](media/iot-hub-java-java-upload/simulated-device.png)

Lo screenshot seguente presenta l'output dell'app **read-file-upload-notification**:

![Output dell'app read-file-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

![File caricato](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)
* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)
* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)