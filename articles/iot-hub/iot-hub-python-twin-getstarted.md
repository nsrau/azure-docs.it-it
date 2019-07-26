---
title: Introduzione ai dispositivi gemelli dell'hub IoT di Azure (Python) | Microsoft Docs
description: Come usare i dispositivi gemelli dell'hub IoT di Azure per aggiungere tag e quindi usare una query dell'hub IoT. Usare Azure IoT SDK per Python per implementare l'app di dispositivo simulato e un'app di servizio che aggiunge i tag ed esegue la query dell'hub IoT.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 53e3d32497c7aae6c584d23b9baddbaeaf1bd822
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405869"
---
# <a name="get-started-with-device-twins-python"></a>Introduzione ai dispositivi gemelli (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al termine di questa esercitazione, si avranno due app console Python:

* **AddTagsAndQuery**, un'app back-end Python che aggiunge tag ed esegue query sui dispositivi gemelli.

* **ReportConnectivity.js**, un'app Python che simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e segnala la condizione della connettività.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) riporta informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2. x o 3. x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python](https://pip.pypa.io/en/stable/installing/).

* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) per consentire l'uso di DLL native da Python.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

> [!NOTE]
> I pacchetti *pip* per `azure-iothub-service-client` e `azure-iothub-device-client` sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
>

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea un'app console Python che aggiunge i metadati della posizione al dispositivo gemello associato all' **{ID dispositivo}** . Viene quindi eseguita una query sui dispositivi gemelli archiviati nell'hub IoT selezionando i dispositivi situati a Redmond e quindi quelli che segnalano una connessione alla rete cellulare.

1. Aprire un prompt dei comandi e installare **Azure IoT Hub Service SDK per Python**. Chiudere il prompt dei comandi dopo l'installazione dell'SDK.

   ```
   pip install azure-iothub-service-client
   ```

2. Usando un editor di testo, creare un nuovo file **AddTagsAndQuery.py**.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK del servizio:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Aggiungere il codice seguente, sostituendo il segnaposto `[Device Id]` per `[IoTHub Connection String]` e con la stringa di connessione per l'hub Internet e l'ID del dispositivo creato nelle sezioni precedenti.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Aggiungere il codice seguente al file **AddTagsAndQuery.py**:

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    L'oggetto **Registry** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice inizializza prima l'oggetto **Registry**, quindi aggiorna il dispositivo gemello per **deviceId** e infine esegue due query. La prima seleziona solo i dispositivi gemelli dei dispositivi situati nello stabilimento **Redmond43** e la seconda affina la query per selezionare solo i dispositivi che sono anche connessi tramite la rete cellulare.

6. Aggiungere il codice seguente alla fine di **AddTagsAndQuery.py** per implementare la funzione **iothub_service_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Eseguire l'applicazione con:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Nei risultati si noterà un dispositivo per la query che cerca tutti i dispositivi situati in **Redmond43** e nessuno per la query che limita i risultati ai dispositivi che usano una rete cellulare.

    ![prima query che Mostra tutti i dispositivi a Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

Nella sezione successiva si crea un'app per dispositivo che segnala le informazioni sulla connettività e modifica il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare l'app per dispositivo

In questa sezione si crea un'app console Python che si connette all'hub come **{ID dispositivo}** e quindi aggiorna le proprietà segnalate del dispositivo gemello per contenere le informazioni connesse usando una rete cellulare.

1. Aprire un prompt dei comandi e installare **Azure IoT Hub Service SDK per Python**. Chiudere il prompt dei comandi dopo l'installazione dell'SDK.

    ```
    pip install azure-iothub-device-client
    ```

2. Usando un editor di testo, creare un nuovo file **ReportConnectivity.py**.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK del servizio:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Aggiungere il codice seguente, sostituendo il segnaposto per `[IoTHub Device Connection String]` con la stringa di connessione del dispositivo hub IoT creata nelle sezioni precedenti.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Aggiungere il codice seguente al file **ReportConnectivity.py** per implementare la funzionalità dei dispositivi gemelli:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    L'oggetto **Client** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice precedente, dopo avere inizializzato l'oggetto **Client**, recupera il dispositivo gemello per il dispositivo e aggiorna le proprietà segnalate con le informazioni sulla connettività.

6. Aggiungere il codice seguente alla fine di **ReportConnectivity.py** per implementare la funzione **iothub_client_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Eseguire l'app per dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Dovrebbe venire visualizzata una conferma che i dispositivi gemelli sono stati aggiornati.

    ![Dispositivi gemelli](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Ora che il dispositivo ha segnalato le informazioni sulla connettività, verrà visualizzato in entrambe le query. Tornare indietro ed eseguire di nuovo le query:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Questa volta il **{ID dispositivo}** verrà visualizzato in entrambi i risultati della query.

    ![Seconda query](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo simulato per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come eseguire una query di queste informazioni usando il registro.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](quickstart-send-telemetry-python.md).

* Configurare i dispositivi usando le proprietà desiderate del dispositivo gemello con l'esercitazione [usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md) .

* Controllare i dispositivi in modo interattivo, ad esempio l'accensione di una ventola da un'app controllata dall'utente, con l'esercitazione [usare i metodi diretti](quickstart-control-device-python.md) .