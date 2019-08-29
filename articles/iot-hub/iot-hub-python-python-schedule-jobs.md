---
title: Pianificare processi con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per Python per implementare le app per dispositivi simulati e un'app di servizio per eseguire il processo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: a1b206b3be4cf012d7d0cd399cf2a67d853537b1
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147400"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Pianificare e trasmettere processi (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Grazie a un processo, ad esempio, un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento.  L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivo gemello e proprietà: [Introduzione ai dispositivi gemelli](iot-hub-python-twin-getstarted.md) ed [esercitazione: Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)

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

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione viene creata un'applicazione console Python che risponde a un metodo chiamato dal cloud, che attiva un metodo **lockDoor** simulato.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > I pacchetti PIP per Azure-iothub-Service-client e Azure-iothub-Device-client sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando un editor di testo, creare un nuovo file **simDevice.py** nella directory di lavoro.

3. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **simDevice.py**. Sostituire `deviceConnectionString` con la stringa di connessione del dispositivo creato in precedenza:

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Aggiungere il callback della funzione seguente per gestire il metodo **lockDoor**:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Aggiungere un altro callback della funzione per gestire gli aggiornamenti dei dispositivi gemelli:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor**. Includere anche la routine `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. Salvare e chiudere il file **simDevice.py**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end che richiama un metodo diretto su un dispositivo e aggiorna il dispositivo gemello. Il servizio deve disporre dell'autorizzazione **Connect** per chiamare un metodo diretto in un dispositivo. Il servizio necessita inoltre delle autorizzazioni di **lettura** del registro di sistema e di **scrittura del registro** di sistema per leggere e scrivere il registro delle identità. Non sono presenti criteri di accesso condiviso predefiniti che contengono solo queste autorizzazioni, quindi è necessario crearne uno.

Per creare un criterio di accesso condiviso che concede le autorizzazioni di **connessione al servizio**, **lettura del registro**di sistema e **scrittura del registro** di sistema e per ottenere una stringa di connessione per questo criterio, attenersi alla procedura seguente:

1. Aprire l'hub Internet delle cose nel [portale di Azure](https://portal.azure.com). Il modo più semplice per ottenere l'hub Internet è quello di selezionare i **gruppi di risorse**, selezionare il gruppo di risorse in cui si trova l'hub Internet e quindi selezionare l'hub delle cose dall'elenco di risorse.

2. Nel riquadro sinistro dell'hub Internet, selezionare **criteri di accesso condiviso**.

3. Dal menu superiore sopra l'elenco di criteri selezionare **Aggiungi**.

4. Nel riquadro **Aggiungi criteri di accesso condiviso** immettere un nome descrittivo per il criterio. ad esempio: *serviceAndRegistryReadWrite*. In **autorizzazioni**selezionare **connessione al servizio** e **scrittura del registro** di sistema. la**lettura del registro di sistema** viene selezionata automaticamente quando si seleziona **scrittura del registro**di sistema. Selezionare quindi **Crea**.

    ![Mostra come aggiungere nuovi criteri di accesso condiviso](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Tornare al riquadro **criteri di accesso condivisi** e selezionare i nuovi criteri dall'elenco dei criteri.

6. In **chiavi di accesso condivise**selezionare l'icona di copia per la **stringa di connessione--chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub Internet, vedere [controllo di accesso e autorizzazioni](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli

In questa sezione si creerà un'app console Python che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e verranno aggiornate le proprietà del dispositivo gemello.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-service-client**:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > I pacchetti PIP per Azure-iothub-Service-client e Azure-iothub-Device-client sono attualmente disponibili solo per il sistema operativo Windows. Per Linux/Mac OS, vedere le sezioni specifiche per Linux e Mac OS dell' [ambiente di sviluppo per Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando un editor di testo, creare un nuovo file **scheduleJobService.py** nella directory di lavoro.

3. Aggiungere le seguenti `import` variabili e istruzioni all'inizio del file **scheduleJobService.py** . Sostituire il `{IoTHubConnectionString}` segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string). Sostituire il `{deviceId}` segnaposto con l'ID del dispositivo registrato in [registrare un nuovo dispositivo nell'hub](#register-a-new-device-in-the-iot-hub)Internet:

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Aggiungere la funzione seguente che viene usata per eseguire query per i dispositivi:

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. Aggiungere i seguenti metodi per eseguire i processi che chiamano il metodo diretto e il dispositivo gemello:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

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

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
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

Per continuare a usare i modelli di gestione di hub e dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware Air, vedere [come eseguire un aggiornamento del firmware](tutorial-firmware-update.md).