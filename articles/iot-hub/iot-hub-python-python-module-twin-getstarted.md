---
title: Identità del modulo Hub Azure Internet e modulo gemello (Python)
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 6ed0290abe91453058589b032fb5ed2dd71fe98a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87872566"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Introduzione all'identità del modulo dell'hub Internet e al modulo gemello (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Le identità dei moduli e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità dei dispositivi dell'hub Azure e ai dispositivi gemelli, ma offrono una maggiore granularità. Mentre le identità dei dispositivi dell'hub Azure Internet e i dispositivi gemelli consentono a un'applicazione back-end di configurare un dispositivo e fornire visibilità sulle condizioni del dispositivo, le identità dei moduli e i moduli gemelli forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi compatibili con più componenti, ad esempio dispositivi basati sul sistema operativo o dispositivi firmware, consentono la configurazione isolata e le condizioni per ogni componente.
>

Al termine di questa esercitazione si avranno tre app Python:

* **CreateModule**, che crea un'identità del dispositivo, un'identità del modulo e le chiavi di sicurezza associate per connettere i client del dispositivo e del modulo.

* **UpdateModuleTwinDesiredProperties**, che invia le proprietà desiderate del modulo gemello aggiornato all'hub Internet.

* **ReceiveModuleTwinDesiredPropertiesPatch**, che riceve la patch del modulo gemello per le proprietà desiderate nel dispositivo.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

In questo articolo viene creato un servizio back-end che aggiunge un dispositivo nel registro delle identità e quindi aggiunge un modulo al dispositivo. Questo servizio richiede l'autorizzazione di **scrittura del registro di sistema** , che include anche la **lettura del registro**di sistema. Si crea anche un servizio che aggiunge le proprietà desiderate al modulo gemello per il modulo appena creato. Questo servizio richiede l'autorizzazione Connect per il **servizio** . Sebbene esistano criteri di accesso condiviso predefiniti che concedono singolarmente queste autorizzazioni, in questa sezione viene creato un criterio di accesso condiviso personalizzato che contiene entrambe le autorizzazioni.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Creare un'identità del dispositivo e un'identità del modulo nell'hub IoT

In questa sezione si crea un'app di servizio Python che crea un'identità del dispositivo e un'identità del modulo nel registro delle identità nell'hub Internet. Un dispositivo o un modulo non è in grado di connettersi all'hub delle cose a meno che non includa una voce nel registro delle identità. Per altre informazioni, vedere [comprendere il registro delle identità nell'hub](iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-hub**:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **msrest** . Questo pacchetto è necessario per intercettare le eccezioni **HTTPOperationError** .

    ```cmd/sh
    pip install msrest
    ```

1. Usando un editor di testo, creare un file denominato **CreateModule.py** nella directory di lavoro.

1. Aggiungere il codice seguente al file Python. Sostituire *YourIoTHubConnectionString* con la stringa di connessione copiata in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

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

1. Al prompt dei comandi eseguire il comando seguente:

    ```cmd/sh
    python CreateModule.py
    ```

Questa app crea un'identità del dispositivo con ID **myFirstDevice** e un'identità del modulo con ID **myFirstModule** per il dispositivo **myFirstDevice**. Se il dispositivo o l'ID del modulo esiste già nel registro delle identità, il codice recupera semplicemente le informazioni sul dispositivo o sul modulo esistente. L'app Visualizza l'ID e la chiave primaria per ogni identità.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere [comprendere il registro delle identità nell'hub](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aggiornare il modulo gemello con Python Service SDK

In questa sezione si crea un'app di servizio Python che aggiorna le proprietà desiderate del modulo gemello.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **Azure-** Internet. È possibile ignorare questo passaggio se è stato installato il pacchetto **Azure-Azure-hub-hub** nella sezione precedente.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Usando un editor di testo, creare un file denominato **UpdateModuleTwinDesiredProperties.py** nella directory di lavoro.

1. Aggiungere il codice seguente al file Python. Sostituire *YourIoTHubConnectionString* con la stringa di connessione copiata in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

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

In questa sezione si crea un'app Python per ottenere l'aggiornamento delle proprietà desiderate del modulo gemello nel dispositivo.

1. Ottenere la stringa di connessione del modulo. In [portale di Azure](https://portal.azure.com/)passare all'hub Internet delle cose e selezionare **dispositivi** Internet nel riquadro sinistro. Selezionare **myFirstDevice** nell'elenco dei dispositivi e aprirlo. In **identità modulo**selezionare **myFirstModule**. Copiare la stringa di connessione del modulo. È necessario in un passaggio successivo.

   ![Dettagli del modulo nel portale di Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-device**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Usando un editor di testo, creare un file denominato **ReceiveModuleTwinDesiredPropertiesPatch.py** nella directory di lavoro.

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

In questa sezione si esegue l'app per dispositivi **ReceiveModuleTwinDesiredPropertiesPatch** , quindi si esegue l'app del servizio **UpdateModuleTwinDesiredProperties** per aggiornare le proprietà desiderate del modulo.

1. Aprire un prompt dei comandi ed eseguire l'app per dispositivo:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Output iniziale dell'app dispositivo](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Aprire un prompt dei comandi separato ed eseguire l'app di servizio:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Si noti che la proprietà desiderata **TelemetryInterval** viene visualizzata nel modulo gemello aggiornato nell'output dell'app di servizio:

   ![Output dell'app di servizio](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    La stessa proprietà viene visualizzata nella patch delle proprietà desiderata ricevuta nell'output dell'app per dispositivi:

   ![L'output dell'app dispositivo Mostra la patch delle proprietà desiderate](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
