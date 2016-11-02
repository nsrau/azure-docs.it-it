<properties
 pageTitle="Come eseguire un aggiornamento del firmware | Microsoft Azure"
 description="Questa esercitazione illustra come eseguire un aggiornamento del firmware"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>


# <a name="tutorial:-how-to-do-a-firmware-update-(preview)"></a>Esercitazione: Come eseguire un aggiornamento del firmware

## <a name="introduction"></a>Introduzione
Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare le primitive dei [dispositivi gemelli][lnk-devtwin] e dei [metodi da cloud a dispositivo][lnk-c2dmethod] per riavviare un dispositivo in remoto. Questa esercitazione usa le stesse primitive dell'hub IoT, offre indicazioni e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo schema viene usato nell'implementazione dell'aggiornamento del firmware per il dispositivo di esempio Intel Edison.

Questa esercitazione illustra come:

- Creare un'applicazione console che chiama il metodo diretto firmwareUpdate sul dispositivo simulato tramite l'hub IoT.
- Creare un dispositivo simulato che implementa un metodo diretto firmwareUpdate che esegue un processo in più fasi che attende di scaricare l'immagine del firmware, la scarica e infine la applica.  Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate dal dispositivo gemello per aggiornare lo stato.

Al termine di questa esercitazione si avranno due applicazioni console Node.js:

**dmpatterns_fwupdate_service.js**, che chiama un metodo diretto sul dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate dal dispositivo gemello aggiornate.

**dmpatterns_fwupdate_device.js**, che connette all'hub IoT con l'identità del dispositivo creata prima, riceve un metodo diretto firmwareUpdate, esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.


Per completare l'esercitazione, sono necessari gli elementi seguenti:

Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment] (Preparare l'ambiente di sviluppo)[lnk-dev-setup] descrive come installare Node.js per questa esercitazione in Windows o Linux.

Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

Vedere l'articolo [Introduzione alla gestione dei dispositivi](iot-hub-device-management-get-started.md) per creare l'hub IoT e ottenere la stringa di connessione.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione si crea un'app console Node.js che risponde a un metodo diretto chiamato dal cloud, che attiva un aggiornamento del firmware del dispositivo simulato e usa le proprietà segnalate dal dispositivo gemello per abilitare le query del dispositivo gemello che consentono di identificare i dispositivi e di sapere quando sono stati riavviati l'ultima volta.

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device@dtpreview**e il pacchetto **azure-iot-device-mqtt@dtpreview**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
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

6. Aggiungere la funzione seguente che verrà usata per aggiornare le proprietà segnalate dal dispositivo gemello.

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
    
7. Aggiungere le funzioni seguenti che simuleranno il download e l'applicazione dell'immagine del firmware.

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

8. Aggiungere la funzione seguente che imposterà lo stato di aggiornamento del firmware tramite le proprietà segnalate dal dispositivo gemello sull'attesa del download.  In genere i dispositivi vengono informati che è disponibile un aggiornamento e i criteri definiti dall'amministratore fanno in modo che il dispositivo avvii il download e applichi l'aggiornamento.  In questi casi verrà eseguita la logica per abilitare tali criteri.  Per semplicità, si imposterà un ritardo di 4 secondi e si procederà al download dell'immagine del firmware. 

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
    
9. Aggiungere la funzione seguente che imposterà lo stato di aggiornamento del firmware tramite le proprietà segnalate dal dispositivo gemello sul download dell'immagine del firmware.  Si continua simulando un download del firmware e infine viene aggiornato lo stato di aggiornamento del firmware per comunicare l'esito positivo o negativo del download.

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
    
10. Aggiungere la funzione seguente che imposterà lo stato di aggiornamento del firmware tramite le proprietà segnalate dal dispositivo gemello sull'applicazione dell'immagine del firmware.  Si continua simulando un'applicazione dell'immagine del firmware e infine viene aggiornato lo stato di aggiornamento del firmware per comunicare l'esito positivo o negativo dell'applicazione.

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

11. Aggiungere la funzione seguente che gestisce il metodo firmwareUpdate e avvia il processo di aggiornamento del firmware in più fasi.

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
    
12. Aggiungere infine il codice seguente che connette all'hub IoT come dispositivo. 

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

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] (Gestione degli errori temporanei)[lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto 

In questa sezione si crea un'app console Node.js che avvia un aggiornamento del firmware remoto in un dispositivo con un metodo diretto e usa le query del dispositivo gemello per ottenere a intervalli regolari lo stato dell'aggiornamento del firmware attivo in tale dispositivo.


1. Creare una nuova cartella vuota denominata **triggerfwupdateondevice**.  Nella cartella **triggerfwupdateondevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Eseguire questo comando al prompt dei comandi nella cartella **triggerfwupdateondevice** per installare il pacchetto SDK per dispositivi **azure-iothub** e il pacchetto **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Con un editor di testo creare un nuovo file **dmpatterns_getstarted_service.js** nella cartella **triggerfwupdateondevice**.

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

8. Aggiungere infine la funzione seguente al codice per avviare la sequenza di aggiornamento del firmware e la visualizzazione periodica delle proprietà segnalate dal dispositivo gemello:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Salvare e chiudere il file **dmpatterns_fwupdate_service.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. Al prompt dei comandi nella cartella **triggerfwupdateondevice** eseguire questo comando per attivare il riavvio remoto e cercare il dispositivo gemello per trovare l'ora dell'ultimo riavvio.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Si osserverà la risposta al metodo diretto visualizzando il messaggio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate a intervalli regolari le proprietà segnalate dal dispositivo gemello per conoscere lo stato del processo di aggiornamento del firmware.  

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Schedule and broadcast jobs] (Pianificare e trasmettere processi)[lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Oct16_HO2-->


