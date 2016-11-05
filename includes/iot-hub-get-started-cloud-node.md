## Creare un'identità del dispositivo
In questa sezione si scriverà un'app console di Node.js che consente di creare una nuova identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro delle identità del dispositivo. Fare riferimento alla sezione **Registro identità del dispositivo** della [Guida per sviluppatori di hub IoT][lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console, viene generato un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una nuova cartella vuota denominata **createdeviceidentity**. Nella cartella **createdeviceidentity** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **createdeviceidentity** per installare il pacchetto **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usare un editor di testo per creare un nuovo file **CreateDeviceIdentity.js** nella cartella **createdeviceidentity**.
4. Aggiungere l'istruzione `require` seguente all'inizio del file **CreateDeviceIdentity.js**:
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. Aggiungere il codice seguente al file **CreateDeviceIdentity.js**, sostituendo il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente:
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. Aggiungere il codice seguente per creare una nuova definizione di dispositivo nel registro delle identità dei dispositivi nell'hub IoT. Se l'ID del dispositivo non esiste nel registro delle identità dei dispositivi il codice crea un nuovo dispositivo, in caso contrario restituisce la chiave di quello esistente:
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```
7. Salvare e chiudere il file **CreateDeviceIdentity.js**.
8. Per eseguire l'applicazione **createdeviceidentity**, eseguire questo comando al prompt dei comandi nella cartella createdeviceidentity:
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. Prendere nota dei valori di **Device id** e **Device key**, che saranno necessari più avanti quando si creerà un'applicazione che si connette all'hub IoT come dispositivo.

> [!NOTE]
> Il registro delle identità dell'hub IoT consente di archiviare solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per sviluppatori di hub IoT][lnk-devguide-identity].
> 
> 

## Ricezione di messaggi da dispositivo a cloud
In questa sezione si creerà un'app console di Node.js che legge i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi da dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'esercitazione [Elaborare messaggi da dispositivo a cloud][lnk-processd2c-tutorial] illustra come elaborare i messaggi da dispositivo a cloud su vasta scala. L'esercitazione [Introduzione a Hub eventi][lnk-eventhubs-tutorial] fornisce altre informazioni su come elaborare i messaggi da Hub eventi ed è applicabile agli endpoint compatibili con l'hub eventi dell'hub IoT.

> [!NOTE]
> L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQPS.
> 
> 

1. Creare una nuova cartella vuota denominata **readdevicetocloudmessages**. Nella cartella **readdevicetocloudmessages** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **readdevicetocloudmessages** per installare i pacchetti **amqp10** e **bluebird**:
   
    ```
    npm install amqp10 bluebird --save
    ```
3. Usare un editor di testo per creare un nuovo file **ReadDeviceToCloudMessages.js** nella cartella **readdevicetocloudmessages**.
4. Aggiungere le istruzioni `require` seguenti all'inizio del file **ReadDeviceToCloudMessages.js**:
   
    ```
    'use strict';
   
    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```
5. Aggiungere le dichiarazioni di variabili seguenti, sostituendo i segnaposto con i valori annotati in precedenza. Il valore del segnaposto **{your event hub-compatible namespace}** proviene dal campo **Endpoint compatibile con l'hub eventi** del portale. È espresso nel formato **namespace.servicebus.windows.net**, senza il prefisso **sb://*.
   
    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```
   
   > [!NOTE]
   > Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1". Se l'hub IoT è stato creato con un altro piano tariffario, è consigliabile modificare il codice per creare un elemento **MessageReceiver** per ogni partizione.
   > 
   > 
6. Aggiungere la definizione di filtro seguente. Questa applicazione usa un filtro quando crea un ricevitore, in modo che il ricevitore legga i messaggi inviati all'hub IoT soltanto dopo l'avvio dell'esecuzione del ricevitore. Ciò è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente, ma in un ambiente di produzione il codice deve verificare l'elaborazione di tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-processd2c-tutorial].
   
    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```
7. Aggiungere il codice seguente per creare l'indirizzo di ricezione e un client AMQP:
   
    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
   
    var client = new AMQPClient(Policy.EventHub);
    ```
8. Aggiungere le due funzioni seguenti che stampano l'output nella console:
   
    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
   
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```
9. Aggiungere la funzione seguente che fa da ricevitore per una determinata partizione usando il filtro:
   
    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```
10. Aggiungere il codice seguente per connettersi all'endpoint compatibile con Hub eventi e avviare i ricevitori:
    
    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```
11. Salvare e chiudere il file **ReadDeviceToCloudMessages.js**.

<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0413_2016-->