---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Node) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure tramite Azure IoT SDK per Node. js. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 35acc1448b662a9b0c08e9d1f91886903444bcb8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620064"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Node)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Il [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) Guida introduttiva illustra come creare un hub IoT, eseguire il provisioning di un'identità del dispositivo in esso e codice di un'app dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sul [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md). Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione, chiedere una conferma di recapito (*commenti e suggerimenti*) per i messaggi inviati a un dispositivo dall'IoT Hub.

È possibile trovare altre informazioni sui messaggi da cloud a dispositivo, vedere la [Guida per gli sviluppatori dell'IoT Hub](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, vengono eseguite due app console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata nel [inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo all'app del dispositivo simulato tramite l'IoT Hub e riceve quindi relativo acknowledgment di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot).
>

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node. js versione 10.0 o versione successiva.
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial) in pochi minuti.

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione, si modifica l'app per dispositivo simulato creata nel [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.

1. Con un editor di testo aprire il file SimulatedDevice.js.

2. Modificare la funzione **connectCallback** per gestire i messaggi inviati dall'hub IoT. In questo esempio il dispositivo richiama sempre la funzione **complete** per notificare all'hub IoT Hub che ha elaborato il messaggio. La nuova versione della funzione **connectCallback** è simile al frammento seguente:

    ```javascript
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
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Se si usa HTTPS invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto MQTT, AMQP e HTTPS e la limitazione dell'Hub IoT, vedere la [Guida per gli sviluppatori dell'IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID dispositivo del dispositivo aggiunto nel [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) Guida introduttiva. È necessaria anche la stringa di connessione per l'hub IoT, disponibile nel [Portale di Azure](https://portal.azure.com).

1. Creare una cartella vuota denominata **sendcloudtodevicemessage**. Nella cartella **sendcloudtodevicemessage** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```shell
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **sendcloudtodevicemessage** per installare il pacchetto **azure-iothub**:

    ```shell
    npm install azure-iothub --save
    ```

3. Con un editor di testo, creare un file **SendCloudToDeviceMessage.js** nella cartella **sendcloudtodevicemessage**.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **SendCloudToDeviceMessage.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire il valore del segnaposto "{iot hub connection string}" con la stringa di connessione dell'IoT Hub per l'hub creato durante la [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) Guida introduttiva. Sostituire il segnaposto "{id dispositivo}" con l'ID dispositivo del dispositivo aggiunto nel [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) Guida introduttiva:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente per stampare i risultati dell'operazione sulla console:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Aggiungere la funzione seguente per stampare i messaggi di feedback del recapito sulla console:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Aggiungere il codice seguente per inviare un messaggio al dispositivo e gestire il messaggio di feedback quando il dispositivo accetta il messaggio da cloud a dispositivo:

    ```javascript
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

9. Salvare e chiudere il file **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella **simulateddevice** eseguire questo comando per inviare i dati di telemetria all'hub IoT e per rimanere in ascolto dei messaggi da cloud a dispositivo:

    ```shell
    node SimulatedDevice.js
    ```

    ![Eseguire un'app di dispositivo simulato](./media/iot-hub-node-node-c2d/receivec2d.png)

2. A un prompt dei comandi nella cartella **sendcloudtodevicemessage** eseguire questo comando per inviare un messaggio da cloud a dispositivo e attendere il feedback di acknowledgment:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Eseguire l'app per inviare il comando da cloud a dispositivo](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
