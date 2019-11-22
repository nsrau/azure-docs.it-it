---
title: "Esercitazione: Aggiungere un dispositivo reale a un'applicazione Azure IoT Central"
description: "Esercitazione: Informazioni su come aggiungere e configurare un dispositivo reale nell'applicazione Azure IoT Central. È anche possibile ottenere informazioni sul codice nel dispositivo reale."
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5234c371cc4a7238148915f4dcca1faac564f608
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108966"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Esercitazione: Aggiungere un dispositivo reale all'applicazione Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questa esercitazione illustra come aggiungere e configurare un *dispositivo reale* nell'applicazione Microsoft Azure IoT Central. In questa esercitazione il codice per il dispositivo reale verrà scritto usando Node.js e verrà eseguito in un computer desktop. Per completare questa esercitazione, non è necessario un dispositivo IoT separato, ad esempio un dispositivo Raspberry Pi o MXChip IoT DevKit.

Questa esercitazione è costituita da due parti:

* L'operatore apprenderà prima come aggiungere e configurare un dispositivo reale nell'applicazione Azure IoT Central. Al termine di questa parte, recupererà una stringa di connessione da usare nella seconda parte.
* Verranno quindi fornite informazioni per lo sviluppatore di dispositivi riguardanti il codice del dispositivo reale. La stringa di connessione recuperata nella prima parte verrà aggiunta al codice di esempio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un nuovo dispositivo reale
> * Configurare il dispositivo reale
> * Ottenere dall'applicazione la stringa di connessione per il dispositivo reale
> * Comprendere come eseguire il mapping del codice client all'applicazione
> * Configurare il codice client per il dispositivo reale

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, il generatore deve completare almeno la prima esercitazione per creare l'applicazione Azure IoT Central: [Definire un nuovo tipo di dispositivo](tutorial-define-device-type.md) (obbligatorio)

Installare [Node.js](https://nodejs.org/) versione 8.0.0 o successiva nel computer di sviluppo. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Per aggiungere un dispositivo reale all'applicazione, si usa il modello di dispositivo **Connected Air Conditioner** (Condizionatore d'aria connesso) creato nell'esercitazione [Definire un nuovo tipo di dispositivo](tutorial-define-device-type.md).

1. Per aggiungere un nuovo dispositivo in qualità di operatore, scegliere **Dispositivi** nel riquadro di sinistra:

   ![Pagina Device Explorer con Connected Air Conditioner (Condizionatore d'aria connesso)](media/tutorial-add-device/explorer.png)

   In **Esplora dispositivi** sono visualizzati il modello di dispositivo **Condizionatore connesso** e un dispositivo simulato. Quando si crea un modello di dispositivo, IoT Central crea automaticamente un dispositivo simulato.

2. Si noti che in **Esplora dispositivi** è selezionato il modello di dispositivo **Condizionatore d'aria connesso**. Per iniziare la connessione di un dispositivo condizionatore d'aria connesso reale che usa questo modello, selezionare **+** , quindi **Reale**:

   ![Iniziare ad aggiungere un nuovo dispositivo condizionatore d'aria connesso](media/tutorial-add-device/newreal.png)

3. Immettere un **ID dispositivo** (lettere minuscole) personalizzato o usare l'ID dispositivo suggerito. È anche possibile immettere un **Nome dispositivo** per il nuovo dispositivo e scegliere **Crea**.

   ![Rinominare il dispositivo](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Configurare un dispositivo reale

Il dispositivo reale viene creato sulla base del modello di dispositivo **Connected Air Conditioner** (Condizionatore d'aria connesso). È possibile usare **Impostazioni** per configurare il dispositivo e impostare i valori delle proprietà per registrare le informazioni sul dispositivo.

1. Nella pagina **Impostazioni** si noti che lo stato dell'impostazione **Set Temperature** (Temperatura impostata) indica **nessun aggiornamento**. Questo stato permane fino a quando il dispositivo reale non si connette all'applicazione e riconosce che l'impostazione è stata modificata.

    ![Impostazioni da sincronizzare](media/tutorial-add-device/settingssyncing.png)

2. Nella pagina **Proprietà** del nuovo dispositivo reale, sia la posizione di utilizzo che la data dell'ultimo utilizzo sono proprietà modificabili. I campi della versione del firmware e del numero di serie sono vuoti fino a quando il dispositivo viene connesso all'applicazione. Sono valori di sola lettura inviati dal dispositivo e non possono essere modificati.

    ![Proprietà del dispositivo reale](media/tutorial-add-device/setproperties1.png)

3. È possibile visualizzare le pagine **Misurazioni**, **Regole** e **Dashboard** per il dispositivo reale.

## <a name="prepare-the-client-code"></a>Preparare il codice client

L'esempio di codice riportato in questo articolo è scritto in [Node.js](https://nodejs.org/) e consente al dispositivo di:

* Eseguire la connessione all'applicazione Azure IoT Central.
* Inviare dati di telemetria sulla temperatura del dispositivo condizionatore d'aria connesso.
* Inviare le proprietà del dispositivo all'applicazione Azure IoT Central.
* Rispondere a un operatore che usa l'impostazione **Set Temperature** (Temperatura impostata).
* Gestire il comando Echo dall'applicazione Azure IoT Central.

Gli articoli elencati nella sezione [Passaggi successivi](#next-steps) includono esempi più completi e illustrano altri linguaggi di programmazione. Per altre informazioni su come i dispositivi si connettono ad Azure IoT Central, vedere l'articolo [Connettività dei dispositivi](concepts-connectivity.md).

La procedura seguente illustra come preparare l'esempio [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Ottenere le informazioni di connessione del dispositivo

1. La stringa di connessione per un'istanza del dispositivo nell'applicazione viene generata dalle informazioni sul dispositivo fornite da IoT Central.

   Nella schermata del condizionatore connesso reale scegliere **Connetti**.

   ![Pagina con il collegamento alle informazioni di connessione](media/tutorial-add-device/connectionlink.png)

1. Nella pagina **Connessione del dispositivo** prendere nota dei valori di **ID ambito**, **ID dispositivo** e **Chiave primaria**. Questi valori vengono usati più avanti nell'esercitazione.

   ![Dettagli della connessione](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Preparare il progetto Node.js

1. Nel computer di sviluppo creare una cartella denominata `connectedairconditioner`.

1. Nell'ambiente della riga di comando accedere alla cartella `connectedairconditioner` creata.

1. Per inizializzare il progetto di Node.js, eseguire il comando seguente accettando tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
      ```

1. Per installare i pacchetti necessari, eseguire questo comando:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Usando un editor di testo, creare un file denominato **ConnectedAirConditioner.js** nella cartella `connectedairconditioner`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Aggiungere le dichiarazioni di variabili seguenti al file. Sostituire i segnaposto `{your Scope ID}`, `{your Device ID}`e `{your Primary Key}` con le informazioni di connessione del dispositivo annotate in precedenza:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Salvare le modifiche apportate finora, ma lasciare aperto il file.

## <a name="review-client-code"></a>Esaminare il codice client

Nella sezione precedente è stato creato uno scheletro di progetto Node.js per un'applicazione che si connette all'applicazione Azure IoT Central. Il passaggio successivo consiste nell'aggiungere codice a:

* Eseguire la connessione all'applicazione Azure IoT Central.
* Inviare dati di telemetria all'applicazione Azure IoT Central.
* Inviare le proprietà del dispositivo all'applicazione Azure IoT Central.
* Ricevere le impostazioni dall'applicazione Azure IoT Central.
* Gestire il comando Echo dall'applicazione Azure IoT Central.

1. Per inviare dati di telemetria sulla temperatura all'applicazione Azure IoT Central, aggiungere il codice seguente al file **ConnectedAirConditioner.js**:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Il nome del campo nel file JSON inviato deve corrispondere al nome del campo specificato per i dati di telemetria sulla temperatura nel modello di dispositivo. In questo esempio il nome del campo è **temperature** (temperatura).

1. Per inviare le proprietà del dispositivo, ad esempio **firmwareVersion** e **serialNumber**, aggiungere la definizione seguente:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Per definire le impostazioni supportate dal dispositivo, ad esempio **setTemperature**, aggiungere la definizione seguente:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Per gestire le impostazioni inviate da Azure IoT Central, aggiungere la funzione seguente che individua ed esegue il codice del dispositivo appropriato:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    Questa funzione:

    * Controlla l'eventuale invio di una proprietà da Azure IoT Central.
    * Individua la funzione appropriata da chiamare per gestire la modifica dell'impostazione.
    * Invia un acknowledgment all'applicazione Azure IoT Central.

1. Per rispondere a un comando, ad esempio **echo**, dall'applicazione Azure IoT Central, aggiungere la definizione seguente:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Aggiungere il codice seguente per completare la connessione ad Azure IoT Central e associare le funzioni nel codice client:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. Registrare il dispositivo e connetterlo all'applicazione Azure IoT Central:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Salvare le modifiche apportate.

## <a name="run-the-client-code"></a>Eseguire il codice client

A questo punto è possibile eseguire il codice client e verificarne l'interazione con l'applicazione IoT Central:

1. Per eseguire l'esempio, immettere il comando seguente nell'ambiente della riga di comando:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Assicurarsi di trovarsi nella cartella `connectedairconditioner` quando si esegue questo comando.

1. L'applicazione stampa l'output sulla console:

   ![Output dell'applicazione client](media/tutorial-add-device/output.png)

1. Dopo circa 30 secondi i dati di telemetria vengono visualizzati nella pagina **Misurazioni** del dispositivo:

   ![Dati di telemetria reali](media/tutorial-add-device/realtelemetry.png)

1. Nella pagina **Impostazioni** è possibile osservare che l'impostazione è ora sincronizzata. Quando il dispositivo si è connesso, ha ricevuto il valore dell'impostazione e riconosciuto la modifica:

   ![Impostazioni sincronizzate](media/tutorial-add-device/settingsynced.png)

1. Nella pagina **Impostazioni** impostare la temperatura del dispositivo su **95** e scegliere **Aggiorna**. L'applicazione di esempio riceve ed elabora questa modifica:

   ![Ricezione ed elaborazione dell'impostazione](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Sono presenti due messaggi "setting update": uno quando viene inviato lo stato `pending` e uno quando viene inviato lo stato `completed`.

1. Nella pagina **Misurazioni** è possibile notare che il dispositivo invia valori di temperatura più elevati:

    ![I dati di telemetria sulla temperatura sono ora più elevati](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="nextstepaction"]
> * Aggiungere un nuovo dispositivo reale
> * Configurare il nuovo dispositivo
> * Ottenere dall'applicazione la stringa di connessione per il dispositivo reale
> * Comprendere come eseguire il mapping del codice client all'applicazione
> * Configurare il codice client per il dispositivo reale

Dopo avere connesso un dispositivo reale all'applicazione Azure IoT Central, qui sono consigliati alcuni passaggi successivi.

Un operatore può apprendere come:

* [Gestire i dispositivi](howto-manage-devices.md)
* [Usare i set di dispositivi](howto-use-device-sets.md)
* [Creare analisi personalizzate](howto-use-device-sets.md)

Uno sviluppatore di dispositivi può apprendere come:

* [Preparare e connettere un dispositivo DevKit (C)](howto-connect-devkit.md)
* [Preparare e connettere un'applicazione Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Preparare e connettere un'applicazione Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Preparare e connettere un dispositivo Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
