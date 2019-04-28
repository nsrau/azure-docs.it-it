---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (Node.js) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 312d3abad2ee2c9e668f8b354aaba96f8a652698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626201"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT usando il back-end Node.js e i dispositivi Node.js

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.

Al termine di questa esercitazione si avranno due app Node.js:

* **CreateIdentities**, che crea un'identità del dispositivo, un'identità del modulo e una chiave di sicurezza associata per connettere i client dispositivo e modulo.

* **UpdateModuleTwinReportedProperties**, che invia le proprietà segnalate aggiornate del modulo gemello all'hub IoT.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.
* Un hub IoT.
* Installare la versione di [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node) più recente.

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione dell'hub necessari per completare il resto di questa esercitazione.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Creare un'identità del dispositivo e un'identità del modulo nell'hub IoT

In questa sezione si scriverà un'app Node.js che crea un'identità del dispositivo e un'identità del modulo nel registro delle identità dell'hub IoT. Un dispositivo o un modulo non può connettersi all'hub IoT se non ha una voce nel registro delle identità. Per altre informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'IoT Hub](iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.

1. Creare una directory per il codice.

2. All'interno di tale directory, eseguire prima di tutto  **npm init -y** per creare un file package.json vuoto con i valori predefiniti. Questo è il file di progetto per il codice.

3. Eseguire **npm installare azure-iothub - S\@-anteprima dei moduli** per installare il SDK del servizio all'interno di **node_modules** sottodirectory.

    > [!NOTE]
    > Nel nome di sottodirectory node_modules, il riferimento a modulo viene usato con il significato di "libreria del nodo". In questo contesto il termine non ha nulla a che fare con i moduli dell'hub IoT.

4. Creare il file con estensione js seguente nella directory. Chiamarlo **add.js**. Copiare e incollare la stringa di connessione dell'hub e il nome dell'hub.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Questa app crea un'identità del dispositivo con ID **myFirstDevice** e un'identità del modulo con ID **myFirstModule** per il dispositivo **myFirstDevice**. Se questo ID modulo è già presente nel registro delle identità, il codice recupera semplicemente le informazioni esistenti sul modulo. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dall'app per modulo simulato per connettersi all'hub IoT.

Eseguire questa operazione usando node add.js. Si otterrà una stringa di connessione per l'identità del dispositivo e un'altra per l'identità del modulo.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi Node.js

In questa sezione, nel dispositivo simulato viene creata un'app Node.js che aggiorna le proprietà restituite del modulo gemello.

1. **Ottenere la stringa di connessione del modulo** -Accedi per il [portale di Azure](https://portal.azure.com/). Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

   ![Dettagli del modulo nel portale di Azure](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. È simile all'utente nel passaggio precedente, creare una directory per il codice del dispositivo e usare NPM per inizializzarla e installare il SDK per dispositivi (**npm installare -S azure-iot-device-amqp\@-anteprima dei moduli**).

   > [!NOTE]
   > L'esecuzione del comando npm install potrebbe sembrare lenta. Attendere pazientemente perché è richiesto il pull di molto codice dal repository dei pacchetti.

   > [!NOTE]
   > Se viene visualizzato un messaggio di errore per NPM che segnala un errore del Registro di sistema durante l'analisi JSON, è possibile ignorarlo. Se viene visualizzato un messaggio di errore per NPM che segnala un errore del Registro di sistema durante l'analisi JSON, è possibile ignorarlo.

3. Creare un file denominato twin.js. Copiare e incollare la stringa di identità del modulo.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. A questo punto, eseguire il comando  **node twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)