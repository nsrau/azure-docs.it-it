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
ms.openlocfilehash: d3e4e0f4e7b1f8d3e100b3f1b3446907cfd587c5
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716948"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Inviare messaggi da cloud a dispositivo con l'hub Internet (node. js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md) Internet viene illustrato come creare un hub Internet, effettuare il provisioning di un'identità del dispositivo e codificare un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sull' [invio di dati di telemetria da un dispositivo a un hub](quickstart-send-telemetry-node.md)Internet. Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione, richiedere il riconoscimento del recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub Internet.

È possibile trovare altre informazioni sui messaggi da cloud a dispositivo nella Guida per gli [sviluppatori dell'hub](iot-hub-devguide-messaging.md)Internet.

Al termine di questa esercitazione, vengono eseguite due app console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md)Internet, che si connette all'hub Internet e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub Internet e quindi riceve la conferma di recapito.

> [!NOTE]
> L'hub di Internet delle cose dispone del supporto SDK per molte piattaforme e linguaggi del dispositivo (inclusi C, Java, Python e JavaScript) tramite gli SDK per dispositivi Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot).
>

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node. js versione 10.0. x o successiva.
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial) in pochi minuti.

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione si modifica l'app per dispositivo simulato creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md) Internet per ricevere messaggi da cloud a dispositivo dall'hub Internet.

1. Usando un editor di testo, aprire il file **SimulatedDevice. js** . Questo file si trova nella cartella **IOT-hub\Quickstarts\simulated-Device** dalla cartella radice del codice di esempio node. js scaricato nella Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md) .

2. Registrare un gestore con il client del dispositivo per ricevere i messaggi inviati dall'hub. Aggiungere la chiamata a `client.on` subito dopo la riga che crea il client del dispositivo come nel frammento di codice seguente:

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

    In questo esempio, il dispositivo richiama la funzione **complete** per inviare una notifica all'hub cose che ha elaborato il messaggio. La chiamata a **complete** non è obbligatoria se si usa il trasporto MQTT e può essere omesso. È obbligatorio per HTTPS e AMQP.
  
   > [!NOTE]
   > Se si usa HTTPS invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e la limitazione dell'hub Internet, vedere la guida per gli [sviluppatori dell'hub](iot-hub-devguide-messaging.md)Internet.
   >

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub Internet delle cose creato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md). Per inviare messaggi da cloud a dispositivo, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID dispositivo del dispositivo aggiunto nella Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-node.md) . È necessaria anche la stringa di connessione dell'hub Internet delle cose copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

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

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire i valori segnaposto "{String Hub Connection String}" e "{ID dispositivo}" con la stringa di connessione dell'hub Internet e l'ID del dispositivo annotati in precedenza:

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

1. Al prompt dei comandi nella cartella **Simulated-Device** eseguire il comando seguente per inviare dati di telemetria all'hub Internet e per restare in ascolto dei messaggi da cloud a dispositivo:

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
