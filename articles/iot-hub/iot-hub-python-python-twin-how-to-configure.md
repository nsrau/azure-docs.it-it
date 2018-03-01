---
title: "Usare le proprietà di un dispositivo gemello dell'hub IoT di Azure (Python) | Microsoft Docs"
description: "Come usare le proprietà di un dispositivo gemello dell'hub IoT di Azure per configurare dispositivi. Usare Azure IoT SDK per Python per implementare un'app per dispositivo simulato e un'app di servizio che modifica la configurazione di un dispositivo usando un dispositivo gemello."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo
ms.openlocfilehash: d0d5a30a76068eb3212124fd14e7ea1616b75708
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Usare le proprietà desiderate per configurare i dispositivi (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al termine di questa esercitazione, si avranno due app console Python:

* **SimulateDeviceConfiguration.py**, un'app per dispositivo simulata che attende un aggiornamento della configurazione desiderata e segnala lo stato di un processo di aggiornamento della configurazione simulata.
* **SetDesiredConfigurationAndQuery.py**, un'app back-end Python che imposta la configurazione desiderata in un dispositivo ed esegue query sul processo di aggiornamento della configurazione.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] contiene informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Se si è seguita l'esercitazione [Introduzione ai dispositivi gemelli][lnk-twin-tutorial] sono già disponibili un hub IoT e un'identità del dispositivo denominata **myDeviceId**. È quindi possibile saltare la sezione [Creare l'app per dispositivo simulato][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Creare l'app per dispositivo simulata
In questa sezione si crea un'app console Python che si connette all'hub come **myDeviceId**, attende un aggiornamento della configurazione desiderata e quindi segnala gli aggiornamenti nel processo di aggiornamento della configurazione simulata.

1. Al prompt dei comandi, installare **Azure IoT SDK per dispositivi per Python** usando il seguente comando:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando un editor di testo, creare un nuovo file **SimulatedDeviceConfiguration.py**.

1. Aggiungere il codice seguente al file **SimulateDeviceConfiguration.py** e sostituire il segnaposto **{device connection string}** con la stringa di connessione del dispositivo copiata quando si è creata l'identità del dispositivo **myDeviceId**:

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    L'oggetto **CLIENT** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. In un ulteriore codice, collegare un gestore per l'aggiornamento alle proprietà desiderate, quindi aggiungere un altro gestore per verificare che si tratti effettivamente di una richiesta di modifica della configurazione confrontando i configId. Il gestore aggiunto richiama quindi un metodo che inizia la modifica della configurazione. Per semplicità, il codice precedente usa un valore predefinito hardcoded per la configurazione iniziale. Una vera app probabilmente caricherebbe tale configurazione da una risorsa di archiviazione locale.
   
   > [!IMPORTANT]
   > Gli eventi di modifica delle proprietà desiderate vengono generati sempre una volta al momento della connessione del dispositivo. Controllare che esista una modifica effettiva nelle proprietà desiderate prima di eseguire eventuali azioni.
   > 

1. Aggiungere il codice seguente alla fine del file **SimulateDeviceConfiguration.py**:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    Il metodo **device_twin_callback** aggiorna le proprietà segnalate nell'oggetto dispositivo gemello locale con la richiesta di aggiornamento della configurazione e imposta il _configId_. Dopo aver aggiornato correttamente il dispositivo gemello, viene visualizzato un messaggio di aggiornamento. Per risparmiare larghezza di banda, le proprietà segnalate vengono aggiornate specificando solo le proprietà da modificare (denominate **TWIN_PAYLOAD** nel codice precedente), invece di sostituire l'intero documento.
   
   > [!NOTE]
   > Questa esercitazione non simula alcun comportamento per gli aggiornamenti della configurazione simultanei. Alcuni processi di aggiornamento della configurazione potrebbero consentire modifiche della configurazione di destinazione mentre l'aggiornamento è in esecuzione, altre potrebbero doverle accodare e altri ancora rifiutarle con una condizione di errore. È importante tenere in considerazione il comportamento desiderato per il processo di configurazione specifico e aggiungere la logica appropriata prima di iniziare la modifica della configurazione.
   > 

1. Aggiungere il codice seguente alla fine del file **SimulateDeviceConfiguration.py**: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

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

1. Eseguire l'app per dispositivo:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Dovrebbe essere visualizzato il messaggio `Device twins updated.`. Mantenere l'app in esecuzione.


## <a name="create-the-service-app"></a>Creare l'app di servizio
In questa sezione si creerà un'app console Python che aggiorna le *proprietà desiderate* nel dispositivo gemello associato a **myDeviceId** con un nuovo oggetto di configurazione di telemetria. Viene quindi effettuata una query dei dispositivi gemelli archiviati nell'hub IoT e viene visualizzata la differenza tra la configurazione desiderata e quella segnalata del dispositivo.

1. Al prompt dei comandi, installare **Azure IoT SDK per servizi per Python** usando il seguente comando:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Usando un editor di testo, creare un nuovo file **SetDesiredAndQuery.py**.

1. Aggiungere il codice seguente al file **SetDesiredAndQuery.py** e sostituire il segnaposto **{IoTHubConnectionString}** con la stringa di connessione dell'hub IoT copiata quando è stato creato l'hub e il segnaposto **{deviceId}** con il nome del dispositivo:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Aggiungere il codice seguente alla fine del file **SetDesiredAndQuery.py**:

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. Con **SimulateDeviceConfiguration.py** in esecuzione, eseguire l'applicazione in un nuovo prompt dei comandi con:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Si potrà osservare l'ID della configurazione segnalata passare da **1** a **2** con la nuova frequenza di invio attiva di cinque minuti e non più di 24 ore.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata impostata una configurazione desiderata come *proprietà desiderate* da un'app back-end ed è stata scritta un'app per dispositivo simulato per rilevare tale modifica e simulare un processo di aggiornamento che segnala lo stato come *proprietà segnalate* al dispositivo gemello.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-iothub-getstarted].
* Per pianificare o eseguire operazioni su grandi set di dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-schedule-jobs].
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare i metodi diretti][lnk-methods-tutorial].

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
