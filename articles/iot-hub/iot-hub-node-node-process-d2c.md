---
title: Routing dei messaggi tramite Hub IoT di Azure (Node) | Microsoft Docs
description: Come elaborare i messaggi da dispositivo a cloud di Hub IoT di Azure usando le regole di routing e gli endpoint personalizzati per inviare i messaggi agli altri servizi di back-end.
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: 5a80195dd474414626cc54623945393c6f88093d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-node"></a>Routing dei messaggi tramite Hub IoT (Node)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Questa esercitazione si basa sull'esercitazione [Introduzione a hub IoT].  L'esercitazione:

* Illustra come usare le regole di routing per inviare i messaggi da dispositivo a cloud con un semplice metodo basato sulla configurazione.
* Illustra come isolare i messaggi interattivi che richiedono un intervento immediato del back-end della soluzione per essere elaborati in seguito.  Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM.  Al contrario, i messaggi di punto dati, come ad esempio la telemetria di temperatura, vengono semplicemente inseriti in un motore di analisi.

Al termine di questa esercitazione si avranno tre app console Node.js in esecuzione:

* **SimulatedDevice.js**, una versione modificata dell'app creata nell'esercitazione di [Introduzione a hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud a intervalli casuali. Questa app usa il protocollo MQTT per comunicare con l'hub IoT.
* **ReadDeviceToCloudMessages.js**, che visualizza i dati di telemetria inviati dall'app per dispositivi.
* **ReadCriticalQueue.js**, che rimuove dalla coda i messaggi importanti dalla coda del bus di servizio collegato all'hub IoT.

> [!NOTE]
> L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una versione di lavoro completa dell'esercitazione di [Introduzione a hub IoT] .
* Node.js 4.0.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Si consiglia anche la lettura della documentazione su [Archiviazione di Azure] e [Bus di servizio di Azure].

## <a name="send-interactive-messages-from-a-device-app"></a>Inviare messaggi interattivi da un'app per dispositivi
In questa sezione viene modificata l'app per dispositivi creata nell'esercitazione [Introduzione a hub IoT] per inviare occasionalmente messaggi che richiedono un intervento immediato.

1. Con un editor di testo aprire il file **simulateddevice\SimulatedDevice.js**. Questo file contiene il codice dell'app **SimulatedDevice** creata durante l'esercitazione di [Introduzione a hub IoT].

2. Sostituire la funzione **connectCallback** con il codice seguente:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Questo metodo aggiunge in modo casuale la proprietà `"level": "critical"` e `"level": "storage"` ai messaggi inviati dal dispositivo, simulando così un messaggio che richiede l'intervento immediato del back-end dell'applicazione o uno che richiede l'archiviazione permanente. L'applicazione passa queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che l'hub IoT possa indirizzare il messaggio alla destinazione messaggi appropriata.
   
   > [!NOTE]
   > È possibile usare le proprietà del messaggio per indirizzare i messaggi in diversi scenari, tra cui l'elaborazione del percorso a freddo, oltre all'esempio del percorso a caldo mostrato qui.

2. Salvare e chiudere il file **simulateddevice\SimulatedDevice.js**.

    > [!NOTE]
    > È consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling](Gestione degli errori temporanei).

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Aggiungere una coda del bus di servizio all'hub IoT ed eseguirvi il routing dei messaggi

In questa sezione, viene illustrato come creare una coda del bus di servizio, connetterla all'hub IoT e configurare l'hub IoT per inviare messaggi alla coda in base alla presenza di una proprietà del messaggio. Per altre informazioni su come elaborare i messaggi dalle code del bus di servizio, vedere [Get started with queues][lnk-sb-queues-node] (Introduzione alle code).

1. Creare una coda del bus di servizio, come descritto in [Get started with queues][lnk-sb-queues-node] (Introduzione alle code). Prendere nota dello spazio dei nomi e del nome della coda.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.

    ![Endpoint in hub IoT][30]

3. Nel pannello **Endpoint** fare clic su **Aggiungi** in alto per aggiungere la coda all'hub IoT. Denominare l'endpoint **CriticalQueue** e usare il menu a discesa per selezionare **Coda del bus di servizio**, lo spazio dei nomi del bus di servizio in cui si trova la coda e il nome della coda. Al termine, fare clic su **OK** nella parte inferiore.  

    ![Aggiunta di un endpoint][31]

4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **Messaggi del dispositivo** come origine dei dati. Immettere `level="critical"` come condizione e scegliere **CriticalQueue** come endpoint personalizzato, come endpoint della regola di routing. Fare clic su **Salva** nella parte inferiore.  

    ![Aggiunta di un route][32]

    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.

    ![Route di fallback][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Facoltativo) Lettura dell'endpoint della coda

In questa sezione si creerà un'app console di Node.js che legge i messaggi importanti dal bus di servizio dell'hub IoT. Per altre informazioni, vedere [Introduzione alle code][lnk-sb-queues-node]. 

1. Creare una cartella vuota denominata `readcriticalqueue`. Nella cartella `readcriticalqueue` creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

2. Al prompt dei comandi nella cartella `readcriticalqueue` eseguire il comando seguente per installare il pacchetto **azure**:

    ```cmd/sh
    npm install azure --save
    ```

3. Con un editor di testo creare un file **ReadCriticalQueue.js** nella cartella `readcriticalqueue`.

4. Aggiungere le istruzioni `require` seguenti all'inizio del file **ReadCriticalQueue.js**:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Aggiungere la dichiarazione di variabile seguente e sostituire il valore del segnaposto con la stringa di connessione del bus di servizio dell'hub IoT e con il nome della coda:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Aggiungere le due funzioni seguenti che stampano l'output nella console:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Salvare e chiudere il file **ReadCriticalQueue.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le tre applicazioni.

1. Per eseguire l'applicazione **ReadDeviceToCloudMessages.js**, in un prompt dei comandi o nella shell passare alla cartella readdevicetocloudmessages ed eseguire il comando seguente:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Eseguire read-d2c-messages][readd2c]

2. Per eseguire l'applicazione **ReadCriticalQueue.js**, in un prompt dei comandi o nella shell passare alla cartella readcriticalqueue ed eseguire il comando seguente:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Eseguire read-critical-queue][readqueue]

3. Per eseguire l'app **SimulatedDevice.js**, in un prompt dei comandi o nella shell passare alla cartella simulateddevice ed eseguire questo comando:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Eseguire simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facoltativo) Aggiungere un contenitore di archiviazione all'hub IoT ed eseguirvi il routing dei messaggi

In questa sezione, viene illustrato come creare un account di archiviazione, come connettere tale account all'hub IoT e come configurare l'hub IoT per inviare messaggi all'account in base alla presenza di una proprietà del messaggio. Per altre informazioni su come gestire l'archiviazione, vedere la [documentazione di Archiviazione di Azure][Archiviazione di Azure].

 > [!NOTE]
   > Se lo scenario non è limitato a un **endpoint**, è possibile configurare l'endpoint **StorageContainer** oltre all'endpoint **CriticalQueue** ed eseguire entrambi contemporaneamente.

1. Creare un account di archiviazione come descritto nella [documentazione di Archiviazione di Azure][lnk-storage]. Prendere nota del nome dell'account.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.

3. Nel pannello **Endpoint** selezionare l'endpoint **CriticalQueue** e fare clic su **Elimina**. Fare clic su **Sì** e quindi su **Aggiungi**. Assegnare un nome all'endpoint **StorageContainer**, usare gli elenchi a discesa per selezionare **Contenitore di archiviazione di Azure** e creare un **account di archiviazione** e un **contenitore di archiviazione**.  Prendere nota dei nomi.  Al termine, fare clic su **OK** nella parte inferiore. 

 > [!NOTE]
   > Se lo scenario non è limitato a un **endpoint**, non è necessario eliminare l'endpoint **CriticalQueue**.

4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **Messaggi del dispositivo** come origine dei dati. Immettere `level="storage"` come condizione e scegliere **StorageContainer** come endpoint personalizzato, come endpoint della regola di routing. Fare clic su **Salva** nella parte inferiore.  

    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.

1. Assicurarsi che l'applicazione precedente **SimulatedDevice.js** sia ancora in esecuzione. 

1. Nel portale di Azure passare all'account di archiviazione, in **Servizio BLOB** fare clic su **Esplora BLOB**.  Selezionare il contenitore, passare al file JSON e fare clic su di esso e quindi fare clic su **Download** per visualizzare i dati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare i messaggi da dispositivo a cloud in modo affidabile usando la funzionalità di routing dei messaggi dell'hub IoT.

L'esercitazione [Come inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d] descrive le modalità di invio dei messaggi ai dispositivi dal back-end della soluzione.

Per avere esempi di soluzioni complete che usano l'hub IoT, vedere [Azure IoT Suite][lnk-suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

Per ulteriori informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introduzione a hub IoT]: iot-hub-node-node-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/