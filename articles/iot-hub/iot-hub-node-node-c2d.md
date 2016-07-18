<properties
	pageTitle="Inviare messaggi da cloud a dispositivo con l’hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per informazioni su come inviare messaggi da cloud a dispositivo usando l'hub IoT Azure con Java."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/04/2016"
     ms.author="dobett"/>

# Esercitazione: Inviare messaggi da cloud a dispositivo con l'hub IoT e Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. L’esercitazione [Introduzione all’hub IoT] illustra come creare un hub IoT, eseguire il provisioning dell’identità di un dispositivo al suo interno e creare il codice di un dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione si basa su [Introduzione all'hub IoT di Azure per Node.js]. Illustra le operazioni seguenti:

- Dal back-end cloud dell'applicazione, inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
- Ricevere messaggi da cloud a dispositivo in un dispositivo.
- Dal back-end cloud dell'applicazione, richiedere l'acknowledgement di recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub IoT.

È possibile trovare ulteriori informazioni sui messaggi da cloud a dispositivo nella [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione si eseguiranno due applicazioni console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata in [Introduzione all’hub IoT], che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.
* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo al dispositivo simulato tramite l'hub IoT e riceve quindi l'acknowledgement di recapito.

> [AZURE.NOTE] L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ Node. js 0.12.x o versione successiva. <br/> [Prepare your development environment][lnk-dev-setup] \(Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Crea subito il tuo account Azure gratuito][lnk-free-trial].

## Ricezione di messaggi sul dispositivo simulato

In questa sezione si modificherà l'applicazione del dispositivo simulato creata in [Introduzione all'hub IoT di Azure per Node.js] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. Con un editor di testo aprire il file SimulatedDevice.js.

2. Modificare la funzione **connectCallback** per gestire i messaggi inviati dall'hub IoT. In questo esempio il dispositivo richiama sempre la funzione **complete** per notificare all'hub IoT Hub che ha elaborato il messaggio. La nuova versione della funzione **connectCallback** è simile alla seguente:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Se si usa HTTP/1 invece di AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di AMQP e di HTTP/1 e sulla limitazione delle richieste dell'hub IoT, vedere [Guida per gli sviluppatori dell'hub IoT di Azure][IoT Hub Developer Guide - C2D].

## Inviare un messaggio da cloud a dispositivo dal back-end dell'app

In questa sezione si creerà un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure per Java] e la stringa di connessione per l'hub IoT che si trova nel [portale di Azure].

1. Creare una nuova cartella vuota denominata **sendcloudtodevicemessage**. Nella cartella **sendcloudtodevicemessage** creare un nuovo file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **sendcloudtodevicemessage** per installare il pacchetto **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Con un editor di testo creare un nuovo file **SendCloudToDeviceMessage.js** nella cartella **sendcloudtodevicemessage**.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **SendCloudToDeviceMessage.js**:

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** e sostituire il valore di segnaposto della stringa di connessione con la stringa di connessione per l'hub IoT creato nell'esercitazione [Introduzione all'hub IoT di Azure per Node.js] il segnaposto del dispositivo di destinazione con l'ID dispositivo del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure per Node.js]\:

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente per stampare i risultati dell'operazione sulla console:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Aggiungere la funzione seguente per stampare i messaggi di feedback del recapito sulla console:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Aggiungere il codice seguente per inviare un messaggio al dispositivo e gestire il messaggio di feedback quando il dispositivo accetta il messaggio da cloud a dispositivo:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Salvare e chiudere il file **SendCloudToDeviceMessage.js**.

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella **simulateddevice** eseguire il comando seguente per inviare i dati di telemetria all'hub IoT e rimanere in ascolto dei messaggi da cloud a dispositivo:

    ```
    node SimulatedDevice.js 
    ```

    ![][img-simulated-device]

2. A un prompt dei comandi nella cartella **sendcloudtodevicemessage** eseguire il comando seguente per inviare un messaggio da cloud a dispositivo e attendere il feedback di acknowledgment:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![][img-send-command]

    > [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] \(Gestione degli errori temporanei).

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

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]: media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introduzione all'hub IoT di Azure per Java]: iot-hub-node-node-getstarted.md
[Introduzione all'hub IoT di Azure per Node.js]: iot-hub-node-node-getstarted.md
[Introduzione all’hub IoT]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Elaborare messaggi da dispositivo a cloud dell'hub IoT]: iot-hub-csharp-csharp-process-d2c.md
[Caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md
[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[Piattaforme e linguaggi di dispositivi supportati]: iot-hub-supported-devices.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portale di Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->