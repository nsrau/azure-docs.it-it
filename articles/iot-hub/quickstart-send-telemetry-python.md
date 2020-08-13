---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (Python) | Microsoft Docs
description: In questa guida introduttiva si esegue un'applicazione Python di esempio per inviare dati di telemetria simulati a un hub IoT e usare un'utilità per leggere i dati di telemetria dall'hub IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
ms.date: 06/16/2020
ms.openlocfilehash: 3df26f78e66aa1806fd13fd1a46444bb5dc79742
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876224"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Avvio rapido: Inviare dati di telemetria da un dispositivo a un hub IoT e leggere i dati con un'applicazione di back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

In questa guida di avvio rapido si inviano dati di telemetria da un'applicazione del dispositivo simulato, tramite l'hub IoT di Azure, a un'applicazione back-end per l'elaborazione. L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. Questo argomento di avvio rapido usa due applicazioni Python già scritte: una per l'invio dei dati di telemetria e l'altra per la relativa lettura dall'hub. Prima di eseguire queste due applicazioni, creare un hub IoT e registrare un dispositivo con l'hub.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7+](https://www.python.org/downloads/). Per informazioni sulle altre versioni di Python supportate, vedere [Funzionalità per i dispositivi IoT di Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Un progetto Python di esempio](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Porta 8883 aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Aggiungere l'estensione Azure IoT

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici dell'hub IoT, di IoT Edge e del servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    **MyPythonDevice**: nome del dispositivo da registrare. È consigliabile usare **MyPythonDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo registrato:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida di avvio rapido.

1. È necessario anche l'_endpoint compatibile con gli Hub eventi di Azure_, il _percorso compatibile con gli Hub eventi di Azure_, e la _chiave primaria di servizio_ dall'hub IoT dell'utente per consentire all'applicazione back-end di connettersi all'hub di IoT e recuperare i messaggi. I comandi seguenti recuperano questi valori per l'hub IoT:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Prendere nota di questi tre valori, che verranno usati più avanti nella guida di avvio rapido.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. In una finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

1. Aprire il file **SimulatedDevice.py** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `CONNECTION_STRING` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche nel file **SimulatedDevice.py**.

1. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione del dispositivo simulato:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'applicazione back-end si connette all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'applicazione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

> [!NOTE]
> La procedura seguente usa l'esempio sincrono **read_device_to_cloud_messages_sync.py**. È possibile eseguire la stessa procedura con l'esempio asincrono **read_device_to_cloud_messages_async.py**.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\read-d2c-messages**.

2. Aprire il file **read_device_to_cloud_messages_sync.py** in un editor di testo a scelta. Aggiornare le variabili seguenti e salvare le modifiche nel file.

    | Variabile | valore |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Sostituire il valore della variabile con l'endpoint compatibile con hub eventi annotato in precedenza. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Sostituire il valore della variabile con il percorso compatibile con hub eventi annotato in precedenza. |
    | `IOTHUB_SAS_KEY`                | Sostituire il valore della variabile con la chiave primaria di servizio annotata in precedenza. |

3. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione back-end:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Nella finestra del terminale locale eseguire i comandi seguenti per compilare ed eseguire l'applicazione back-end:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    La schermata seguente mostra l'output mentre l'applicazione back-end riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

    ![Eseguire l'applicazione back-end](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub usando un'applicazione Python e sono stati letti i dati di telemetria dall'hub usando una semplice applicazione back-end.

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Avvio rapido: controllare un dispositivo connesso a un hub IoT](quickstart-control-device-python.md)
