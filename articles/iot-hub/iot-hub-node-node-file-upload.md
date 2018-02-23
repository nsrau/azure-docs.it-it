---
title: Caricare file da dispositivi nell'hub IoT di Azure con Node | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Node.js. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo
ms.openlocfilehash: cff0f2fc664e0c09bfa1f8f0e0d488a049a6f448
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Caricare file da un dispositivo al cloud con l'hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice contenuto nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT](iot-hub-node-node-c2d.md) e illustra come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file in [Archiviazione BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

- Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.
- Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

Le esercitazioni [Introduzione all'hub IoT](iot-hub-node-node-getstarted.md) e [Inviare messaggi da cloud a dispositivo con l'hub IoT](iot-hub-node-node-c2d.md) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Ad esempio: 

* File di grandi dimensioni che contengono immagini
* Video
* Dati di vibrazione campionati ad alta frequenza
* Qualche tipo di dati pre-elaborati.

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.md). Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

Al termine di questa esercitazione, verranno eseguite due app console Node.js:

* **SimulatedDevice.js**, che carica un file nell'archiviazione usando un URI di firma di accesso condiviso fornito dall'hub IoT.
* **ReadFileUploadNotification.js**, che riceve le notifiche di caricamento file dall'hub IoT.

> [!NOTE]
> L'hub IoT supporta molte piattaforme e linguaggi, tra cui C, .NET, Javascript, Python e Java, tramite gli Azure IoT SDK per dispositivi. Vedere il [Centro per sviluppatori di IoT di Azure] per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 4.0.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene creata l'app del dispositivo per caricare un file nell'hub IoT.

1. Creare una cartella vuota denominata ```simulateddevice```.  Nella cartella ```simulateddevice``` creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

1. Eseguire questo comando al prompt dei comandi nella cartella ```simulateddevice``` per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Con un editor di testo creare un file **SimulatedDevice.js** nella cartella ```simulateddevice```.

1. Aggiungere le istruzioni ```require``` seguenti all'inizio del file **SimulatedDevice.js** :

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Aggiungere una variabile ```deviceconnectionstring``` e usarla per creare un'istanza **Client**.  Sostituire ```{deviceconnectionstring}``` con il nome del dispositivo creato nella sezione _Creare un hub IoT_:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Per scopi di semplicità, la stringa di connessione è inclusa nel codice. Questa non è una procedura consigliata e a seconda del caso d'uso e dell'architettura potrebbe essere necessario prendere in considerazione modalità più sicure per l'archiviazione del segreto.

1. Aggiungere il codice seguente per connettere il client:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Creare un callback e usare la funzione **uploadToBlob** per caricare il file.

    ```nodejs
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

1. Salvare e chiudere il file **SimulatedDevice.js** .

1. Copiare un file di immagine nella cartella `simulateddevice` e rinominarlo `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione viene creata un'app console Node.js che riceve messaggi di notifica di caricamento file dall'hub IoT.

Per completare questa sezione, è possibile usare la stringa di connessione **iothubowner** dall'hub IoT. La stringa di connessione è disponibile nel [portale di Azure](https://portal.azure.com/) nel pannello **Criteri di accesso condiviso**.

1. Creare una cartella vuota denominata ```fileuploadnotification```.  Nella cartella ```fileuploadnotification``` creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

1. Al prompt dei comandi nella cartella ```fileuploadnotification``` eseguire il comando seguente per installare il pacchetto SDK **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Usando un editor di testo creare un file **FileUploadNotification.js** nella cartella ```fileuploadnotification```.

1. Aggiungere le istruzioni ```require``` seguenti all'inizio del file **FileUploadNotification.js**:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Aggiungere una variabile ```iothubconnectionstring``` e usarla per creare un'istanza **Client**.  Sostituire ```{iothubconnectionstring}``` con la stringa di connessione per l'hub IoT creata nella sezione _Creare un hub IoT_:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Per scopi di semplicità, la stringa di connessione è inclusa nel codice. Questa non è una procedura consigliata e a seconda del caso d'uso e dell'architettura potrebbe essere necessario prendere in considerazione modalità più sicure per l'archiviazione del segreto.

1. Aggiungere il codice seguente per connettere il client:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Aprire il client e usare la funzione **getFileNotificationReceiver** per ricevere aggiornamenti sullo stato.

    ```nodejs
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

1. Salvare e chiudere il file **FileUploadNotification.js**.

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

Lo screenshot seguente mostra l'output dell'app **SimulatedDevice**:

![Output dell'app simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

Lo screenshot seguente mostra l'output dell'app **FileUploadNotification**:

![Output dell'app read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

![File caricato](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice][lnk-create-hub]
* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

<!-- Links -->
[Centro per sviluppatori di IoT di Azure]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
