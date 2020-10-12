---
title: Pianificare processi con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per Python per implementare le app per dispositivi simulati e un'app di servizio per eseguire il processo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876683"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Pianificare e trasmettere processi (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Grazie a un processo, ad esempio, un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento.  L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivo gemello e proprietà: [Introduzione ai dispositivi gemelli](iot-hub-python-twin-getstarted.md) ed [esercitazione: Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)

* Metodi diretti: [Guida per gli sviluppatori dell'hub Internet-metodi diretti](iot-hub-devguide-direct-methods.md) ed [esercitazione: metodi diretti](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione illustra come:

* Creare un'app per dispositivo simulato Python con un metodo diretto che abilita **lockDoor**, che può essere chiamato dal back-end della soluzione.

* Creare un'app console Python che chiama il metodo diretto **lockDoor** nell'app per dispositivo simulato tramite un processo e aggiorna le proprietà desiderate tramite un processo del dispositivo.

Al termine di questa esercitazione si hanno due app Python:

**simDevice.py**, che si connette all'hub IoT con l'identità del dispositivo e riceve un metodo diretto **lockDoor**.

**scheduleJobService.py**, che chiama un metodo diretto nell'app del dispositivo simulato e aggiorna le proprietà desiderate di un dispositivo gemello tramite un processo.

> [!NOTE]
> **Azure IoT SDK per Python** non supporta direttamente la funzionalità **Processi**. Questa esercitazione offre invece una soluzione alternativa tramite l'uso dii timer e thread asincroni. Per altri aggiornamenti, vedere l'elenco delle funzionalità **Service Client SDK** nella pagina [Azure IoT SDK per Python](https://github.com/Azure/azure-iot-sdk-python).
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione viene creata un'applicazione console Python che risponde a un metodo chiamato dal cloud, che attiva un metodo **lockDoor** simulato.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-device**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando un editor di testo, creare un nuovo file **simDevice.py** nella directory di lavoro.

3. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **simDevice.py**. Sostituire `deviceConnectionString` con la stringa di connessione del dispositivo creato in precedenza:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Aggiungere il callback della funzione seguente per gestire il metodo **lockDoor**:

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Aggiungere un altro callback della funzione per gestire gli aggiornamenti dei dispositivi gemelli:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor** . Includere anche la routine `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Salvare e chiudere il file **simDevice.py**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

In questo articolo viene creato un servizio back-end che richiama un metodo diretto su un dispositivo e aggiorna il dispositivo gemello. Il servizio deve disporre dell'autorizzazione **Connect** per chiamare un metodo diretto in un dispositivo. Il servizio necessita inoltre delle autorizzazioni di **lettura** del registro di sistema e di **scrittura del registro** di sistema per leggere e scrivere il registro delle identità. Non sono presenti criteri di accesso condiviso predefiniti che contengono solo queste autorizzazioni, quindi è necessario crearne uno.

Per creare un criterio di accesso condiviso che concede le autorizzazioni di **connessione al servizio**, **lettura del registro**di sistema e **scrittura del registro** di sistema e per ottenere una stringa di connessione per questo criterio, attenersi alla procedura seguente:

1. Aprire l'hub Internet delle cose nel [portale di Azure](https://portal.azure.com). Il modo più semplice per ottenere l'hub Internet è quello di selezionare i **gruppi di risorse**, selezionare il gruppo di risorse in cui si trova l'hub Internet e quindi selezionare l'hub delle cose dall'elenco di risorse.

2. Nel riquadro sinistro dell'hub IoT selezionare **Criteri di accesso condiviso**.

3. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

4. Nel riquadro **Aggiungi criteri di accesso condiviso** immettere un nome descrittivo per il criterio. ad esempio: *serviceAndRegistryReadWrite*. In **autorizzazioni**selezionare **connessione al servizio** e **scrittura del registro** di sistema. la**lettura del registro di sistema** viene selezionata automaticamente quando si seleziona **scrittura del registro**di sistema. Selezionare quindi **Crea**.

    ![Mostrare come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Tornare al riquadro **criteri di accesso condivisi** e selezionare i nuovi criteri dall'elenco dei criteri.

6. In **Chiavi di accesso condivise** selezionare l'icona di copia per **Stringa di connessione -- Chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub IoT, vedere [Controllo dell'accesso e autorizzazioni](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli

In questa sezione si crea un'app console Python che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e aggiorna anche le proprietà desiderate del dispositivo gemello.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-hub**:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Usando un editor di testo, creare un nuovo file **scheduleJobService.py** nella directory di lavoro.

3. Aggiungere le seguenti `import` variabili e istruzioni all'inizio del file **scheduleJobService.py** . Sostituire il `{IoTHubConnectionString}` segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string). Sostituire il `{deviceId}` segnaposto con l'ID del dispositivo registrato in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub)Internet:

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Aggiungere la funzione seguente che viene usata per eseguire query per i dispositivi:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Aggiungere i seguenti metodi per eseguire i processi che chiamano il metodo diretto e il dispositivo gemello:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Aggiungere il codice seguente per pianificare i processi e aggiornare lo stato del dispositivo. Includere anche la routine `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Salvare e chiudere il file **scheduleJobService.py**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi, eseguire il comando riportato di seguito nella directory di lavoro per iniziare l'ascolto del metodo diretto di riavvio:

    ```cmd/sh
    python simDevice.py
    ```

2. Al prompt dei comandi successivo, eseguire il comando riportato di seguito nella directory di lavoro per attivare i processi per bloccare la porta e aggiornare il dispositivo gemello:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Nella console vengono visualizzate le risposte del dispositivo al metodo diretto e l'aggiornamento dei dispositivi gemelli.

    ![Esempio di processo dell'hub Internet 1--output del dispositivo](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Esempio di processo dell'hub Internet 2--output del dispositivo](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere [Come eseguire un aggiornamento del firmware](tutorial-firmware-update.md).
