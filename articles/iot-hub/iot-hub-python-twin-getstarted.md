---
title: Introduzione ai dispositivi gemelli dell'hub IoT di Azure (Python) | Microsoft Docs
description: Come usare i dispositivi gemelli dell'hub IoT di Azure per aggiungere tag e quindi usare una query dell'hub IoT. Usare Azure IoT SDK per Python per implementare l'app di dispositivo simulato e un'app di servizio che aggiunge i tag ed esegue la query dell'hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, tracking-python
ms.openlocfilehash: 142ac150e863b69a4580812478828062c1f5c6c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84605596"
---
# <a name="get-started-with-device-twins-python"></a>Introduzione ai dispositivi gemelli (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al termine di questa esercitazione, si avranno due app console Python:

* **AddTagsAndQuery**, un'app back-end Python che aggiunge tag ed esegue query sui dispositivi gemelli.

* **ReportConnectivity.js**, un'app Python che simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e segnala la condizione della connettività.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea un'app console Python che aggiunge i metadati della posizione al dispositivo gemello associato all' **{ID dispositivo}**. Viene quindi eseguita una query sui dispositivi gemelli archiviati nell'hub IoT selezionando i dispositivi situati a Redmond e quindi quelli che segnalano una connessione alla rete cellulare.

1. Nella directory di lavoro aprire un prompt dei comandi e installare l' **SDK del servizio Hub Azure Internet per Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Usando un editor di testo, creare un nuovo file **AddTagsAndQuery.py**.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK del servizio:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Aggiungere il codice seguente. Sostituire `[IoTHub Connection String]` con la stringa di connessione dell'hub IoT copiata in [Ottenere la stringa di connessione dell'hub IoT](#get-the-iot-hub-connection-string). Sostituire `[Device Id]` con l'ID del dispositivo registrato in [registrare un nuovo dispositivo nell'hub Internet delle](#register-a-new-device-in-the-iot-hub)cose.
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Aggiungere il codice seguente al file **AddTagsAndQuery.py**:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    L'oggetto **IoTHubRegistryManager** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice inizializza prima di tutto l'oggetto **IoTHubRegistryManager** , quindi aggiorna il dispositivo gemello per **DEVICE_ID**e infine esegue due query. Il primo seleziona solo i dispositivi gemelli dei dispositivi situati nello stabilimento **Redmond43** e la seconda affina la query per selezionare solo i dispositivi che sono anche connessi tramite una rete cellulare.

6. Aggiungere il codice seguente alla fine di **AddTagsAndQuery.py** per implementare la funzione **iothub_service_sample_run**:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Eseguire l'applicazione con:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Nei risultati si noterà un dispositivo per la query che cerca tutti i dispositivi situati in **Redmond43** e nessuno per la query che limita i risultati ai dispositivi che usano una rete cellulare.

    ![prima query che Mostra tutti i dispositivi a Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Nella sezione successiva si crea un'app per dispositivo che segnala le informazioni sulla connettività e modifica il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare l'app per dispositivo

In questa sezione si crea un'app console Python che si connette all'hub come **{ID dispositivo}** e quindi aggiorna le proprietà segnalate del dispositivo gemello per contenere le informazioni connesse usando una rete cellulare.

1. Da un prompt dei comandi nella directory di lavoro, installare l' **SDK per dispositivi dell'hub Azure Internet per Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando un editor di testo, creare un nuovo file **ReportConnectivity.py**.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK per dispositivi:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Aggiungere il codice seguente. Sostituire il `[IoTHub Device Connection String]` valore del segnaposto con la stringa di connessione del dispositivo copiata in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Aggiungere il codice seguente al file **ReportConnectivity.py** per implementare la funzionalità dei dispositivi gemelli:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    L'oggetto **IoTHubModuleClient** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice precedente, dopo avere inizializzato l'oggetto **IoTHubModuleClient** , recupera il dispositivo gemello per il dispositivo e aggiorna la relativa proprietà segnalata con le informazioni sulla connettività.

6. Aggiungere il codice seguente alla fine di **ReportConnectivity.py** per implementare la funzione **iothub_client_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Eseguire l'app per dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Verrà visualizzata la conferma che le proprietà segnalate dal dispositivo gemello sono state aggiornate.

    ![aggiornare le proprietà segnalate dall'app per dispositivi](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Ora che il dispositivo ha segnalato le informazioni sulla connettività, verrà visualizzato in entrambe le query. Tornare indietro ed eseguire di nuovo le query:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Questa volta il **{ID dispositivo}** verrà visualizzato in entrambi i risultati della query.

    ![seconda query nell'app di servizio](./media/iot-hub-python-twin-getstarted/service-2.png)

    Nell'app per dispositivi verrà visualizzata la conferma della ricezione della patch delle proprietà desiderate inviate dall'app di servizio.

    ![ricevere le proprietà desiderate nell'app per dispositivi](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo simulato per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come eseguire una query di queste informazioni usando il registro.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](quickstart-send-telemetry-python.md).

* Configurare i dispositivi usando le proprietà desiderate del dispositivo gemello con l'esercitazione [usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md) .

* Controllare i dispositivi in modo interattivo, ad esempio l'accensione di una ventola da un'app controllata dall'utente, con l'esercitazione [usare i metodi diretti](quickstart-control-device-python.md) .
