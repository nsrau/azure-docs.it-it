## Creare un'app di dispositivo simulato

In questa sezione si creerà un'app console di Node.js che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per installare il pacchetto **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device-amqp --save
    ```

3. Usare un editor di testo per creare un nuovo file **SimulatedDevice.js** nella cartella **simulateddevice**.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Aggiungere una variabile **connectionString** e usarla per creare un client del dispositivo. Sostituire **{youriothubname}** con il nome dell'hub IoT e **{yourdeviceid}** e **{yourdevicekey}** con i valori del dispositivo generati nella sezione *Creare un'identità del dispositivo*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente per visualizzare l'output dell'applicazione:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Creare un callback e usare la funzione **setInterval** per inviare un nuovo messaggio all'hub IoT ogni secondo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Aprire la connessione all'hub IoT e iniziare a inviare messaggi:

    ```
    client.open(connectCallback);
    ```

9. Salvare e chiudere il file **SimulatedDevice.js**.

> [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [gestione degli errori temporanei][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->