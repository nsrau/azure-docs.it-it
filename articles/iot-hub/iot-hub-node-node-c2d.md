---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Node) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure tramite Azure IoT SDK per Node. js. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 4580bda5633f84a7c7af0dc85f3cea4951024836
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. L'esercitazione [Introduzione all'hub IoT di Azure] illustra come creare un hub IoT, effettuare il provisioning dell'identità di un dispositivo al suo interno e creare il codice di un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione si basa su [Introduzione all'hub IoT di Azure]. Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione richiedere l'acknowledgement di recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub IoT.

Per altre informazioni sui messaggi da cloud a dispositivo, vedere la[Guida per sviluppatori dell'hub IoT][IoT Hub developer guide - C2D].

Al termine di questa esercitazione, vengono eseguite due app console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata in [Introduzione all'hub IoT di Azure], che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.
* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub IoT e riceve quindi l'acknowledgement di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT].
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 0.10.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato
In questa sezione si modificherà l'app per il dispositivo simulato creata in [Introduzione all'hub IoT di Azure] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

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
   > Se si usa HTTP/25 invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTP e sulla limitazione delle richieste dell'hub IoT, vedere [Guida per sviluppatori dell'hub IoT][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo
In questa sezione si crea un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure] . È necessaria anche la stringa di connessione per l'hub IoT, disponibile nel [portale di Azure].

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
5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire il valore del segnaposto "{iot hub connection string}" con la stringa di connessione dell'hub IoT creato durante l'esercitazione [Introduzione all'hub IoT di Azure]. Sostituire il segnaposto "{device id}" con l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure]:
   
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
   
    ![Eseguire un'app di dispositivo simulato][img-simulated-device]
2. A un prompt dei comandi nella cartella **sendcloudtodevicemessage** eseguire questo comando per inviare un messaggio da cloud a dispositivo e attendere il feedback di acknowledgment:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Eseguire l'app per inviare il comando da cloud a dispositivo][img-send-command]
   
   > [!NOTE]
   > Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling](Gestione degli errori temporanei).
   > 
   > 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo. 

Per avere degli esempi di soluzioni complete che utilizzano l'hub IoT, vedere la [Azure IoT Suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introduzione all'hub IoT di Azure]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portale di Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

