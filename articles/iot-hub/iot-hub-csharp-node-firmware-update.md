---
title: Come eseguire un aggiornamento del firmware con l&quot;hub IoT di Azure | Documentazione Microsoft
description: Questa esercitazione illustra come eseguire un aggiornamento del firmware
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
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 7cbb823f2d8b9a337bd987ae3fc41a85ddb6ae80


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>Esercitazione: Come eseguire un aggiornamento del firmware
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduzione
Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo modello viene usato nell'implementazione dell'aggiornamento del firmware per l'[esempio di implementazione del dispositivo Raspberry Pi][lnk-rpi-implementation].

Questa esercitazione illustra come:

* Creare un'app console che chiama il metodo diretto firmwareUpdate nell'app per dispositivo simulato tramite l'hub IoT.
* Creare un'app per dispositivo simulato che implementa un metodo diretto firmwareUpdate che esegue un processo in più fasi che attende di scaricare l'immagine del firmware, la scarica e infine la applica.  Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Node.js e un'app back-end console .NET (C#):

**dmpatterns_fwupdate_service.js**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate aggiornate.

**TriggerFWUpdate**, che connette all'hub IoT con l'identità del dispositivo creata prima, riceve un metodo diretto firmwareUpdate, esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Vedere l'articolo [Introduzione alla gestione dei dispositivi](iot-hub-csharp-node-device-management-get-started.md) per creare l'hub IoT e ottenere la stringa di connessione.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione si crea un'app console .NET (usando C#) che avvia un aggiornamento del firmware remoto in un dispositivo con un metodo diretto e usa le query del dispositivo gemello per ottenere a intervalli regolari lo stato dell'aggiornamento del firmware attivo in tale dispositivo.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **TriggerFWUpdate**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createapp]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet][img-servicenuget]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore dei segnaposti multipli con la stringa di connessione dell'hub IoT creato nella sezione precedente.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Aggiungere il metodo seguente alla classe **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Aggiungere il metodo seguente alla classe **Program** :

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Aggiungere infine le righe seguenti al metodo **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compilare la soluzione.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
Questa sezione consente di:

* Creare un'app console Node.js che risponde a un metodo diretto chiamato dal cloud
* Attivare un aggiornamento del firmware simulato
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo completamento di un aggiornamento del firmware

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **dmpatterns_fwupdate_device.js** nella cartella **manageddevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un client dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Aggiungere la funzione seguente che viene usata per aggiornare le proprietà segnalate.
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. Aggiungere le funzioni seguenti che simulano il download e l'applicazione dell'immagine del firmware.
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate sull'attesa del download.  In genere i dispositivi vengono informati che è disponibile un aggiornamento e i criteri definiti dall'amministratore fanno in modo che il dispositivo avvii il download e applichi l'aggiornamento.  In questi casi verrà eseguita la logica per abilitare tali criteri.  Per semplicità, si imposterà un ritardo di 4 secondi e si procederà al download dell'immagine del firmware. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate sul download dell'immagine del firmware.  Si continua simulando un download del firmware e infine viene aggiornato lo stato di aggiornamento del firmware per comunicare l'esito positivo o negativo del download.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate sull'applicazione dell'immagine del firmware.  Si continua simulando un'applicazione dell'immagine del firmware e infine viene aggiornato lo stato di aggiornamento del firmware per comunicare l'esito positivo o negativo dell'applicazione.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Aggiungere la funzione seguente che gestisce il metodo **firmwareUpdate** e avvia il processo di aggiornamento del firmware in più fasi.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Aggiungere infine il codice seguente che effettua la connessione all'hub IoT come dispositivo. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Eseguire l'app console C# **TriggerFWUpdate**: fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate**, quindi scegliere **Debug** e **Avvia nuova istanza**.

3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate a intervalli regolari le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.  

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


