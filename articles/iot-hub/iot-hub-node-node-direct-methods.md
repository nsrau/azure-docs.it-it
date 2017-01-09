---
title: Usare metodi diretti | Documentazione Microsoft
description: Questa esercitazione illustra come usare metodi diretti
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: fd80a245f2cc2415bec487f98360980534df8730


---
# <a name="tutorial-use-direct-methods"></a>Esercitazione: Usare metodi diretti
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Al termine di questa esercitazione si avranno due applicazioni console Node.js:

* **CallMethodOnDevice.js**, che chiama un metodo nell'app per dispositivo simulato e visualizza la risposta.
* **SimulatedDevice.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e risponde al metodo chiamato dal cloud.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 0.10.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione viene creata un'applicazione console Node.js che risponde a un metodo chiamato dal cloud.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **SimulatedDevice.js** nella cartella **simulateddevice**.
4. Aggiungere le istruzioni `require` seguenti all'inizio del file **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un client dispositivo. Sostituire **{stringa di connessione dispositivo}** con la stringa di connessione generata nella sezione *Creare un'identità del dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Aggiungere la funzione seguente per implementare il metodo nel dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Aprire la connessione all'hub IoT e inizializzare il listener del metodo:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Salvare e chiudere il file **SimulatedDevice.js** .

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio i tentativi di connessione, come indicato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="call-a-method-on-a-device"></a>Chiamare un metodo su un dispositivo
In questa sezione viene creata un'app console Node.js che chiama un metodo nell'app per dispositivo simulato e quindi visualizza la risposta.

1. Creare una nuova cartella vuota denominata **callmethodondevice**. Nella cartella **callmethodondevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **callmethodondevice** per installare il pacchetto **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usando un editor di testo creare un file **CallMethodOnDevice.js** nella cartella **callmethodondevice**.
4. Aggiungere le istruzioni `require` seguenti all'inizio del file **CallMethodOnDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Aggiungere la dichiarazione di variabile seguente e sostituire il valore del segnaposto con la stringa di connessione per l'hub IoT:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Creare il client per aprire la connessione all'hub IoT.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Aggiungere la funzione seguente per richiamare il metodo del dispositivo e stampare la risposta del dispositivo nella console:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Salvare e chiudere il file **CallMethodOnDevice.js**.

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per iniziare ad ascoltare le chiamate ai metodi dall'hub IoT:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Al prompt dei comandi nella cartella **callmethodondevice** eseguire il comando seguente per iniziare a monitorare l'hub IoT:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Il dispositivo reagirà al metodo stampando il messaggio e l'applicazione che ha chiamato il metodo visualizzerà la risposta dal dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di reagire ai metodi richiamati dal cloud. È stata anche creata un'applicazione che richiama i metodi sul dispositivo e visualizza la risposta dal dispositivo. 

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione all'hub IoT]
* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introduzione all'hub IoT]: iot-hub-node-node-getstarted.md



<!--HONumber=Nov16_HO5-->


