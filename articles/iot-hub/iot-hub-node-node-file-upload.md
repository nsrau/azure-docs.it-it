---
title: Caricare file da dispositivi nell'hub IoT di Azure con Node | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Node.js. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732240"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Caricare file dal dispositivo al cloud con L'hub IoT (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice nell'esercitazione Invia messaggi da cloud a dispositivo con hub IoT per mostrare come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file nell'archiviazione BLOB di Azure.This tutorial builds on the code in the Send [cloud-to-device messages with IoT Hub](iot-hub-node-node-c2d.md) tutorial to show you how to use the file upload capabilities of IoT Hub to upload a file to Azure blob [storage](../storage/index.yml). L'esercitazione illustra come:

* Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.

* Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

La guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md) illustra la funzionalità di base di messaggistica da dispositivo a cloud dell'hub IoT.The Send telemetry from a device to an IoT hub quickstart demonstrates the basic device-to-cloud messaging functionality of IoT Hub. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Ad esempio:

* File di grandi dimensioni che contengono immagini
* Video
* Dati di vibrazione campionati ad alta frequenza
* Una qualche forma di dati pre-elaborati.

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.yml). Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

Al termine di questa esercitazione, verranno eseguite due app console Node.js:

* **SimulatedDevice.js**, che carica un file nell'archiviazione usando un URI di firma di accesso condiviso fornito dall'hub IoT.

* **ReadFileUploadNotification.js**, che riceve le notifiche di caricamento file dall'hub IoT.

> [!NOTE]
> L'hub IoT supporta molte piattaforme e linguaggi, tra cui C, .NET, Javascript, Python e Java, tramite gli Azure IoT SDK per dispositivi. Fare riferimento a [Azure IoT Developer Center] per istruzioni dettagliate su come connettere il dispositivo all'hub IoT di Azure.Refer to the [Azure IoT Developer Center] for step-by-step instructions on how to connect your device to Azure IoT Hub.

## <a name="prerequisites"></a>Prerequisiti

* Node.js versione 10.0.x o successiva. [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene creata l'app del dispositivo per caricare un file nell'hub IoT.

1. Creare una cartella vuota denominata ```simulateddevice```.  Nella cartella ```simulateddevice``` creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella ```simulateddevice``` per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Con un editor di testo creare un file **SimulatedDevice.js** nella cartella ```simulateddevice```.

4. Aggiungere le istruzioni ```require``` seguenti all'inizio del file **SimulatedDevice.js** :

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Aggiungere una variabile `deviceconnectionstring` e usarla per creare un'istanza **Client**.  Sostituire `{deviceconnectionstring}` con il nome del dispositivo creato nella sezione *Creare un hub IoT*:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Per scopi di semplicità, la stringa di connessione è inclusa nel codice. Questa non è una procedura consigliata e a seconda del caso d'uso e dell'architettura potrebbe essere necessario prendere in considerazione modalità più sicure per l'archiviazione del segreto.

6. Aggiungere il codice seguente per connettere il client:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Creare un callback e usare la funzione **uploadToBlob** per caricare il file.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Salvare e chiudere il file **SimulatedDevice.js** .

9. Copiare un file di immagine nella cartella `simulateddevice` e rinominarlo `myimage.png`.

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

In questo articolo viene creato un servizio back-end per ricevere messaggi di notifica di caricamento file dall'hub IoT creato in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md). Per ricevere i messaggi di notifica di caricamento dei file, il servizio deve disporre dell'autorizzazione di connessione del **servizio.** Per impostazione predefinita, ogni hub IoT viene creato con un criterio di accesso condiviso denominato **servizio** che concede questa autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione viene creata un'app console Node.js che riceve messaggi di notifica di caricamento file dall'hub IoT.

Per completare questa sezione, è possibile usare la stringa di connessione **iothubowner** dall'hub IoT. La stringa di connessione è disponibile nel [portale di Azure](https://portal.azure.com/) nel pannello **Criteri di accesso condiviso**.

1. Creare una cartella vuota denominata ```fileuploadnotification```.  Nella cartella ```fileuploadnotification``` creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

2. Al prompt dei comandi nella cartella ```fileuploadnotification``` eseguire il comando seguente per installare il pacchetto SDK **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando un editor di testo creare un file **FileUploadNotification.js** nella cartella `fileuploadnotification`.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **FileUploadNotification.js**:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Aggiungere una variabile `iothubconnectionstring` e usarla per creare un'istanza **Client**.  Sostituire `{iothubconnectionstring}` il valore segnaposto con la stringa di connessione dell'hub IoT copiata in precedenza in Ottenere la stringa di [connessione dell'hub IoT:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Per scopi di semplicità, la stringa di connessione è inclusa nel codice. Questa non è una procedura consigliata e a seconda del caso d'uso e dell'architettura potrebbe essere necessario prendere in considerazione modalità più sicure per l'archiviazione del segreto.

6. Aggiungere il codice seguente per connettere il client:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Aprire il client e usare la funzione **getFileNotificationReceiver** per ricevere aggiornamenti sullo stato.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Salvare e chiudere il file **FileUploadNotification.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

Al prompt dei comandi nella cartella `fileuploadnotification` eseguire il comando seguente:

```cmd/sh
node FileUploadNotification.js
```

Al prompt dei comandi nella cartella `simulateddevice` eseguire il comando seguente:

```cmd/sh
node SimulatedDevice.js
```

La schermata seguente mostra l'output dell'app **SimulatedDevice:The** following screenshot shows the output from the SimulatedDevice app:

![Output dell'app simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

Lo screenshot seguente mostra l'output dell'app **FileUploadNotification**:

![Output dell'app read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

![File caricato](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
