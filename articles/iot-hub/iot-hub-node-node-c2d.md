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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732347"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Inviare messaggi da cloud a dispositivo con hub IoT (Node.js)Send cloud-to-device messages with IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) mostra come creare un hub IoT, eseguire il provisioning di un'identità del dispositivo in esso e codificare un'app per dispositivi simulata che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa [sull'invio di dati di telemetria da un dispositivo a un hub IoT.This](quickstart-send-telemetry-node.md)tutorial builds on Send telemetry from a device to an IoT hub . Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione, richiedere il riconoscimento di recapito ( feedback ) per i messaggi inviati a un dispositivo dall'hub IoT.From your solution back-end, request delivery acknowledgment (*feedback*) for messages sent to a device from ioT Hub.

Ulteriori informazioni sui messaggi da cloud a dispositivo sono disponibili nella [guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, vengono eseguite due app console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata in [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo all'app per dispositivi simulata tramite l'hub IoT, quindi riceve il riconoscimento di recapito.

> [!NOTE]
> IoT Hub has SDK support for many device platforms and languages (including C, Java, Python, and Javascript) through Azure IoT device SDKs. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Prerequisiti

* Node.js versione 10.0.x o successiva. [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione viene modificato l'app per dispositivi simulata creata in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.In this section, you modify the simulated device app you created in Send telemetry from a device to an IoT hub to receive cloud-to-device messages from the IoT hub.

1. Utilizzando un editor di testo, aprire il file **SimulatedDevice.js.Using** a text editor, open the SimulatedDevice.js file. Questo file si trova nella cartella **iot-hub-Quickstarts-simulated-device** della cartella radice del codice di esempio Node.js scaricato nel messaggio di avvio rapido Invia dati di telemetria da un dispositivo a un [hub IoT.](quickstart-send-telemetry-node.md)

2. Registrare un gestore con il client del dispositivo per ricevere i messaggi inviati dall'hub IoT.Register a handler with the device client to receive messages sent from IoT Hub. Aggiungere la `client.on` chiamata a subito dopo la riga che crea il client del dispositivo come nel frammento di codice seguente:Add the call to just after the line that creates the device client as in the following snippet:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    In questo esempio, il dispositivo richiama la funzione **completa** per notificare all'hub IoT che ha elaborato il messaggio. La chiamata da **completare** non è necessaria se si utilizza il trasporto MQTT e può essere omessa. È necessario per HTTPS e AMQP.
  
   > [!NOTE]
   > Se si usa HTTPS invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere la [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub IoT creato in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md). Per inviare messaggi da cloud a dispositivo, il servizio deve disporre dell'autorizzazione di connessione del **servizio.** Per impostazione predefinita, ogni hub IoT viene creato con un criterio di accesso condiviso denominato **servizio** che concede questa autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID del dispositivo aggiunto nella guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT.You](quickstart-send-telemetry-node.md) need the device ID of the device you added in the Send telemetry from a device to an IoT hub quickstart. È inoltre necessaria la stringa di connessione dell'hub IoT copiata in precedenza in Ottenere la stringa di [connessione dell'hub IoT](#get-the-iot-hub-connection-string).

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

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire i valori segnaposto "stringa di connessione dell'hub iot" e "ID dispositivo" con la stringa di connessione dell'hub IoT e l'ID dispositivo annotato in precedenza:

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

1. Al prompt dei comandi nella cartella **del dispositivo simulato,** eseguire il comando seguente per inviare dati di telemetria all'hub IoT e per restare in ascolto dei messaggi da cloud a dispositivo:

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
