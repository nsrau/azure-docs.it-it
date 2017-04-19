## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione verrà illustrato come:

* Creare un'app console Node.js che risponde a un metodo diretto chiamato dal cloud
* Attivare un aggiornamento del firmware simulato
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo completamento di un aggiornamento del firmware

Passaggio 1: creare una cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```

Passaggio 2: eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare i pacchetti SDK per dispositivi **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

Passaggio 3: con un editor di testo creare un file **dmpatterns_fwupdate_device.js** nella cartella **manageddevice**.

Passaggio 4: aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
Passaggio 5: aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**. Sostituire il segnaposto `{yourdeviceconnectionstring}` con la stringa di connessione di cui si è preso nota prima nella sezione "Create a device identity" (Creare un'identità del dispositivo):
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

Passaggio 6: aggiungere la funzione seguente che viene usata per aggiornare le proprietà segnalate:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```

Passaggio 7: aggiungere le funzioni seguenti che simulano il download e l'applicazione dell'immagine del firmware:
   
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

Passaggio 8: aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate su **waiting**. In genere i dispositivi vengono informati che è disponibile un aggiornamento e i criteri definiti dall'amministratore fanno in modo che il dispositivo avvii il download e applichi l'aggiornamento. In questa funzione viene eseguita la logica per abilitare tali criteri. Per semplicità, l'esempio attende quattro secondi prima di procedere al download dell'immagine del firmware:
   
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

Passaggio 9: aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate su **downloading**. La funzione simula quindi un download del firmware e infine imposta lo stato di aggiornamento del firmware su **downloadFailed** o su **downloadComplete**:
   
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

Passaggio 10: aggiungere la funzione seguente che imposta lo stato di aggiornamento del firmware tramite le proprietà segnalate su **applying**. La funzione simula quindi l'applicazione dell'immagine del firmware e infine imposta lo stato di aggiornamento del firmware su **applyFailed** o su **applyComplete**:
    
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

Passaggio 11: aggiungere la funzione seguente che gestisce il metodo diretto **firmwareUpdate** e avvia il processo di aggiornamento del firmware in più fasi:
    
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
      var fwPackageUri = request.payload.fwPackageUri;
    
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

Passaggio 12: aggiungere infine il codice seguente che effettua la connessione all'hub IoT:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling](https://msdn.microsoft.com/library/hh675232.aspx) (Gestione degli errori temporanei).
> 
> 