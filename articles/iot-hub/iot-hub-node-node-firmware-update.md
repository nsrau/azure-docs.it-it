---
title: Aggiornamento firmware del dispositivo con l'hub IoT di Azure (Node) | Documentazione Microsoft
description: Come usare la gestione dei dispositivi nell'hub IoT di Azure per avviare un aggiornamento del firmware del dispositivo. Usare Azure IoT SDK per Node.js per implementare un'app per dispositivo simulato e un'app di servizio che attiva l'aggiornamento del firmware.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e169367592b25ea45c3d1017937316a3b3b538b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Usare la gestione dei dispositivi per avviare un aggiornamento del firmware del dispositivo (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT, offre indicazioni e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo schema viene usato nell'implementazione dell'aggiornamento del firmware per il dispositivo di esempio Intel Edison.

Questa esercitazione illustra come:

* Creare un'app console Node.js che chiama il metodo diretto firmwareUpdate nell'app per dispositivo simulato tramite l'hub IoT.
* Creare un'app di dispositivo simulato che implementa un metodo diretto **firmwareUpdate**. Questo metodo avvia un processo in più fasi che attende di scaricare l'immagine del firmware, quindi la scarica e infine la applica. Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine di questa esercitazione si avranno due app console Node.js:

**dmpatterns_fwupdate_service.js**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate aggiornate.

**dmpatterns_fwupdate_device.js**, che connette all'hub IoT con l'identità del dispositivo creata prima, riceve un metodo diretto firmwareUpdate, esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 4.0.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Vedere l'articolo [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md) per creare l'hub IoT e ottenere la stringa di connessione relativa.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione viene creata un'app console Node.js che avvia un aggiornamento del firmware remoto in un dispositivo. L'applicazione usa un metodo diretto per avviare l'aggiornamento e usa le query di dispositivo gemello per ottenere periodicamente lo stato di aggiornamento del firmware attivo.

1. Creare una cartella vuota denominata **triggerfwupdateondevice**.  Nella cartella **triggerfwupdateondevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **triggerfwupdateondevice** per installare il pacchetto **azure-iot-hub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor di testo creare un file **dmpatterns_getstarted_service.js** nella cartella **triggerfwupdateondevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Aggiungere le dichiarazioni di variabile seguenti e sostituire i valori segnaposto:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Aggiungere la funzione seguente per trovare e visualizzare il valore della proprietà segnalata da firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Aggiungere la funzione seguente per richiamare il metodo firmwareUpdate per riavviare il dispositivo di destinazione:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Aggiungere infine la funzione seguente al codice per avviare la sequenza di aggiornamento del firmware e la visualizzazione periodica delle proprietà segnalate:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Salvare e chiudere il file **dmpatterns_fwupdate_service.js**.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Al prompt dei comandi nella cartella **triggerfwupdateondevice** eseguire questo comando per attivare il riavvio remoto e cercare il dispositivo gemello per trovare l'ora dell'ultimo riavvio.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
