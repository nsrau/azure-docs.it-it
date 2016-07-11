<properties
	pageTitle="Inviare messaggi da cloud a dispositivo con l’hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per informazioni su come inviare messaggi da cloud a dispositivo usando l'hub IoT Azure con Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="dobett"/>

# Esercitazione: Inviare messaggi da cloud a dispositivo con l'hub IoT e Java

[AZURE.INCLUDE iot-hub-selector-c2d]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. L’esercitazione [Introduzione all’hub IoT] illustra come creare un hub IoT, eseguire il provisioning dell’identità di un dispositivo al suo interno e creare il codice di un dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione si basa su [Introduzione all'hub IoT di Azure]. e illustra come inviare messaggi da cloud a dispositivo a un unico dispositivo, richiedere la conferma del recapito (*feedback*) dall'hub IoT e riceverla dal back-end cloud dell'applicazione.

È possibile trovare ulteriori informazioni sui messaggi da cloud a dispositivo nella [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione si eseguiranno due applicazioni console Java:

* **simulated-device**, una versione modificata dell'app creata in [Introduzione all'hub IoT di Azure], che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.
* **send-c2d-messages**, che invia un messaggio da cloud a dispositivo al dispositivo simulato tramite l'hub IoT e riceve quindi la conferma del recapito.

> [AZURE.NOTE] L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Java per questa esercitazione in Windows o Linux.

+ Maven 3. <br/> [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Maven per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Crea subito il tuo account Azure gratuito][lnk-free-trial].

## Ricezione di messaggi sul dispositivo simulato

In questa sezione si modificherà l'applicazione del dispositivo simulato creata in [Introduzione all'hub IoT di Azure] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. Usando un editor di testo, aprire il file simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Aggiungere la seguente classe **MessageCallback** come classe annidata all'interno della classe **App**. Il metodo **execute** viene richiamato quando il dispositivo riceve un messaggio dall'hub IoT. In questo esempio il dispositivo notifica sempre all'hub di aver completato il messaggio:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificare il metodo **main** per creare un'istanza di **MessageCallback** e chiamare il metodo **setMessageCallback** prima dell'apertura del client, come indicato di seguito:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Se si usa HTTP/1 invece di AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di AMQP e di HTTP/1 e sulla limitazione delle richieste dell'hub IoT, vedere [Guida per gli sviluppatori dell'hub IoT di Azure][IoT Hub Developer Guide - C2D].

## Inviare un messaggio da cloud a dispositivo dal back-end dell'app

In questa sezione si creerà un'app console Java che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure] e la stringa di connessione per l'hub IoT che si trova in

1. Creare un nuovo progetto Maven denominato **send-c2d-messages** usando il comando seguente al prompt dei comandi. Si noti che si tratta di un lungo comando singolo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella send-c2d-messages.

3. In un editor di testo aprire il file pom.xml nella cartella send-c2d-messages e aggiungere la dipendenza seguente al nodo **dependencies**. Ciò consente di usare il pacchetto **iothub-java-service-client** nell'applicazione per comunicare con il servizio hub IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. In un editor di testo aprire il file send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Aggiungere al file le istruzioni **import** seguenti:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le variabili a livello di classe seguenti alla classe **App**, sostituendo **{yourhubconnectionstring}** e **{yourdeviceid}** con i valori annotati prima:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. Sostituire il metodo **main** con il codice seguente che si connette all'hub IoT, invia un messaggio al dispositivo e quindi attende un riconoscimento che il dispositivo ha ricevuto ed elaborato il messaggio:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
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

    > [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] (Gestione degli errori temporanei).

## Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT con le esercitazioni seguenti:

- [Elaborare messaggi da dispositivo a cloud dell'hub IoT] illustra come elaborare in modo affidabile dati di telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricamento di file da dispositivi] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati]
* [Centro per sviluppatori Azure IoT]

<!-- Links -->

[Introduzione all'hub IoT di Azure]: iot-hub-java-java-getstarted.md
[Introduzione all’hub IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Elaborare messaggi da dispositivo a cloud dell'hub IoT]: iot-hub-csharp-csharp-process-d2c.md
[Caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md
[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[Piattaforme e linguaggi di dispositivi supportati]: iot-hub-supported-devices.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0629_2016-->