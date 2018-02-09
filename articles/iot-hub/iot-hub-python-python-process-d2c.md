---
title: Routing dei messaggi tramite l'hub IoT di Azure (Python) | Microsoft Docs
description: Come elaborare i messaggi da dispositivo a cloud di Hub IoT di Azure usando le regole di routing e gli endpoint personalizzati per inviare i messaggi agli altri servizi di back-end.
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: f467437afb4bf89e77668cfd3e8a824bfbde9e10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Routing dei messaggi tramite l'hub IoT (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Questa esercitazione si basa sull'esercitazione [Introduzione a hub IoT].  L'esercitazione:

* Illustra come usare le regole di routing per inviare i messaggi da dispositivo a cloud con un semplice metodo basato sulla configurazione.
* Illustra come isolare i messaggi interattivi che richiedono un intervento immediato del back-end della soluzione per essere elaborati in seguito.  Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM.  Al contrario, i messaggi di punto dati, come ad esempio la telemetria di temperatura, vengono semplicemente inseriti in un motore di analisi.

Al termine di questa esercitazione, verranno eseguite tre app console Python:

* **SimulatedDevice.py**, una versione modificata dell'app creata durante l'esercitazione [Introduzione a hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud a intervalli casuali. Questa app usa il protocollo MQTT per comunicare con l'hub IoT.
* **ReadCriticalQueue.py**, che rimuove i messaggi importanti dalla coda del bus di servizio collegata all'hub IoT.

> [!NOTE]
> L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una versione di lavoro completa dell'esercitazione di [Introduzione a hub IoT] .
* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* [Node.js 4.0 o versione successiva][lnk-node-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Questo è un requisito per installare lo [strumento IoT Hub Explorer][lnk-iot-hub-explorer].
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Si consiglia anche la lettura della documentazione su [Archiviazione di Azure] e [Bus di servizio di Azure].


## <a name="send-interactive-messages-from-a-device-app"></a>Inviare messaggi interattivi da un'app per dispositivi
In questa sezione viene modificata l'app per dispositivi creata nell'esercitazione [Introduzione a hub IoT] per inviare occasionalmente messaggi che richiedono un intervento immediato.

1. Con un editor di testo aprire il file **SimulatedDevice.py**. Questo file contiene il codice dell'app **SimulatedDevice** creata durante l'esercitazione di [Introduzione a hub IoT].

2. Sostituire la funzione **iothub_client_telemetry_sample_run** con il codice seguente:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Questo metodo aggiunge in modo casuale la proprietà `"level": "critical"` e `"level": "storage"` ai messaggi inviati dal dispositivo, simulando così un messaggio che richiede l'intervento immediato del back-end dell'applicazione o uno che richiede l'archiviazione permanente. L'applicazione supporta il routing dei messaggi in base al corpo del messaggio.
   
   > [!NOTE]
   > È possibile usare le proprietà del messaggio per indirizzare i messaggi in diversi scenari, tra cui l'elaborazione del percorso a freddo, oltre all'esempio del percorso a caldo mostrato qui.

1. Salvare e chiudere il file **SimulatedDevice.py**.

    > [!NOTE]
    > Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling](Gestione degli errori temporanei).


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Aggiungere code all'hub IoT e indirizzarvi i messaggi

In questa sezione si creano una coda del bus di servizio e un account di archiviazione, li si connette all'hub IoT e si configura l'hub IoT per inviare messaggi alla coda in base alla presenza di una proprietà nel messaggio e tutti i messaggi all'account di archiviazione. Per altre informazioni su come elaborare i messaggi delle code del bus di servizio, vedere [Introduzione alle code][lnk-sb-queues-node] e per informazioni su come gestire l'archiviazione, vedere [Introduzione ad Archiviazione di Azure][Archiviazione di Azure].

1. Creare una coda del bus di servizio, come descritto in [Get started with queues][lnk-sb-queues-node] (Introduzione alle code). Prendere nota dello spazio dei nomi e del nome della coda.

    > [!NOTE]
    > Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

1. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.

    ![Endpoint in hub IoT][30]

1. Nel pannello **Endpoint** fare clic su **Aggiungi** in alto per aggiungere la coda all'hub IoT. Denominare l'endpoint **CriticalQueue** e usare il menu a discesa per selezionare **Coda del bus di servizio**, lo spazio dei nomi del bus di servizio in cui si trova la coda e il nome della coda. Al termine, fare clic su **OK** nella parte inferiore.  

    ![Aggiunta di un endpoint][31]

1. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. 

    ![Aggiunta di un route][34]

    Immettere un nome e selezionare **Messaggi del dispositivo** come origine dei dati. Scegliere **CriticalQueue** come endpoint personalizzato per la regola di gestione e immettere `level="critical"` come stringa di query. Fare clic su **Salva** nella parte inferiore.

    ![Dettagli della route][32]

    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.

    ![Route di fallback][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Facoltativo) Lettura dell'endpoint della coda

In questa sezione si crea un'app console Python che legge i messaggi importanti dal bus di servizio IoT. Per altre informazioni, vedere [Introduzione alle code][lnk-sb-queues-node]. 

1. Aprire un nuovo prompt dei comandi e installare Azure IoT Hub Device SDK per Python, come indicato di seguito. Chiudere il prompt dei comandi dopo l'installazione.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Per problemi di installazione del pacchetto **azure-servicebus** o per altre opzioni di installazione, vedere il [pacchetto azure-servicebus Python][lnk-python-service-bus].

1. Creare un file denominato **ReadCriticalQueue.py**. Aprire il file in un editor/IDE di Python di propria scelta, ad esempio IDLE.

1. Aggiungere il codice seguente per importare i moduli necessari dall'SDK per dispositivi:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Aggiungere il codice seguente e sostituire i segnaposto con i dati di connessione per il bus di servizio:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Aggiungere il codice seguente per connettersi al bus di servizio e leggere i dati: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Aggiungere infine la funzione main. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Salvare e chiudere il file **ReadCriticalQueue.py**. A questo punto è possibile eseguire le applicazioni.


## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Aprire un prompt dei comandi e installare IoT Hub Explorer. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Eseguire il comando seguente nel prompt dei comandi per avviare il monitoraggio dei messaggi dal dispositivo al cloud dal dispositivo. Usare la stringa di connessione dell'hub IoT nel segnaposto dopo `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Aprire un nuovo prompt dei comandi e passare alla directory contenente il file **SimulatedDevice.py**.

1. Eseguire il file **SimulatedDevice.py**, che invia periodicamente dati di telemetria all'hub IoT. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Per eseguire l'applicazione **ReadCriticalQueue**, in un prompt dei comandi o nella shell passare al file **ReadCriticalQueue.py** ed eseguire il comando seguente:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Osservare i messaggi del dispositivo nel prompt dei comandi eseguendo lo strumento IoT Hub Explorer dalla sezione precedente. Osservare i messaggi `critical` nell'applicazione **ReadCriticalQueue**.

    ![Messaggi dal dispositivo Python al cloud][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facoltativo) Aggiungere un contenitore di archiviazione all'hub IoT ed eseguirvi il routing dei messaggi

In questa sezione, viene illustrato come creare un account di archiviazione, come connettere tale account all'hub IoT e come configurare l'hub IoT per inviare messaggi all'account in base alla presenza di una proprietà del messaggio. Per altre informazioni su come gestire l'archiviazione, vedere la [documentazione di Archiviazione di Azure][Archiviazione di Azure].

 > [!NOTE]
   > Gli account dell'hub IoT creati nel livello _F1 Gratuito_ sono limitati a un **endpoint**. Se lo scenario non è limitato a un **endpoint**, è possibile configurare l'endpoint **StorageContainer** oltre all'endpoint **CriticalQueue** ed eseguire entrambi contemporaneamente.

1. Creare un account di archiviazione come descritto nella [documentazione di Archiviazione di Azure][lnk-storage]. Prendere nota del nome dell'account.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.

3. Nel pannello **Endpoint** selezionare l'endpoint **CriticalQueue** e fare clic su **Elimina**. Fare clic su **Sì** e quindi su **Aggiungi**. Assegnare un nome all'endpoint **StorageContainer**, usare gli elenchi a discesa per selezionare **Contenitore di archiviazione di Azure** e creare un **account di archiviazione** e un **contenitore di archiviazione**.  Prendere nota dei nomi.  Al termine, fare clic su **OK** nella parte inferiore. 

 > [!NOTE]
   > Gli account dell'hub IoT creati nel livello _F1 Gratuito_ sono limitati a un **endpoint**. Se lo scenario non è limitato a un **endpoint**, non è necessario eliminare l'endpoint **CriticalQueue**.

4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **Messaggi del dispositivo** come origine dei dati. Immettere `level="storage"` come condizione e scegliere **StorageContainer** come endpoint personalizzato, come endpoint della regola di routing. Fare clic su **Salva** nella parte inferiore.  

    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.

1. Assicurarsi che l'applicazione precedente **SimulatedDevice.py** sia ancora in esecuzione. 

1. Nel portale di Azure passare all'account di archiviazione, in **Servizio BLOB** fare clic su **Esplora BLOB**.  Selezionare il contenitore, passare al file JSON e fare clic su di esso e quindi fare clic su **Download** per visualizzare i dati.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare i messaggi da dispositivo a cloud in modo affidabile usando la funzionalità di routing dei messaggi dell'hub IoT.

Per avere esempi di soluzioni complete che usano l'hub IoT, vedere [Azure IoT Suite][lnk-suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

Per ulteriori informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introduzione a hub IoT]: iot-hub-python-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus