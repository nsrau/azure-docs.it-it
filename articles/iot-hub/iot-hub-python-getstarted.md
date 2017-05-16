---
title: Introduzione all&quot;hub IoT di Azure (Python) | Microsoft Docs
description: Questo articolo illustra come inviare messaggi da un dispositivo simulato all&quot;hub IoT di Azure usando gli SDK di Azure IoT per Python.
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2017
ms.author: dkshir
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: d020fc6b51f3b20f56fa056e666ba9c7441a3065
ms.lasthandoff: 04/25/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Connettere il dispositivo simulato all'hub IoT usando Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione si avranno due app Python:

* **CreateDeviceIdentity.py**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere l'app per dispositivo simulato.
* **SimulatedDevice.py**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia periodicamente un messaggio di telemetria usando il protocollo MQTT.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] offre informazioni sui vari Azure IoT SDK che è possibile usare per compilare applicazioni da eseguire nei dispositivi e il backend della soluzione.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x][lnk-python-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python][lnk-install-pip].
* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++][lnk-visual-c-redist] per consentire l'uso di DLL native da Python.
* [Node.js 4.0 o versione successiva][lnk-node-download]. Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Questo è un requisito per installare lo [strumento IoT Hub Explorer][lnk-iot-hub-explorer].
* Un account Azure attivo. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

L'hub IoT è stato creato. Usare il nome host e la stringa di connessione dell'hub IoT nel resto dell'esercitazione.

> [!NOTE]
> È anche possibile creare con facilità un hub IoT personalizzato in una riga di comando, usando l'interfaccia della riga di comando di Azure basata su Python o Node.js. L'articolo [Creare un hub IoT usando l'interfaccia della riga di comando di Azure 2.0][lnk-azure-cli-hub] illustra la procedura rapida per eseguire questa operazione. 
> 

## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo
Questa sezione elenca i passaggi per la creazione di un'app console Python, che crea un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo può connettersi all'hub IoT solo se ha una voce nel registro delle identità. Per altre informazioni, vedere la sezione **Registro di identità** della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Quando si esegue questa app console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT.

1. Aprire un prompt dei comandi e installare **Azure IoT Hub Service SDK per Python**. Chiudere il prompt dei comandi dopo l'installazione dell'SDK.

    ```
    pip install azure-iothub-service-client
    ```

2. Creare un file di Python denominato **CreateDeviceIdentity.py**. Aprirlo in un [editor/IDE di Python di propria scelta][lnk-python-ide-list], ad esempio l'editor [IDLE][lnk-idle] predefinito.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK del servizio:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Aggiungere il codice seguente, sostituendo il segnaposto per `[IoTHub Connection String]` con la stringa di connessione dell'hub IoT creato nella sezione precedente. È possibile usare qualsiasi nome per `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
3. Aggiungere la funzione seguente per stampare alcune informazioni del dispositivo.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Aggiungere la funzione seguente per creare l'identificazione del dispositivo usando Gestore Registro di sistema. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Aggiungere infine la funzione main, come indicato di seguito, quindi salvare il file.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. Nel prompt dei comandi eseguire **CreateDeviceIdentity.py**, come indicato di seguito:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Verrà visualizzata la creazione del dispositivo simulato. Annotare il valore di **deviceId** e di **primaryKey** per il dispositivo. che saranno necessari più avanti quando si creerà un'applicazione che si connette all'hub IoT come dispositivo.

    ![La creazione del dispositivo è stata completata][1]

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia le chiavi e gli ID dispositivo da usare come credenziali di sicurezza e un flag di abilitazione/disabilitazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
Questa sezione elenca i passaggi per la creazione di un'app console di Python, che simula un dispositivo e invia messaggi da dispositivo a cloud all'hub IoT.

1. Aprire un nuovo prompt dei comandi e installare Azure IoT Hub Device SDK per Python, come indicato di seguito. Chiudere il prompt dei comandi dopo l'installazione.

    ```
    pip install azure-iothub-device-client
    ```
2. Creare un file denominato **SimulatedDevice.py**. Aprire il file in un editor/IDE di Python di propria scelta, ad esempio IDLE.

3. Aggiungere il codice seguente per importare i moduli necessari dall'SDK del dispositivo.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Aggiungere il codice seguente e sostituire il segnaposto per `[IoTHub Device Connection String]` con la stringa di connessione per il dispositivo. La stringa di connessione del dispositivo ha in genere il formato `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Usare il valore **deviceId** e il valore **primaryKey** del dispositivo creato nella sezione precedente per sostituire rispettivamente i valori `<deviceId>` e `<primaryKey>`. Sostituire `<hostName>` con il nome host dell'hub IoT, in genere come `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Aggiungere il codice seguente per definire un callback di conferma di invio. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Aggiungere il codice seguente per inizializzare il client del dispositivo.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. Aggiungere la funzione seguente al formato e inviare un messaggio dal dispositivo simulato all'hub IoT.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Aggiungere infine la funzione main. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Salvare e chiudere il file **SimulatedDevice.py**. A questo punto è possibile eseguire l'app.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Ricevere messaggi dal dispositivo simulato
Per ricevere messaggi di telemetria dal dispositivo, è necessario usare un endpoint compatibile con [Hub eventi][lnk-event-hubs-overview] esposto dall'hub IoT, che legge i messaggi dal dispositivo al cloud. Per informazioni su come elaborare i messaggi dagli hub eventi per l'endpoint compatibile con hub eventi dell'hub IoT, vedere l'esercitazione [Introduzione agli hub eventi][lnk-eventhubs-tutorial]. Gli hub eventi non supportano ancora la telemetria in Python. È quindi possibile creare un'app console di [Node.js](iot-hub-node-node-getstarted.md#D2C_node) o [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) basata sugli hub eventi per leggere i messaggi dal dispositivo al cloud dall'hub IoT. Questa esercitazione mostra come usare lo [strumento IoT Hub Explorer][lnk-iot-hub-explorer] per leggere questi messaggi del dispositivo.

1. Aprire un prompt dei comandi e installare IoT Hub Explorer. 

    ```
    npm install -g iothub-explorer
    ```

2. Eseguire il comando seguente nel prompt dei comandi per avviare il monitoraggio dei messaggi dal dispositivo al cloud dal dispositivo. Usare la stringa di connessione dell'hub IoT nel segnaposto dopo `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Aprire un nuovo prompt dei comandi e passare alla directory contenente il file **SimulatedDevice.py**.

4. Eseguire il file **SimulatedDevice.py**, che invia periodicamente dati di telemetria all'hub IoT. 
   
    ```
    python SimulatedDevice.py
    ```
5. Osservare i messaggi del dispositivo nel prompt dei comandi eseguendo lo strumento IoT Hub Explorer dalla sezione precedente. 

    ![Messaggi dal dispositivo Python al cloud][2]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Questa identità del dispositivo è stata usata per consentire all'app per dispositivo simulato di inviare all'hub IoT messaggi dispositivo a cloud. Sono stati osservati i messaggi ricevuti dall'hub IoT tramite lo strumento IoT Hub Explorer. 

Per esplorare Python SDK per ottenere informazioni approfondite sull'utilizzo dell'hub IoT di Azure, vedere [questo repository di GitHub][lnk-python-github]. Per verificare le funzionalità di messaggistica di Azure IoT Hub Service SDK per Python, è possibile scaricare ed eseguire [iothub_messaging_sample.py][lnk-messaging-sample]. Per la simulazione lato dispositivo con Azure IoT Hub Device SDK per Python, è possibile scaricare ed eseguire [iothub_client_sample.py][lnk-client-sample].

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Connessione del dispositivo][lnk-connect-device]
* [Introduzione alla gestione dei dispositivi][lnk-device-management]
* [Introduzione all'IoT SDK per gateway][lnk-gateway-SDK]

Per informazioni sull'estensione della soluzione IoT e l'elaborazione di messaggi dispositivo a cloud su vasta scala, vedere l'esercitazione [Elaborare messaggi dispositivo a cloud][lnk-process-d2c-tutorial].

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

