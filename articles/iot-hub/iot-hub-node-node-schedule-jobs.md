---
title: Pianificare processi con l'hub IoT di Azure (Node) | Microsoft Docs
description: "Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per Node.js per implementare le app per dispositivi simulati e un'app di servizio per eseguire il processo."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: e607f5db8b4f2a974cb172d4581dadefe7851275
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="schedule-and-broadcast-jobs-node"></a>Pianificare e trasmettere processi (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Grazie a un processo, ad esempio, un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento.  L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivi gemelli e proprietà: [Introduzione ai dispositivi gemelli][lnk-get-started-twin] ed [Esercitazione: Come usare le proprietà dei dispositivi gemelli][lnk-twin-props]
* Metodi diretti: [Guida per sviluppatori dell'hub IoT - Metodi diretti][lnk-dev-methods] ed [Esercitazione: Metodi diretti][lnk-c2d-methods]

Questa esercitazione illustra come:

* Creare un'app Node.js per dispositivo simulato con un metodo diretto che abilita il metodo diretto **lockDoor** che può essere chiamato dal back-end della soluzione.
* Creare un'app console Node.js che chiama il metodo diretto **lockDoor** nell'app per dispositivo simulato tramite un processo e aggiorna le proprietà desiderate tramite un processo del dispositivo.

Al termine di questa esercitazione si avranno due app Node.js:

**simDevice.js**, che si connette all'hub IoT con l'identità del dispositivo e riceve un metodo diretto **lockDoor**.

**scheduleJobService.js**, che chiama un metodo diretto nell'app per dispositivo simulato e aggiorna le proprietà desiderate di un dispositivo gemello tramite un processo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 4.0.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione viene creata un'applicazione console Node.js che risponde a un metodo chiamato dal cloud, che attiva un metodo **lockDoor** simulato.

1. Creare una nuova cartella vuota chiamata **simDevice**.  Nella cartella **simDevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **simDevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **simDevice.js** nella cartella **simDevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **simDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Aggiungere la funzione seguente per gestire il metodo **lockDoor**.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor**.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Salvare e chiudere il file **simDevice.js**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli
In questa sezione si creerà un'app console Node.js che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e verranno aggiornate le proprietà del dispositivo gemello.

1. Creare una nuova cartella vuota chiamata **scheduleJobService**.  Nella cartella **scheduleJobService** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **scheduleJobService** per installare il pacchetto SDK per dispositivi **azure-iothub** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Usando un editor di testo, creare un nuovo file **scheduleJobService.js** nella cartella **scheduleJobService**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_gscheduleJobServiceetstarted_service.js**:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Aggiungere le dichiarazioni di variabile seguenti e sostituire i valori segnaposto:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Aggiungere la funzione seguente che verrà usata per monitorare l'esecuzione del processo:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. Aggiungere il codice seguente per pianificare il processo che chiama il metodo del dispositivo:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. Aggiungere il codice seguente per pianificare il processo che aggiorna il dispositivo gemello:
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. Salvare e chiudere il file **scheduleJobService.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella **simDevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node simDevice.js
    ```
2. Eseguire il comando riportato di seguito al prompt dei comandi nella cartella **scheduleJobService** per attivare i processi per bloccare la porta e aggiornare il dispositivo gemello
   
    ```
    node scheduleJobService.js
    ```
3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Introduzione a IoT Edge di Azure][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
