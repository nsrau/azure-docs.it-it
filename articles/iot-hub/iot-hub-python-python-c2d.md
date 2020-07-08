---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo da un hub IoT di Azure usando Azure IoT SDK per Python. Modificare un'app per dispositivo simulato per ricevere messaggi da cloud a dispositivo e modificare un'app back-end per inviare i messaggi da cloud a dispositivo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, tracking-python
ms.openlocfilehash: 12d25fd06ddfa8265dbf046093d3854f7fde4f33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609557"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Inviare messaggi da cloud a dispositivo con l'hub IoT (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. L'argomento di avvio rapido [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-python.md) (Inviare dati di telemetria da un dispositivo a un hub IoT) mostra come creare un hub IoT, eseguire il provisioning dell'identità di un dispositivo al suo interno e creare il codice di un'app per dispositivo simulato che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione è basata su [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-python.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). Illustra le operazioni seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

Altre informazioni sui [messaggi da cloud a dispositivo sono disponibili nella guida per sviluppatori dell'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, verranno eseguite due app console Python:

* **SimulatedDevice.py**, una versione modificata dell'app creata in inviare dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-python.md)Internet, che si connette all'hub Internet e riceve messaggi da cloud a dispositivo.

* **SendCloudToDeviceMessage.py**, che invia messaggi da cloud a dispositivo all'app per dispositivo simulato tramite l'hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ricevere messaggi nell'app per dispositivo simulato

In questa sezione si crea un'app console Python per simulare il dispositivo e si ricevono messaggi da cloud a dispositivo dall'hub IoT.

1. Da un prompt dei comandi nella directory di lavoro, installare l' **SDK per dispositivi dell'hub Azure Internet per Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Usando un editor di testo, creare un file denominato **SimulatedDevice.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SimulatedDevice.py**:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Aggiungere il codice seguente al file **SimulatedDevice.py**. Sostituire il `{deviceConnectionString}` valore del segnaposto con la stringa di connessione del dispositivo per il dispositivo creato nella Guida introduttiva inviare dati di [telemetria da un dispositivo a un hub](quickstart-send-telemetry-python.md) .

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Aggiungere la funzione seguente per stampare i messaggi ricevuti nella console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Aggiungere il codice seguente per inizializzare il client e attendere di ricevere il messaggio da cloud a dispositivo:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Aggiungere la funzione main seguente:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Salvare e chiudere il file **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub IoT creato in [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-python.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). Per inviare messaggi da cloud a dispositivo, è necessario che il servizio disponga dell'autorizzazione di **connessione al servizio**. Per impostazione predefinita, ogni hub IoT viene creato con un **servizio** con nome di criteri di accesso condiviso che concede tale autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

In questa sezione si crea un'app console Python che invia messaggi da cloud a dispositivo all'app di dispositivo simulato. È necessario l'ID del dispositivo aggiunto nell’argomento di avvio rapido [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-python.md) (Inviare dati di telemetria da un dispositivo a un hub IoT). È necessaria anche la stringa di connessione dell'hub IoT copiata in precedenza in [Ottenere la stringa di connessione dell’hub IoT](#get-the-iot-hub-connection-string).

1. Nella directory di lavoro aprire un prompt dei comandi e installare l' **SDK del servizio Hub Azure Internet per Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Usando un editor di testo, creare un file denominato **SendCloudToDeviceMessage.py**.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **SendCloudToDeviceMessage.py**:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.py**. Sostituire i `{iot hub connection string}` `{device id}` valori segnaposto e con la stringa di connessione dell'hub Internet e l'ID del dispositivo annotati in precedenza:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Aggiungere il codice seguente per inviare messaggi al dispositivo:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Aggiungere la funzione main seguente:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Salvare e chiudere il file **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella directory di lavoro, eseguire il comando seguente per restare in ascolto dei messaggi da cloud a dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Eseguire un'app di dispositivo simulato](./media/iot-hub-python-python-c2d/device-1.png)

1. Aprire un nuovo prompt dei comandi nella directory di lavoro ed eseguire il comando seguente per inviare messaggi da cloud a dispositivo:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Eseguire l'app per inviare il comando da cloud a dispositivo](./media/iot-hub-python-python-c2d/service.png)

1. Prendere nota dei messaggi ricevuti dal dispositivo.

    ![Messaggio ricevuto](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
