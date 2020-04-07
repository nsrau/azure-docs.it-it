---
title: Identità e modulo gemello dell'hub IoT di Azure (Python)Azure IoT Hub module identity and module twin (Python)
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756991"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Introduzione all'identità del modulo Hub IoT e al modulo gemello (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> Le identità dei moduli e i moduli gemelli sono simili alle identità dei dispositivi [dell'hub dell'IoT](iot-hub-devguide-module-twins.md) di Azure e ai dispositivi gemelli, ma offrono una granularità più fine. Mentre le identità dei dispositivi dell'hub IoT di Azure e i dispositivi gemelli consentono a un'applicazione back-end di configurare un dispositivo e di fornire visibilità sulle condizioni del dispositivo, le identità dei moduli e i moduli gemelli forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi in grado con più componenti, ad esempio dispositivi basati sul sistema operativo o dispositivi firmware, consentono la configurazione isolata e le condizioni per ogni componente.
>

Alla fine di questa esercitazione, sono disponibili tre app Python:At the end of this tutorial, you have three Python apps:

* **CreateModule**, che crea un'identità del dispositivo, un'identità del modulo e le chiavi di sicurezza associate per connettere i client del dispositivo e del modulo.

* **UpdateModuleTwinDesiredProperties**, che invia le proprietà desiderate del modulo gemello aggiornate all'hub IoT.

* **ReceiveModuleTwinDesiredPropertiesPatch**, che riceve la patch delle proprietà desiderate del modulo gemello sul dispositivo.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

In questo articolo viene creato un servizio back-end che aggiunge un dispositivo nel Registro di sistema delle identità e quindi aggiunge un modulo a tale dispositivo. Questo servizio richiede l'autorizzazione di **scrittura del Registro** di sistema (che include anche la **lettura del Registro**di sistema ). È inoltre possibile creare un servizio che aggiunge le proprietà desiderate al modulo gemello per il modulo appena creato. Questo servizio richiede l'autorizzazione di connessione del **servizio.** Sebbene esistano criteri di accesso condiviso predefiniti che concedono queste autorizzazioni singolarmente, in questa sezione viene creato un criterio di accesso condiviso personalizzato contenente entrambe queste autorizzazioni.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Creare un'identità del dispositivo e un'identità del modulo nell'hub IoT

In questa sezione viene creata un'app del servizio Python che crea un'identità del dispositivo e un'identità del modulo nel Registro di sistema delle identità nell'hub IoT.In this section, you create a Python service app that creates a device identity and a module identity in the identity registry in your IoT hub. Un dispositivo o un modulo non può connettersi all'hub IoT a meno che non disponga di una voce nel Registro di sistema delle identità. Per altre informazioni, vedere [Informazioni sul registro delle identità nell'hub IoT](iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-hub:At** your command prompt, run the following command to install the azure-iot-hub package:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **msrest.** Questo pacchetto è necessario per intercettare le eccezioni **HTTPOperationError.You** need this package to catch HTTPOperationError exceptions.

    ```cmd/sh
    pip install msrest
    ```

1. Utilizzando un editor di testo, creare un file denominato **CreateModule.py** nella directory di lavoro.

1. Aggiungere il codice seguente al file Python. Sostituire *YourIoTHubConnectionString* con la stringa di connessione copiata in Ottenere la stringa di [connessione dell'hub IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Al prompt dei comandi, eseguire il comando seguente:

    ```cmd/sh
    python CreateModule.py
    ```

Questa app crea un'identità del dispositivo con ID **myFirstDevice** e un'identità del modulo con ID **myFirstModule** per il dispositivo **myFirstDevice**. Se l'ID del dispositivo o del modulo esiste già nel registro delle identità, il codice recupera semplicemente le informazioni sul dispositivo o sul modulo esistente. L'app visualizza l'ID e la chiave primaria per ogni identità.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere [Informazioni sul registro delle identità nell'hub IoT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aggiornare il modulo gemello utilizzando python service SDK

In questa sezione viene creata un'app di servizio Python che aggiorna le proprietà desiderate del modulo gemello.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-hub.** È possibile ignorare questo passaggio se è stato installato il pacchetto **azure-iot-hub** nella sezione precedente.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Utilizzando un editor di testo, creare un file denominato **UpdateModuleTwinDesiredProperties.py** nella directory di lavoro.

1. Aggiungere il codice seguente al file Python. Sostituire *YourIoTHubConnectionString* con la stringa di connessione copiata in Ottenere la stringa di [connessione dell'hub IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Ottenere aggiornamenti sul lato dispositivo

In questa sezione viene creata un'app Python per ottenere l'aggiornamento delle proprietà desiderato del modulo gemello nel dispositivo.

1. Ottenere la stringa di connessione del modulo. Nel [portale di Azure](https://portal.azure.com/)passare all'hub IoT e selezionare Dispositivi **IoT** nel riquadro sinistro. Selezionare **myFirstDevice** dall'elenco dei dispositivi e aprirlo. In **Identità modulo**selezionare **myFirstModule**. Copiare la stringa di connessione del modulo. È necessario in un passaggio successivo.

   ![Dettagli del modulo nel portale di Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-device:At** your command prompt, run the following command to install the azure-iot-device package:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Utilizzando un editor di testo, creare un file denominato **ReceiveModuleTwinDesiredPropertiesPatch.py** nella directory di lavoro.

1. Aggiungere il codice seguente al file Python. Sostituire *YourModuleConnectionString* con la stringa di connessione del modulo copiata nel passaggio 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Eseguire le app

In questa sezione viene eseguita l'app per dispositivi **ReceiveModuleTwinDesiredPropertiesPatch** e quindi l'app di servizio **UpdateModuleTwinDesiredProperties** per aggiornare le proprietà desiderate del modulo.

1. Aprire un prompt dei comandi ed eseguire l'app per dispositivi:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Output iniziale dell'app per dispositivi](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Aprire un prompt dei comandi separato ed eseguire l'app di servizio:Open a separate command prompt and run the service app:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Si noti che la proprietà desiderata TelemetryInterval viene visualizzata nel modulo gemello aggiornato nell'output dell'app del servizio:Notice that the **TelemetryInterval** desired property appears in the updated module twin in your service app output:

   ![Output dell'app di servizio](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    La stessa proprietà viene visualizzata nella patch delle proprietà desiderata ricevuta nell'output dell'app per dispositivi:

   ![L'output dell'app per dispositivi mostra la patch delle proprietà desiderate](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)