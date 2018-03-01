---
title: Aggiornamento firmware del dispositivo con l'hub IoT di Azure (Python) | Microsoft Docs
description: Come usare la gestione dei dispositivi nell'hub IoT di Azure per avviare un aggiornamento del firmware del dispositivo. Usare Azure IoT SDK per Python per implementare un'app per dispositivo simulato e un'app di servizio che attiva l'aggiornamento del firmware.
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: e7ca0d95da3fd36e6dad081bad0aa5a178436159
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Usare la gestione dei dispositivi per avviare un aggiornamento del firmware del dispositivo (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT, offre indicazioni e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo schema viene usato nell'implementazione dell'aggiornamento del firmware per il dispositivo di esempio Intel Edison.

Questa esercitazione illustra come:

* Creare un'app console Python che chiama il metodo diretto firmwareUpdate nell'app dispositivo simulato tramite l'hub IoT.
* Creare un'app di dispositivo simulato che implementa un metodo diretto **firmwareUpdate**. Questo metodo avvia un processo in più fasi che attende di scaricare l'immagine del firmware, quindi la scarica e infine la applica. Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine di questa esercitazione, si avranno due app console Python:

**dmpatterns_fwupdate_service.py**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate aggiornate.

**dmpatterns_fwupdate_device.py**, che connette all'hub IoT con l'identità del dispositivo creata prima, riceve un metodo diretto firmwareUpdate, esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione viene creata un'app console Python che avvia un aggiornamento del firmware remoto in un dispositivo. L'applicazione usa un metodo diretto per avviare l'aggiornamento e usa le query di dispositivo gemello per ottenere periodicamente lo stato di aggiornamento del firmware attivo.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iothub-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando un editor di testo, nella directory di lavoro creare un file **dmpatterns_getstarted_service.py**.

1. Aggiungere le variabili e le istruzioni "import" seguenti all'inizio del file **dmpatterns_getstarted_service.py**. Sostituire `IoTHubConnectionString` e `deviceId` con i valori indicati in precedenza:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Aggiungere la funzione seguente per chiamare il metodo diretto e visualizzare il valore della proprietà segnalata da firmwareUpdate. Aggiungere anche la routine `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
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
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Salvare e chiudere il file **dmpatterns_fwupdate_service.py**.


## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione verrà illustrato come:

* Creare un'app console Python che risponde a un metodo diretto chiamato dal cloud
* Attivare un aggiornamento del firmware simulato
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo completamento di un aggiornamento del firmware

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iothub-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando un editor di testo, creare un file **dmpatterns_fwupdate_device.py**.

1. Aggiungere le variabili e le istruzioni "import" seguenti all'inizio del file **dmpatterns_fwupdate_device.py**. Sostituire `deviceConnectionString` con la stringa di connessione del dispositivo dall'hub IoT:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Aggiungere le funzioni seguenti che consentono di fornire aggiornamenti delle proprietà segnalate e implementare il metodo diretto:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Aggiungere le funzioni seguenti che simulano il download e l'applicazione dell'immagine del firmware:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Aggiungere la funzione seguente che inizializza le proprietà segnalate del dispositivo gemello e attende la chiamata del metodo diretto. Aggiungere anche la routine `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling](https://msdn.microsoft.com/library/hh675232.aspx) (Gestione degli errori temporanei).
> 


## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi eseguire il comando seguente per iniziare a stare in ascolto del metodo diretto di riavvio.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. A un altro prompt dei comandi eseguire il comando seguente per attivare il riavvio remoto ed eseguire una query per il dispositivo gemello per trovare la data e l'ora dell'ultimo riavvio.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Nella console viene visualizzata la risposta del dispositivo al metodo diretto. Annotare la modifica nelle proprietà segnalate durante l'aggiornamento del firmware.

    ![output programma][1]


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
