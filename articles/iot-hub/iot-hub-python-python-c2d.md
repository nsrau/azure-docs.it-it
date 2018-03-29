---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo da un hub IoT di Azure usando Azure IoT SDK per Python. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 9a669bda2a41ea6aa474549ee9798889523a27f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. L'esercitazione [Introduzione all'hub IoT di Azure] illustra come creare un hub IoT, effettuare il provisioning dell'identità di un dispositivo al suo interno e creare il codice di un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione si basa su [Introduzione all'hub IoT di Azure]. Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.
* Ricevere messaggi da cloud a dispositivo in un dispositivo.
* Dal back-end della soluzione richiedere l'acknowledgement di recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub IoT.

Per altre informazioni sui messaggi da cloud a dispositivo, vedere la[Guida per gli sviluppatori dell'hub IoT][IoT Hub developer guide - C2D].

Al termine di questa esercitazione, verranno eseguite due app console Python:

* **SimulatedDevice.py**, una versione modificata dell'app creata in [Introduzione all'hub IoT di Azure], che si connette all'hub IoT e riceve i messaggi da cloud a dispositivo.
* **SendCloudToDeviceMessage.py**, che invia un messaggio da cloud a dispositivo all'app di dispositivo simulato attraverso l'hub IoT e quindi riceve l'acknowledgement di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere il [Centro per sviluppatori Azure IoT].
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

> [!NOTE]
> I pacchetti *pip* per `azure-iothub-service-client` e `azure-iothub-device-client` sono attualmente disponibili solo per il sistema operativo Windows. Per Linux o Mac OS, vedere le sezioni dedicate ai sistemi operativi Linux e Mac OS nel post [Prepare your development environment for Python][lnk-python-devbox] (Preparare l'ambiente di sviluppo per Python).
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato
In questa sezione si crea un'app console Python per simulare il dispositivo e si ricevono messaggi da cloud a dispositivo dall'hub IoT.

1. Usando un editor di testo, creare un file **SimulatedDevice.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SimulatedDevice.py**:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Aggiungere il codice seguente al file **SimulatedDevice.py**. Sostituire il valore del segnaposto "{deviceConnectionString}" con la stringa di connessione per il dispositivo creato durante l'esercitazione [Introduzione all'hub IoT di Azure]:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Aggiungere la funzione seguente per stampare i messaggi ricevuti nella console:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Aggiungere il codice seguente per inizializzare il client e attendere di ricevere il messaggio da cloud a dispositivo:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Aggiungere la funzione main seguente:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Salvare e chiudere il file **SimulatedDevice.py**.


## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo
In questa sezione si crea un'app console Python che invia messaggi da cloud a dispositivo all'app di dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure] . È necessaria anche la stringa di connessione per l'hub IoT, disponibile nel [portale di Azure].

1. Usando un editor di testo, creare un file **SendCloudToDeviceMessage.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SendCloudToDeviceMessage.py**:
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.py**. Sostituire il valore del segnaposto "{IoTHubConnectionString}" con la stringa di connessione dell'hub IoT per l'hub creato durante l'esercitazione [Introduzione all'hub IoT di Azure]. Sostituire il segnaposto "{deviceId}" con l'ID del dispositivo aggiunto nell'esercitazione [Introduzione all'hub IoT di Azure]:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Aggiungere la funzione seguente per stampare i messaggi di feedback nella console:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Aggiungere il codice seguente per inviare un messaggio al dispositivo e gestire il messaggio di feedback quando il dispositivo accetta il messaggio da cloud a dispositivo:
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. Aggiungere la funzione main seguente:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Salvare e chiudere il file **SendCloudToDeviceMessage.py**.


## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Aprire un prompt dei comandi e installare **Azure IoT Hub SDK per dispositivi per Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. Al prompt dei comandi eseguire il comando seguente per mettersi in ascolto dei messaggi da cloud a dispositivo:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Eseguire un'app di dispositivo simulato][img-simulated-device]

1. Aprire un nuovo prompt dei comandi e installare **Azure IoT Hub SDK per servizi per Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. A un prompt dei comandi eseguire il comando seguente per inviare un messaggio da cloud a dispositivo e attendere il feedback del messaggio:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Eseguire l'app per inviare il comando da cloud a dispositivo][img-send-command]
   
1. Si noti il messaggio ricevuto dal dispositivo.

    ![Messaggio ricevuto][img-message-recieved]


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo. 

Per avere degli esempi di soluzioni complete che utilizzano l'hub IoT, vedere la [Azure IoT Suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Introduzione all'hub IoT di Azure]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portale di Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
