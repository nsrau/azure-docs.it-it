---
title: Aggiungere un dispositivo reale a un'applicazione Azure IoT Central | Microsoft Docs
description: Un operatore può aggiungere un dispositivo reale all'applicazione Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5933f74dcedb579023d187061229cdd53bce6414
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819434"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Esercitazione: Aggiungere un dispositivo reale all'applicazione Azure IoT Central

Questa esercitazione illustra come aggiungere e configurare un dispositivo reale nell'applicazione Microsoft Azure IoT Central.

Questa esercitazione è costituita da due parti:

1. L'operatore apprenderà prima come aggiungere e configurare un dispositivo reale nell'applicazione Azure IoT Central. Al termine di questa parte, recupererà una stringa di connessione da usare nella seconda parte.
2. Verranno quindi fornite informazioni per lo sviluppatore di dispositivi riguardanti il codice del dispositivo reale. La stringa di connessione recuperata nella prima parte verrà aggiunta al codice di esempio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un nuovo dispositivo reale
> * Configurare il dispositivo reale
> * Ottenere dall'applicazione la stringa di connessione per il dispositivo reale
> * Comprendere come eseguire il mapping del codice client all'applicazione
> * Configurare il codice client per il dispositivo reale

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, il generatore deve completare almeno la prima esercitazione per creare l'applicazione Azure IoT Central:

* [Definire un nuovo tipo di dispositivo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (obbligatorio)
* [Configurare le regole e le azioni per i dispositivi](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (facoltativo)
* [Personalizzare la visualizzazione dell'operatore](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (facoltativo)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Per aggiungere un dispositivo reale all'applicazione, si usa il modello di dispositivo **Connected Air Conditioner** (Condizionatore d'aria connesso) creato nell'esercitazione [Definire un nuovo tipo di dispositivo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Per aggiungere un nuovo dispositivo in qualità di operatore, scegliere **Device Explorer** nel menu di spostamento a sinistra:

   ![Pagina Device Explorer con Connected Air Conditioner (Condizionatore d'aria connesso)](media/tutorial-add-device-experimental/explorer.png)

   In **Device Explorer** viene visualizzato il modello di dispositivo **Connected Air Conditioner** (Condizionatore d'aria connesso) e il dispositivo simulato creato automaticamente al momento della creazione del modello di dispositivo da parte del generatore.

2. Per iniziare la connessione di un dispositivo condizionatore d'aria connesso reale, scegliere **Nuovo**, quindi **Reale**:

   ![Iniziare ad aggiungere un nuovo dispositivo condizionatore d'aria connesso](media/tutorial-add-device-experimental/newreal.png)

3. Immettere l'ID dispositivo (**in lettere minuscole**) o usare l'ID dispositivo suggerito. È anche possibile immettere il nome per il nuovo dispositivo e scegliere **Crea**.  

   ![Rinominare il dispositivo](media/tutorial-add-device-experimental/rename.png)

## <a name="configure-a-real-device"></a>Configurare un dispositivo reale

Il dispositivo reale viene creato sulla base del modello di dispositivo **Connected Air Conditioner** (Condizionatore d'aria connesso). È possibile usare **Impostazioni** per configurare il dispositivo e impostare i valori delle proprietà per registrare le informazioni sul dispositivo.

1. Nella pagina **Impostazioni** si noti che lo stato dell'impostazione **Set Temperature** (Temperatura impostata) indica **nessun aggiornamento**. Questo stato permane fino a quando il dispositivo reale non si connette all'applicazione e riconosce che l'impostazione è stata modificata. 

    ![Impostazioni da sincronizzare](media/tutorial-add-device-experimental/settingssyncing.png)

2. Nella pagina **Proprietà** del nuovo condizionatore connesso, la posizione di utilizzo e la data dell'ultimo utilizzo sono entrambe proprietà modificabili del dispositivo. I campi della versione del firmware e del numero di serie sono vuoti fino a quando il dispositivo viene connesso all'applicazione. Sono valori di sola lettura inviati dal dispositivo e non possono essere modificati.

    ![Proprietà del dispositivo reale](media/tutorial-add-device-experimental/setproperties1.png)

3. È possibile visualizzare le pagine **Misurazioni**, **Regole** e **Dashboard** per il dispositivo reale.

## <a name="generate-connection-string-for-real-device-from-application"></a>Generare dall'applicazione la stringa di connessione per un dispositivo reale

Uno sviluppatore di dispositivi deve incorporare la *stringa di connessione* del dispositivo reale nel codice che viene eseguito sul dispositivo. La stringa di connessione consente al dispositivo di connettersi in modo sicuro all'applicazione Azure IoT Central. La stringa di connessione viene generata durante la preparazione del codice client scritto in Node.js nei passaggi successivi. L'applicazione Node.js rappresenta il condizionatore reale connesso. 

## <a name="prepare-the-client-code"></a>Preparare il codice client

L'esempio di codice riportato in questo articolo è scritto in [Node.js](https://nodejs.org/) e consente di:

* Connettere un dispositivo all'applicazione Azure IoT Central.
* Inviare dati di telemetria sulla temperatura del dispositivo condizionatore d'aria connesso.
* Inviare le proprietà del dispositivo all'applicazione Azure IoT Central.
* Rispondere a un operatore che usa l'impostazione **Set Temperature** (Temperatura impostata).
* Gestire il comando Echo dall'applicazione Azure IoT Central.

Gli articoli sulle procedure riportati nella sezione [Passaggi successivi](#next-steps) forniscono esempi più completi e illustrano l'uso di altri linguaggi di programmazione. Per altre informazioni su come i dispositivi si connettono ad Azure IoT Central, vedere l'articolo [Connettività dei dispositivi](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

La procedura seguente illustra come preparare l'esempio [Node.js](https://nodejs.org/):

1. Installare [Node.js](https://nodejs.org/) versione 4.0.x o successiva nel computer in uso. Node.js è disponibile per un'ampia gamma di sistemi operativi.

1. Nel computer creare una cartella denominata `connectedairconditioner`.

1. Nell'ambiente della riga di comando accedere alla cartella `connectedairconditioner` creata.

1. Installare il generatore di chiavi DPS usando il comando seguente:

    ```cmd/sh
    npm i -g dps-keygen
    ```

   Per altre informazioni sullo strumento da riga di comando, vedere [qui](https://www.npmjs.com/package/dps-keygen).

1. La stringa di connessione per un'istanza del dispositivo nell'applicazione viene generata dalle informazioni sul dispositivo fornite da IoT Central.

   Tornare al portale di IoT Central. Nella schermata del condizionatore connesso reale scegliere **Connetti**.

   ![Pagina con il collegamento alle informazioni di connessione](media/tutorial-add-device-experimental/connectionlink.png)

1. Nella pagina Device Connection (Connessione del dispositivo) copiare l'ID ambito, l'ID dispositivo e la chiave primaria e incollarli in un editor di testo, quindi salvare. Questi valori verranno usati nel passaggio successivo.

   ![Dettagli della connessione](media/tutorial-add-device-experimental/device-connect.png)

1. Tornare all'ambiente della riga di comando e generare la stringa di connessione eseguendo:

    ```cmd/sh
    dps_keygen <scope_id> <device_id> <Primary Key>
    ```

   Copiare l'output e salvarlo in un nuovo file (ad esempio, connection.txt).

1. Per inizializzare il progetto di Node.js, eseguire il comando seguente accettando tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
      ```

1. Per installare i pacchetti necessari, eseguire questo comando:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Usando un editor di testo, creare un file denominato **ConnectedAirConditioner.js** nella cartella `connectedairconditioner`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Aggiungere le dichiarazioni di variabili seguenti al file:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > Il segnaposto `{your device connection string}` verrà aggiornato in un passaggio successivo.

1. Salvare le modifiche apportate finora, ma lasciare aperto il file.

## <a name="understand-how-client-code-maps-to-the-application"></a>Comprendere come eseguire il mapping del codice client all'applicazione

Nella sezione precedente è stato creato uno scheletro di progetto Node.js per un'applicazione che si connette all'applicazione Azure IoT Central. In questa sezione si aggiungerà il codice per:

* Eseguire la connessione all'applicazione Azure IoT Central.
* Inviare dati di telemetria all'applicazione Azure IoT Central.
* Inviare le proprietà del dispositivo all'applicazione Azure IoT Central.
* Ricevere le impostazioni dall'applicazione Azure IoT Central.
* Gestire il comando Echo dall'applicazione Azure IoT Central.

1. Per inviare dati di telemetria sulla temperatura all'applicazione Azure IoT Central, aggiungere il codice seguente al file **ConnectedAirConditioner.js**:

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Salvare le modifiche apportate finora, ma lasciare aperto il file.

## <a name="configure-client-code-for-the-real-device"></a>Configurare il codice client per il dispositivo reale

<!-- Add the connection string to the sample code, build, and run --> Per configurare il codice client per la connessione all'applicazione Azure IoT Central, è necessario aggiungere la stringa di connessione del dispositivo reale annotata in precedenza in questa esercitazione.

1. Nel file **ConnectedAirConditioner.js** individuare la riga di codice seguente:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Sostituire `{your device connection string}` con la stringa di connessione del dispositivo reale. La stringa di connessione è stata salvata in precedenza in un editor di testo.

1. Salvare le modifiche apportate al file **ConnectedAirConditioner.js**.

1. Per eseguire l'esempio, immettere il comando seguente nell'ambiente della riga di comando:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Assicurarsi di trovarsi nella cartella `connectedairconditioner` quando si esegue questo comando.

1. L'applicazione stampa l'output sulla console:

   ![Output dell'applicazione client](media/tutorial-add-device-experimental/output.png)

1. Dopo circa 30 secondi i dati di telemetria vengono visualizzati nella pagina **Misurazioni** del dispositivo:

   ![Dati di telemetria reali](media/tutorial-add-device-experimental/realtelemetry.png)

1. Nella pagina **Impostazioni** è possibile osservare che l'impostazione è ora sincronizzata. Quando il dispositivo si è connesso, ha ricevuto il valore dell'impostazione e riconosciuto la modifica:

   ![Impostazioni sincronizzate](media/tutorial-add-device-experimental/settingsynced.png)

1. Nella pagina **Impostazioni** impostare la temperatura del dispositivo su **95** e scegliere **Aggiorna**. L'applicazione di esempio riceve ed elabora questa modifica:

   ![Ricezione ed elaborazione dell'impostazione](media/tutorial-add-device-experimental/receivesetting.png)

   > [!NOTE]
   > Sono presenti due messaggi "setting update": uno quando viene inviato lo stato `pending` e uno quando viene inviato lo stato `completed`.

1. Nella pagina **Misurazioni** è possibile notare che il dispositivo invia valori di temperatura più elevati:

    ![I dati di telemetria sulla temperatura sono ora più elevati](media/tutorial-add-device-experimental/highertemperature.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="nextstepaction"]
> * Aggiungere un nuovo dispositivo reale
> * Configurare il nuovo dispositivo
> * Ottenere dall'applicazione la stringa di connessione per il dispositivo reale
> * Comprendere come eseguire il mapping del codice client all'applicazione
> * Configurare il codice client per il dispositivo reale

Dopo avere connesso un dispositivo reale all'applicazione Azure IoT Central, i passaggi successivi consigliati sono i seguenti:

Un operatore può apprendere come:

* [Gestire i dispositivi](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Usare i set di dispositivi](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Creare analisi personalizzate](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

Uno sviluppatore di dispositivi può apprendere come:

* [Preparare e connettere un dispositivo DevKit](howto-connect-devkit-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Preparare e connettere un'applicazione Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Customize your code (Personalizzare il codice)][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest