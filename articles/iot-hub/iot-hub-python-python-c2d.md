---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo da un hub IoT di Azure usando Azure IoT SDK per Python. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 340d728a45da4e392c85ab4ba7ce822f7762da3b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147371"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-python.md) Internet viene illustrato come creare un hub Internet, effettuare il provisioning di un'identità del dispositivo e codificare un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sull' [invio di dati di telemetria da un dispositivo a un hub](quickstart-send-telemetry-python.md)Internet. Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, richiedere il riconoscimento del recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub Internet.

È possibile trovare altre informazioni sui messaggi da cloud a dispositivo nella Guida per gli [sviluppatori dell'hub](iot-hub-devguide-messaging.md)Internet.

Al termine di questa esercitazione, verranno eseguite due app console Python:

* **SimulatedDevice.py**, una versione modificata dell'app creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-python.md)Internet, che si connette all'hub Internet e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage.py**, che invia un messaggio da cloud a dispositivo all'app per dispositivo simulato tramite l'hub Internet e quindi riceve la conferma di recapito.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione si crea un'app console Python per simulare il dispositivo e si ricevono messaggi da cloud a dispositivo dall'hub IoT.

1. Usando un editor di testo, creare un file **SimulatedDevice.py**.

2. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SimulatedDevice.py**:

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

3. Aggiungere il codice seguente al file **SimulatedDevice.py**. Sostituire il valore del segnaposto "{deviceConnectionString}" con la stringa di connessione del dispositivo creata nella Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-python.md) .

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Aggiungere la funzione seguente per stampare i messaggi ricevuti nella console:

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

5. Aggiungere il codice seguente per inizializzare il client e attendere di ricevere il messaggio da cloud a dispositivo:

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

6. Aggiungere la funzione main seguente:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Salvare e chiudere il file **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub Internet delle cose creato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-python.md). Per inviare messaggi da cloud a dispositivo, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Python che invia messaggi da cloud a dispositivo all'app di dispositivo simulato. È necessario l'ID dispositivo del dispositivo aggiunto nella Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-python.md) . È necessaria anche la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

1. Usando un editor di testo, creare un file **SendCloudToDeviceMessage.py**.

2. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SendCloudToDeviceMessage.py**:

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

3. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.py**. Sostituire i valori segnaposto "{String Hub Connection String}" e "{ID dispositivo}" con la stringa di connessione dell'hub Internet e l'ID del dispositivo annotati in precedenza:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Aggiungere la funzione seguente per stampare i messaggi di feedback nella console:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Aggiungere il codice seguente per inviare un messaggio al dispositivo e gestire il messaggio di feedback quando il dispositivo accetta il messaggio da cloud a dispositivo:

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

6. Aggiungere la funzione main seguente:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Salvare e chiudere il file **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Aprire un prompt dei comandi e installare **Azure IoT Hub SDK per dispositivi per Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Al prompt dei comandi eseguire il comando seguente per mettersi in ascolto dei messaggi da cloud a dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Eseguire un'app di dispositivo simulato](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Aprire un nuovo prompt dei comandi e installare **Azure IoT Hub SDK per servizi per Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. A un prompt dei comandi eseguire il comando seguente per inviare un messaggio da cloud a dispositivo e attendere il feedback del messaggio:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Eseguire l'app per inviare il comando da cloud a dispositivo](./media/iot-hub-python-python-c2d/send-command.png)

5. Prendere nota del messaggio ricevuto dal dispositivo.

    ![Messaggio ricevuto](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
