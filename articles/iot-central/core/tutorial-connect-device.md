---
title: "Esercitazione: Connettere un'app client Node.js generica ad Azure IoT Central | Microsoft Docs"
description: Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un dispositivo che esegue un'app client Node.js all'applicazione Azure IoT Central. Per creare un modello di dispositivo, importare un modello di funzionalità di dispositivo e aggiungere visualizzazioni che consentono di interagire con un dispositivo connesso
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624337"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Esercitazione: Creare e connettere un'applicazione client all'applicazione Azure IoT Central (Node.js)

Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un'applicazione client Node.js all'applicazione Azure IoT Central. L'applicazione Node.js simula il comportamento di un dispositivo reale. Verrà usato un _modello di funzionalità di dispositivo_ di esempio per un dispositivo sensore ambientale per creare un _modello di dispositivo_ in IoT Central. Al modello di dispositivo verranno aggiunte le visualizzazioni che consentono di osservare i dati di telemetria del dispositivo, gestire le proprietà del dispositivo e usare i comandi per controllare i dispositivi.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Importare un modello di funzionalità di dispositivo per creare un modello di dispositivo.
> * Aggiungere visualizzazioni predefinite e personalizzate a un modello di dispositivo.
> * Pubblicare un modello di dispositivo e aggiungere un dispositivo reale all'applicazione IoT Central.
> * Creare ed eseguire il codice del dispositivo Node.js per connetterlo all'applicazione IoT Central.
> * Visualizzare i dati di telemetria simulati inviati dal dispositivo.
> * Usare una visualizzazione per gestire le proprietà del dispositivo.
> * Chiamare i comandi per controllare il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un'applicazione Azure IoT Central creata usando il modello **Applicazione personalizzata**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un computer di sviluppo con [Node.js](https://nodejs.org/) versione 10.0.0 o successiva installato. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi. Le istruzioni riportate in questa esercitazione presuppongono che il comando **node** venga eseguito al prompt dei comandi di Windows. È possibile usare Node.js in un'ampia gamma di sistemi operativi.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Nel computer locale creare una cartella denominata `environmental-sensor`.

Scaricare il file JSON del [Modello di funzionalità sensore ambientale](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) e salvarlo nella cartella `environmental-sensor`.

Usare un editor di testo e sostituire le due istanze di `{YOUR_COMPANY_NAME_HERE}` con il nome dell'azienda nel file `EnvironmentalSensorInline.capabilitymodel.json` scaricato.

Nell'applicazione Azure IoT Central creare un modello di dispositivo denominato *sensore ambientale* importando il file del modello di funzionalità di dispositivo `EnvironmentalSensorInline.capabilitymodel.json`:

![Modello di dispositivo con modello di funzionalità di dispositivo importato](./media/tutorial-connect-device/device-template.png)

Il modello di funzionalità di dispositivo include due interfacce: l'interfaccia **Informazioni sul dispositivo** standard e l'interfaccia **Sensore ambientale** personalizzata. L'interfaccia **Sensore ambientale** definisce le funzionalità seguenti:

| Type | Nome visualizzato | Descrizione |
| ---- | ------------ | ----------- |
| Proprietà | Stato del dispositivo     | Stato del dispositivo. Sono disponibili due stati, online/offline. |
| Proprietà | Nome del cliente    | Nome del cliente che attualmente gestisce il dispositivo. |
| Proprietà | Livello luminosità | Livello di luminosità dell'illuminazione nel dispositivo. Può essere specificato come 1 (alto), 2 (medio), 3 (basso). |
| Telemetria | Temperatura | Temperatura corrente del dispositivo. |
| Telemetria | Umidità    | Umidità corrente del dispositivo. |
| Comando | blink          | Attiva il lampeggiamento del LED per l'intervallo di tempo specificato. |
| Comando | turnon         | Accende la luce del LED sul dispositivo. |
| Comando | turnoff        | Spegne la luce del LED sul dispositivo. |
| Comando | rundiagnostics | Questo comando avvia un'esecuzione di diagnostica. |

Per personalizzare la modalità di visualizzazione della proprietà **Stato del dispositivo** nell'applicazione IoT Central, selezionare **Personalizza** nel modello di dispositivo. Espandere la voce **Stato del dispositivo**, immettere _Online_ come **Nome True** e _Offline_ come **Nome False**. Salvare quindi le modifiche:

![Personalizzare il modello di dispositivo](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Creare viste

Le visualizzazioni consentono di interagire con i dispositivi connessi all'applicazione IoT Central. Le visualizzazioni possono ad esempio mostrare dati di telemetria e le proprietà oppure consentire la modifica di proprietà scrivibili e cloud. Le visualizzazioni fanno parte di un modello di dispositivo.

Per aggiungere alcune visualizzazioni predefinite al modello di dispositivo **Sensore ambientale**, passare al modello di dispositivo, selezionare **Visualizzazioni** e quindi selezionare il riquadro **Genera visualizzazioni predefinite**. Assicurarsi che le opzioni **Panoramica** e **Informazioni su** siano impostate su **Sì**, quindi selezionare **Generate default dashboard views** (Genera visualizzazioni dashboard predefinite). Nel modello sono ora definite due visualizzazioni predefinite.

L'interfaccia **Sensore ambientale** include due proprietà scrivibili, ovvero **Nome cliente** e **Livello luminosità**. Per creare una visualizzazione, è possibile modificare queste proprietà:

1. Selezionare **Visualizzazioni** e quindi il riquadro **Modifica dei dati del dispositivo e del cloud**.

1. Immettere _Properties_ come nome del modulo.

1. Selezionare le proprietà del dispositivo **Brightness Level** (Livello luminosità) e **Nome cliente**. Fare quindi clic su **Aggiungi sezione**.

1. Salvare le modifiche.

![Aggiungere una visualizzazione per abilitare la modifica delle proprietà](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Pubblicare il modello

Prima di aggiungere un dispositivo all'applicazione IoT Central che usa il modello di dispositivo **Sensore ambientale**, è necessario pubblicare il modello.

Nel modello di dispositivo selezionare **Pubblica**. Nel riquadro che mostra le modifiche da pubblicare selezionare **Pubblica**.

Per verificare che il modello sia pronto per l'uso, passare alla pagina **Dispositivi** nell'applicazione IoT Central. La sezione **Dispositivi** visualizza un elenco dei dispositivi pubblicati nell'applicazione:

![Modelli pubblicati nella pagina Dispositivi](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale al modello di dispositivo creato nella sezione precedente:

1. Nella pagina **Dispositivi** selezionare il modello di dispositivo **Sensore ambientale**.

1. Selezionare **+ Nuovo**.

1. Assicurarsi che l'opzione **Simulato** sia impostata su **No**. Selezionare quindi **Crea**.

Fare clic sul nome del dispositivo e quindi selezionare **Connetti**. Prendere nota delle informazioni di connessione del dispositivo nella pagina **Connessione del dispositivo**: **Ambito ID**, **ID dispositivo** e **Chiave primaria**. Questi valori saranno necessari per la creazione del codice del dispositivo:

![Informazioni di connessione del dispositivo](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

I passaggi seguenti illustrano come creare un'applicazione client Node.js che implementa il dispositivo reale aggiunto all'applicazione. L'applicazione Node.js simula il comportamento di un dispositivo reale.

1. Nell'ambiente della riga di comando passare alla cartella `environmental-sensor` creata in precedenza.

1. Per inizializzare il progetto Node.js e installare le dipendenze necessarie, eseguire i comandi seguenti e accettare tutte le opzioni predefinite quando si esegue `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Creare un file denominato **environmentalSensor.js** nella cartella `environmental-sensor`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **environmentalSensor.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Aggiungere le dichiarazioni di variabili seguenti al file:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    Aggiornare i segnaposto `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` con i valori annotati in precedenza. In questo esempio il valore `targetTemperature` viene inizializzato impostandolo su zero. È anche possibile usare la lettura corrente dal dispositivo o il valore dal dispositivo gemello.

1. Per inviare i dati di telemetria all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Per inviare le proprietà del dispositivo all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Per definire e gestire le proprietà scrivibili a cui risponde il dispositivo, aggiungere il codice seguente:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Aggiungere il codice seguente per gestire i comandi inviati dall'applicazione IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Eseguire l'applicazione Node.js

Per avviare l'applicazione client del dispositivo, immettere il comando seguente nell'ambiente della riga di comando:

```cmd/sh
node environmentalSensor.js
```

È possibile osservare che il dispositivo si connette all'applicazione Azure IoT Central e inizia a inviare i dati di telemetria:

![Eseguire l'applicazione client](media/tutorial-connect-device/run-application.png)

Nell'applicazione Azure IoT Central un operatore può:

* Visualizzare i dati di telemetria inviati dal dispositivo nella pagina **Panoramica**:

    ![Visualizzare i dati di telemetria](media/tutorial-connect-device/view-telemetry.png)

* Aggiornare i valori delle proprietà scrivibili nella pagina **Proprietà**:

    ![Aggiornare le proprietà](media/tutorial-connect-device/update-properties.png)

    ![Aggiornare le proprietà del dispositivo](media/tutorial-connect-device/update-properties-device.png)

* Chiamare i comandi dalla pagina **Comandi**:

    ![Chiamare il comando blink](media/tutorial-connect-device/call-command.png)

    ![Chiamare il comando blink sul dispositivo](media/tutorial-connect-device/call-command-device.png)

* Visualizzare le proprietà del dispositivo nella pagina **Informazioni su**:

    ![Visualizzazione di proprietà](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui modelli di funzionalità di dispositivo e su come creare modelli di dispositivo personalizzati, continuare con la guida pratica:

> [!div class="nextstepaction"]
> [Definire un nuovo tipo di dispositivo IoT](./howto-set-up-template.md)
