---
title: Introduzione alla gestione dei dispositivi nell'hub IoT di Azure (Python) | Microsoft Docs
description: Come usare la gestione dei dispositivi dell'hub IoT per riavviare un dispositivo remoto. Usare Azure IoT SDK per Python per implementare un'app di dispositivo simulato che include un metodo diretto e un'app di servizio che richiama il metodo diretto.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 287dbd3d6da4aa2bf5bd1da652cdeaeda3136321
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907758"
---
# <a name="get-started-with-device-management-python"></a>Introduzione alla gestione dei dispositivi (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.

* Creare un'app di dispositivo simulato contenente un metodo diretto per il riavvio del dispositivo. I metodi diretti vengono richiamati dal cloud.

* Creare un'app console Python che chiama il metodo diretto di riavvio nell'app di dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione, si avranno due app console Python:

* **dmpatterns_getstarted_device.py**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve un metodo diretto di riavvio, simula un riavvio fisico e segnala il tempo impiegato per l'ultimo riavvio.

* **dmpatterns_getstarted_service.py**, che chiama un metodo diretto nell'app di dispositivo simulato e visualizza la risposta e le proprietà segnalate aggiornate.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Di seguito sono riportate le istruzioni di installazione per i prerequisiti.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione verrà illustrato come:

* Creare un'app console Python che risponde a un metodo diretto chiamato dal cloud

* Simulare un riavvio del dispositivo

* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > I pacchetti PIP per Azure-iothub-Service-client e Azure-iothub-Device-client sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando un editor di testo, creare un file denominato **dmpatterns_getstarted_device. py** nella directory di lavoro.

3. Aggiungere le istruzioni `import` seguenti all'inizio del file **dmpatterns_getstarted_device.py**.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

4. Aggiungere variabili, tra cui una variabile **CONNECTION_STRING** e l'inizializzazione del client.  Sostituire il `{deviceConnectionString}` valore del segnaposto con la stringa di connessione del dispositivo. Questa stringa di connessione è stata copiata in precedenza in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

5. Aggiungere i callback delle funzioni seguenti per implementare il metodo diretto nel dispositivo.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

6. Avviare il listener del metodo diretto e attendere.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Salvare e chiudere il file **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione viene creata un'app console Python che attiva un riavvio remoto in un dispositivo usando un metodo diretto. L'app esegue query nel dispositivo gemello per ottenere l'ora dell'ultimo riavvio del dispositivo in questione.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-service-client**:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > I pacchetti PIP per Azure-iothub-Service-client e Azure-iothub-Device-client sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando un editor di testo, creare un file denominato **dmpatterns_getstarted_service. py** nella directory di lavoro.

3. Aggiungere le istruzioni `import` seguenti all'inizio del file **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. Aggiungere le dichiarazioni di variabili seguenti. Sostituire il `{IoTHubConnectionString}` valore del segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string). Sostituire il `{deviceId}` valore del segnaposto con l'ID del dispositivo registrato in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub)Internet.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Aggiungere la funzione seguente per richiamare il metodo del dispositivo per riavviare il dispositivo di destinazione, quindi eseguire una query per i dispositivi gemelli e ottenere la data e l'ora dell'ultimo riavvio.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Salvare e chiudere il file **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Eseguire le app

A questo punto si è pronti per eseguire le app.

1. Al prompt dei comandi eseguire il comando seguente per iniziare a stare in ascolto del metodo diretto di riavvio.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. A un altro prompt dei comandi eseguire il comando seguente per attivare il riavvio remoto ed eseguire una query per il dispositivo gemello per trovare la data e l'ora dell'ultimo riavvio.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

   Di seguito viene illustrata la risposta del dispositivo al metodo diretto di riavvio:

   ![Output app dispositivo simulato](./media/iot-hub-python-python-device-management-get-started/device.png)

   Di seguito viene illustrato il servizio che chiama il metodo diretto di riavvio e il polling del dispositivo gemello per lo stato:

   ![Attivare l'output del servizio di riavvio](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]