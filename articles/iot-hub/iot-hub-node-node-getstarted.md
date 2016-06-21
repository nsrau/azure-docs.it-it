<properties
	pageTitle="Introduzione all'hub IoT di Azure per Node.js | Microsoft Azure"
	description="Esercitazione introduttiva all'hub IoT di Azure con Node.js. Usare l'hub IoT di Azure e Node.js con gli SDK IoT di Microsoft Azure per implementare una soluzione Internet delle cose."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introduzione all'hub IoT di Azure per Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT (Internet delle cose) e un back-end della soluzione. Una delle maggiori difficoltà con i progetti IoT consiste nel connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. Per affrontare questa sfida, l'hub IoT:

- Offre messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
- Rende possibili comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
- Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questa esercitazione illustra come:

- Usare il portale di Azure per creare un hub IoT.
- Creare un'identità del dispositivo nell'hub IoT.
- Creare un dispositivo simulato che invia dati di telemetria al back-end cloud.

Al termine di questa esercitazione si avranno tre applicazioni console di Node.js:

* **CreateDeviceIdentity.js**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **ReadDeviceToCloudMessages.js**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **SimulatedDevice.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo AMQPS.

> [AZURE.NOTE] L'articolo [SDK Hub IoT][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel backend della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Node. js 0.12.x o versione successiva. <br/> [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Infine, fare clic su **Impostazioni** nel pannello dell'hub IoT e quindi su **Messaggistica** nel pannello **Impostazioni**. Nel pannello **Messaggistica** prendere nota del **Nome compatibile con l'hub eventi** e dell'**Endpoint compatibile con l'hub eventi**. Questi valori serviranno quando si creerà l'applicazione **read-d2c-messages**.

![][6]

È stato creato l'hub IoT e sono disponibili il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, il nome compatibile con l'hub eventi e l'endpoint compatibile con l'hub eventi, valori necessari per completare il resto dell'esercitazione.

## Creare un'identità del dispositivo

In questa sezione si scriverà un'app console di Node.js che consente di creare una nuova identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro delle identità del dispositivo. Fare riferimento alla sezione **Registro identità del dispositivo** della [Guida per sviluppatori di hub IoT][lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console, viene generato un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Creare una nuova cartella vuota denominata **createdeviceidentity**. Nella cartella **createdeviceidentity** creare un nuovo file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **createdeviceidentity** per installare il pacchetto **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Con un editor di testo creare un nuovo file **CreateDeviceIdentity.js** nella cartella **createdeviceidentity**.

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

> [AZURE.NOTE] Il registro delle identità dell'hub IoT consente di archiviare solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per sviluppatori di hub IoT][lnk-devguide-identity].

## Ricezione di messaggi da dispositivo a cloud

In questa sezione si creerà un'app console di Node.js che legge i messaggi da dispositivo a cloud dall'hub IoT. L'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs-overview] per abilitare la lettura dei messaggi da dispositivo a cloud. Per semplicità, questa esercitazione crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. L'esercitazione [Elaborare messaggi da dispositivo a cloud][lnk-process-d2c-tutorial] illustra come elaborare i messaggi da dispositivo a cloud su vasta scala. L'esercitazione [Introduzione all'Hub eventi][lnk-eventhubs-tutorial] fornisce altre informazioni su come elaborare i messaggi da hub eventi ed è applicabile agli endpoint compatibili con l'hub eventi dell'hub IoT.

> [AZURE.NOTE] L'endpoint compatibile con Hub eventi per la lettura di messaggi da dispositivo a cloud usa sempre il protocollo AMQPS.

1. Creare una nuova cartella vuota denominata **readdevicetocloudmessages**. Nella cartella **readdevicetocloudmessages** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **readdevicetocloudmessages** per installare i pacchetti **amqp10** e **bluebird**:

    ```
    npm install amqp10 bluebird --save
    ```

3. Con un editor di testo creare un nuovo file **ReadDeviceToCloudMessages.js** nella cartella **readdevicetocloudmessages**.

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

    > [AZURE.NOTE] Questo codice presuppone che l'hub IoT sia stato creato nel livello F1 gratuito. Un hub IoT gratuito ha due partizioni denominate "0" e "1". Se l'hub IoT è stato creato con un altro piano tariffario, è consigliabile modificare il codice per creare un elemento **MessageReceiver** per ogni partizione.

6. Aggiungere la definizione di filtro seguente. Questa applicazione usa un filtro quando crea un ricevitore, in modo che il ricevitore legga i messaggi inviati all'hub IoT soltanto dopo l'avvio dell'esecuzione del ricevitore. Ciò è utile in un ambiente di test perché consente di visualizzare il set di messaggi corrente, ma in un ambiente di produzione il codice deve verificare di avere elaborato tutti i messaggi. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial].

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

## Creare un'app di dispositivo simulato

In questa sezione si creerà un'app console di Node.js che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un nuovo file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per installare il pacchetto **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Con un editor di testo creare un nuovo file **SimulatedDevice.js** nella cartella **simulateddevice**.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Aggiungere una variabile **connectionString** e usarla per creare un client dispositivo. Sostituire **{youriothubname}** con il nome dell'hub IoT e **{yourdeviceid}** e **{yourdevicekey}** con i valori del dispositivo generati nella sezione *Creare un'identità del dispositivo*:

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


## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Eseguire questo comando al prompt dei comandi nella cartella **readdevicetocloudmessages** per iniziare il monitoraggio dell'hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Eseguire questo comando al prompt dei comandi della cartella **simulateddevice** per iniziare a inviare dati di telemetria all'hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub:

    ![][43]

## Passaggi successivi

In questa esercitazione si è configurato un nuovo hub IoT nel portale e quindi è stata creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di inviare all'hub messaggi da dispositivo a cloud ed è stata creata un'app che visualizza i messaggi ricevuti dall'hub. È possibile continuare a esplorare le funzionalità dell'hub IoT e altri scenari IoT nelle esercitazioni seguenti:

- [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial] illustra come inviare messaggi ai dispositivi ed elaborare i commenti sul recapito generati dall'hub IoT.
- [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial] illustra come elaborare in modo affidabile dati di telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricare file dai dispositivi al cloud con l'hub IoT][lnk-upload-tutorial] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->