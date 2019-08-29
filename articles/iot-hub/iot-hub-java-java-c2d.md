---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Java) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure usando Azure IoT SDK per Java. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 4d9a73ee9fb9e4019d30354e6d9fbbe57d081e33
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147511"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md) Internet viene illustrato come creare un hub Internet, effettuare il provisioning di un'identità del dispositivo e codificare un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sull' [invio di dati di telemetria da un dispositivo a un hub](quickstart-send-telemetry-java.md)Internet. Illustra come eseguire le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, richiedere il riconoscimento del recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub Internet.

Altre informazioni sui [messaggi da cloud a dispositivo sono disponibili nella guida per sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, vengono eseguite due app console Java:

* **Simulated-Device**, una versione modificata dell'app creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md)Internet, che si connette all'hub Internet e riceve messaggi da cloud a dispositivo.

* **Send-C2D-messages**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub Internet e quindi riceve la conferma di recapito.

> [!NOTE]
> L'hub di Internet delle cose dispone del supporto SDK per molte piattaforme e linguaggi del dispositivo (inclusi C, Java, Python e JavaScript) tramite gli SDK per dispositivi Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Prerequisiti

* Una versione funzionante completa della Guida introduttiva per l'invio di dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md) Internet o l'esercitazione [configurare il routing dei messaggi con l'hub](tutorial-routing.md) .

* [Java SE Development Kit 8](https://aka.ms/azure-jdks) più recente

* [Maven 3](https://maven.apache.org/install.html)

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione si modifica l'app per dispositivo simulato creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md) Internet per ricevere messaggi da cloud a dispositivo dall'hub Internet.

1. Usando un editor di testo, aprire il file simulated-device\src\main\java\com\mycompany\app\App.java.

2. Aggiungere la seguente classe **MessageCallback** come classe annidata all'interno della classe **App**. Il metodo **execute** viene richiamato quando il dispositivo riceve un messaggio dall'hub IoT. In questo esempio, il dispositivo notifica sempre all'hub IoT di aver completato il messaggio:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificare il metodo **main** per creare un'istanza di **AppMessageCallback** e chiamare il metodo **setMessageCallback** prima dell'apertura del client, come indicato di seguito:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Se si usa HTTPS invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere la [sezione di messaggistica della guida per sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

4. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub Internet delle cose creato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md). Per inviare messaggi da cloud a dispositivo, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Java che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID dispositivo del dispositivo aggiunto nella Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-java.md) . È necessaria anche la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

1. Creare un progetto Maven denominato **send-c2d-messages** eseguendo questo comando al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella send-c2d-messages.

3. In un editor di testo aprire il file pom.xml nella cartella send-c2d-messages e aggiungere la dipendenza seguente al nodo **dependencies** . L'aggiunta della dipendenza consente di usare il pacchetto **iothub-java-service-client** nell'applicazione per comunicare con il servizio hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > È possibile cercare la versione più recente di **iot-service-client** usando la [ricerca di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Salvare e chiudere il file pom.xml.

5. In un editor di testo aprire il file send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **app** , sostituendo **{yourhubconnectionstring}** e **{yourdeviceid}** con i valori annotati in precedenza:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Sostituire il metodo **main** con il codice seguente. Il codice seguente si connette all'hub IoT, invia un messaggio al dispositivo e quindi attende un riconoscimento che il dispositivo ha ricevuto ed elaborato il messaggio:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

9. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella simulated-device eseguire questo comando per iniziare a inviare i dati di telemetria all'hub IoT e rimanere in ascolto dei messaggi da cloud a dispositivo inviati dall'hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire un'app di dispositivo simulato](./media/iot-hub-java-java-c2d/receivec2d.png)

2. A un prompt dei comandi nella cartella send-c2d-messages eseguire questo comando per inviare un messaggio da cloud a dispositivo e attendere un acknowledgment di feedback:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire il comando per inviare il messaggio dal cloud al dispositivo](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per esempi di soluzioni complete che usano l'hub IoT, vedere [Acceleratori di soluzioni di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
