---
title: Introduzione alla gestione dei dispositivi dell&quot;hub IoT di Azure (Node) | Documentazione Microsoft
description: Come usare la gestione dei dispositivi dell&quot;hub IoT per riavviare un dispositivo remoto. Usare Azure IoT SDK per Node.js per implementare un&quot;app per dispositivo simulato che include un metodo diretto e un&quot;app di servizio che richiama il metodo diretto.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: e1bb89ba369818d7ba0e92a54a4712033f648187


---
# <a name="get-started-with-device-management-node"></a>Introduzione alla gestione dei dispositivi (Node)
## <a name="introduction"></a>Introduzione
Le applicazioni cloud IoT possono usare primitive nell'hub IoT di Azure, ovvero i metodi diretti e il dispositivo gemello, per avviare e monitorare le operazioni di gestione del dispositivo in modalità remota.  Questo articolo illustra come un'applicazione cloud IoT e un dispositivo collaborano per avviare e monitorare un riavvio del dispositivo in modalità remota tramite l'hub IoT.

Per avviare e monitorare le operazioni di gestione dei dispositivi da un'applicazione back-end basata su cloud in modalità remota, usare le primitive dell'hub IoT di Azure, ad esempio [dispositivo gemello][lnk-devtwin] e [metodi diretti][lnk-c2dmethod]. Questa esercitazione illustra come un'applicazione back-end e un dispositivo collaborano per avviare e monitorare il riavvio di un dispositivo in modalità remota dall'hub IoT.

Usare un metodo diretto per avviare le operazioni di gestione dei dispositivi, ad esempio il riavvio, il ripristino delle impostazioni predefinite e l'aggiornamento del firmware, da un'applicazione back-end nel cloud. Il dispositivo è responsabile per:

* La gestione della richiesta di metodo inviata dall'hub IoT.
* L'avvio di un'operazione specifica del dispositivo corrispondente sul dispositivo.
* Gli aggiornamenti di stato tramite le proprietà segnalate nell'hub IoT.

È possibile usare un'applicazione back-end nel cloud per eseguire query di un dispositivo gemello in modo da creare report sullo stato di avanzamento delle operazioni di gestione del dispositivo.

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.
* Creare un'app per dispositivo simulato con un metodo diretto che abilita il riavvio e può essere chiamato dal cloud.
* Creare un'app console Node.js che chiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione si avranno due app console Node.js:

**dmpatterns_getstarted_device.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve un metodo diretto per il riavvio, simula un riavvio fisico e segnala il tempo impiegato per l'ultimo riavvio.

**dmpatterns_getstarted_service.js**, che chiama un metodo diretto nel dispositivo simulato, visualizza la risposta e le proprietà segnalate aggiornate.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
Questa sezione consente di:

* Creare un'app console Node.js che risponde a un metodo diretto chiamato dal cloud
* Attivare un riavvio del dispositivo simulato
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **dmpatterns_getstarted_device.js** nella cartella **manageddevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**.  Sostituire la stringa di connessione con la stringa di connessione del dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Aggiungere la funzione seguente per implementare il metodo diretto nel dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Aprire la connessione all'hub IoT e avviare il listener del metodo diretto:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Salvare e chiudere il file **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto
In questa sezione si crea un'app console Node.js che avvia un riavvio remoto in un dispositivo con un metodo diretto e usa le query del dispositivo gemello per ottenere l'ora dell'ultimo riavvio di tale dispositivo.

1. Creare una nuova cartella vuota denominata **triggerrebootondevice**.  Nella cartella **triggerrebootondevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **triggerrebootondevice** per installare il pacchetto SDK per dispositivi **azure-iothub** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor di testo creare un nuovo file **dmpatterns_getstarted_service.js** nella cartella **triggerrebootondevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Aggiungere le dichiarazioni di variabile seguenti e sostituire i valori segnaposto:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Aggiungere la funzione seguente per richiamare il metodo per riavviare il dispositivo di destinazione:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. Aggiungere la funzione seguente per eseguire una query per il dispositivo e ottenere l'ora dell'ultimo riavvio:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. Aggiungere il codice seguente per chiamare le funzioni che attivano il metodo diretto per il riavvio ed eseguire una query per ottenere l'ora dell'ultimo riavvio:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Salvare e chiudere il file **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Al prompt dei comandi nella cartella **triggerrebootondevice** eseguire questo comando per attivare il riavvio remoto e cercare il dispositivo gemello per trovare l'ora dell'ultimo riavvio.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizzare ed estendere le operazioni di gestione dei dispositivi
Le soluzioni IoT possono espandere il set definito di modelli di gestione di dispositivi o abilitare modelli personalizzati tramite l'uso delle primitive dispositivo gemello e metodo diretto. Altri esempi di operazioni di gestione dei dispositivi includono il ripristino delle informazioni predefinite, l'aggiornamento del firmware, l'aggiornamento del software, il risparmio energia, la gestione di rete e connettività e la crittografia dei dati.

## <a name="device-maintenance-windows"></a>Finestre di manutenzione del dispositivo
Configurare i dispositivi in modo che eseguano le azioni in un momento che riduce al minimo le interruzioni e i tempi di inattività.  Le finestre di manutenzione del dispositivo costituiscono un modello comunemente usato per definire il momento in cui un dispositivo deve eseguire l'aggiornamento della configurazione. Le soluzioni di back-end possono usare le proprietà desiderate del dispositivo gemello per definire e attivare un criterio sul dispositivo che attiva una finestra di manutenzione. Quando un dispositivo riceve il criterio della finestra di manutenzione, può usare la proprietà segnalata del dispositivo gemello per segnalare lo stato del criterio. L'applicazione back-end può quindi usare le query del dispositivo gemello per attestare la conformità dei dispositivi e di tutti i criteri.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un riavvio remoto di un dispositivo, sono state usate le proprietà segnalate per segnalare l'ora di ultimo riavvio del dispositivo ed è stata eseguita una query per ottenere l'ora di ultimo riavvio del dispositivo dal cloud.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Getting started with the IoT Gateway SDK][lnk-gateway-SDK] (Introduzione a IoT Gateway SDK).

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Dec16_HO1-->


