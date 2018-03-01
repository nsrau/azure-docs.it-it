---
title: Pianificare processi con l'hub IoT di Azure (Python) | Microsoft Docs
description: "Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per Python per implementare le app per dispositivi simulati e un'app di servizio per eseguire il processo."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 3b6daf1ce1116ea3787550f9dac8d90b3751f9af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Pianificare e trasmettere processi (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Grazie a un processo, ad esempio, un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento.  L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivi gemelli e proprietà: [Introduzione ai dispositivi gemelli][lnk-get-started-twin] ed [Esercitazione: Come usare le proprietà dei dispositivi gemelli][lnk-twin-props]
* Metodi diretti: [Guida per sviluppatori dell'hub IoT - Metodi diretti][lnk-dev-methods] ed [Esercitazione: Metodi diretti][lnk-c2d-methods]

Questa esercitazione illustra come:

* Creare un'app per dispositivo simulato Python con un metodo diretto che abilita **lockDoor**, che può essere chiamato dal back-end della soluzione.
* Creare un'app console Python che chiama il metodo diretto **lockDoor** nell'app per dispositivo simulato tramite un processo e aggiorna le proprietà desiderate tramite un processo del dispositivo.

Al termine di questa esercitazione si hanno due app Python:

**simDevice.py**, che si connette all'hub IoT con l'identità del dispositivo e riceve un metodo diretto **lockDoor**.

**scheduleJobService.py**, che chiama un metodo diretto nell'app del dispositivo simulato e aggiorna le proprietà desiderate di un dispositivo gemello tramite un processo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

> [!NOTE]
> **Azure IoT SDK per Python** non supporta direttamente la funzionalità **Processi**. Questa esercitazione offre invece una soluzione alternativa tramite l'uso dii timer e thread asincroni. Per altri aggiornamenti, vedere l'elenco delle funzionalità **Service Client SDK** nella pagina [Azure IoT SDK per Python](https://github.com/Azure/azure-iot-sdk-python). 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione viene creata un'applicazione console Python che risponde a un metodo chiamato dal cloud, che attiva un metodo **lockDoor** simulato.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando un editor di testo, creare un nuovo file **simDevice.py** nella directory di lavoro.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **simDevice.py**. Sostituire `deviceConnectionString` con la stringa di connessione del dispositivo creato in precedenza:
   
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

1. Aggiungere il callback della funzione seguente per gestire il metodo **lockDoor**:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Aggiungere un altro callback della funzione per gestire gli aggiornamenti dei dispositivi gemelli:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor**. Includere anche la routine `main`:
   
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

1. Salvare e chiudere il file **simDevice.py**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli
In questa sezione si creerà un'app console Python che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e verranno aggiornate le proprietà del dispositivo gemello.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iot-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando un editor di testo, creare un nuovo file **scheduleJobService.py** nella directory di lavoro.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **scheduleJobService.py**:
   
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

1. Aggiungere la funzione seguente che viene usata per eseguire query per i dispositivi:
   
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

1. Aggiungere i seguenti metodi per eseguire i processi che chiamano il metodo diretto e il dispositivo gemello:
   
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

1. Aggiungere il codice seguente per pianificare i processi e aggiornare lo stato del dispositivo. Includere anche la routine `main`:
   
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

1. Salvare e chiudere il file **scheduleJobService.py**.


## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi, eseguire il comando riportato di seguito nella directory di lavoro per iniziare l'ascolto del metodo diretto di riavvio:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Al prompt dei comandi successivo, eseguire il comando riportato di seguito nella directory di lavoro per attivare i processi per bloccare la porta e aggiornare il dispositivo gemello:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Nella console vengono visualizzate le risposte del dispositivo al metodo diretto e l'aggiornamento dei dispositivi gemelli.

    ![output dispositivo][1]

    ![output servizio][2]


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Introduzione a IoT Edge di Azure][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
