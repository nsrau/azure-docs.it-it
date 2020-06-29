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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732347"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. L'argomento di avvio rapido [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT) mostra come creare un hub IoT, eseguire il provisioning dell'identità di un dispositivo al suo interno e creare il codice di un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione è basata su [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione richiedere l'acknowledgement di recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub IoT.

Altre informazioni sui [messaggi da cloud a dispositivo sono disponibili nella guida per sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, vengono eseguite due app console Node.js:

* **SimulatedDevice**, una versione modificata dell'app creata in [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub IoT e riceve quindi l'acknowledgement di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi, tra cui C, Java, Python e Javascript, tramite Azure IoT SDK per dispositivi. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Prerequisiti

* Node.js 10.0.x o versione successiva. [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione si modificherà l'app di dispositivo simulato creata in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.

1. Con un editor di testo aprire il file **SimulatedDevice.js**. Questo file si trova nella cartella **iot-hub\Quickstarts\simulated-device** all'esterno della cartella radice del codice di esempio Node.js scaricato nell’argomento di avvio rapido [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT).

2. Registrare un gestore con il client del dispositivo per ricevere i messaggi inviati dall'hub IoT. Aggiungere la chiamata a `client.on` subito dopo la riga che crea il client del dispositivo come nel frammento di codice seguente:

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

    In questo esempio il dispositivo richiama la funzione **complete** per notificare all'hub IoT Hub che ha elaborato il messaggio. La chiamata a **complete** non è necessaria se si usa il trasporto MQTT e può essere omessa. È obbligatoria per HTTPS e AMQP.
  
   > [!NOTE]
   > Se si usa HTTPS invece di MQTT o AMQP per il trasporto, l'istanza di **DeviceClient** controlla raramente i messaggi provenienti dall'hub IoT (meno di 25 minuti). Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere la [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub IoT creato in [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). Per inviare messaggi da cloud a dispositivo, è necessario che il servizio disponga dell'autorizzazione di **connessione al servizio**. Per impostazione predefinita, ogni hub IoT viene creato con un **servizio** con nome di criteri di accesso condiviso che concede tale autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Node.js che invia messaggi da cloud a dispositivo all'app del dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell’argomento di avvio rapido [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). È necessaria anche la stringa di connessione dell'hub IoT copiata in precedenza in [Ottenere la stringa di connessione dell’hub IoT](#get-the-iot-hub-connection-string).

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

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire i valori segnaposto "{iot hub connection string}" e "{device id}" con la stringa di connessione dell'hub IoT e l'ID del dispositivo annotati in precedenza:

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
   > Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
