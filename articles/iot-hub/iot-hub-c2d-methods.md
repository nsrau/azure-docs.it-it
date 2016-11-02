<properties
 pageTitle="Utilizzo di metodi diretti | Microsoft Azure"
 description="Questa esercitazione illustra come usare metodi diretti"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial:-use-direct-methods"></a>Esercitazione: Usare metodi diretti

## <a name="introduction"></a>Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Le esercitazioni precedenti, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT], illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT. L'hub IoT offre inoltre la possibilità di richiamare metodi non durevoli dal cloud ai dispositivi. I metodi rappresentano un'interazione di tipo richiesta-risposta con un dispositivo, analogamente a una chiamata HTTP, in quanto l'esito positivo o negativo è comunicato immediatamente dopo il timeout specificato dall'utente, consentendo all'utente di conoscere subito lo stato della chiamata. L'articolo [Richiamare un metodo diretto su un dispositivo][lnk-devguide-methods] descrive i metodi in maggior dettaglio e spiega quando usarli in alternativa ai messaggi da cloud a dispositivo.

Questa esercitazione illustra come:

- Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.
- Creare un dispositivo simulato con un metodo diretto che può essere chiamato dal cloud.
- Creare un'applicazione console che chiama un metodo diretto sul dispositivo simulato tramite l'hub IoT.

> [AZURE.NOTE] Al momento i metodi diretti sono accessibili solo dai dispositivi che si connettono all'hub IoT tramite il protocollo MQTT. Per istruzioni su come convertire l'app del dispositivo esistente affinché usi MQTT, leggere l'articolo [Supporto di MQTT][lnk-devguide-mqtt].

Al termine di questa esercitazione si avranno due applicazioni console Node.js:

* **CallMethodOnDevice.js**, che chiama un metodo sul dispositivo simulato e visualizza la risposta.
* **SimulatedDevice.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e risponde al metodo chiamato dal cloud.

> [AZURE.NOTE] L'articolo [SDK Hub IoT][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel back-end della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Node.js 0.10.x o versione successiva.

+ Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione viene creata un'applicazione console Node.js che risponde a un metodo chiamato dal cloud.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
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

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio la ripetizione della connessione, come indicato nell'articolo di MSDN [Transient Fault Handling] (Gestione degli errori temporanei)[lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Chiamare un metodo su un dispositivo

In questa sezione viene creata un'applicazione console Node.js che chiama un metodo sul dispositivo simulato e quindi visualizza la risposta.

1. Creare una nuova cartella vuota denominata **callmethodondevice**. Nella cartella **callmethodondevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **callmethodondevice** per installare il pacchetto **azure-iothub**:

    ```
    npm install azure-iothub@dtpreview --save
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

7. Salvare e chiudere il file **CallMethodOnDevice.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

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

In questa esercitazione è stato configurato un nuovo hub IoT nel portale ed è stata quindi creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di reagire ai metodi richiamati dal cloud. È stata anche creata un'applicazione che richiama i metodi sul dispositivo e visualizza la risposta dal dispositivo. 

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Introduzione all'hub IoT]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Schedule and broadcast jobs] (Pianificare e trasmettere processi)[lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Elaborare i messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Introduzione all’hub IoT]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


