---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (Python) | Microsoft Docs
description: In questa guida introduttiva si esegue un'applicazione Python di esempio per inviare dati di telemetria simulati a un hub IoT e usare un'utilità per leggere i dati di telemetria dall'hub IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/07/2018
ms.author: dobett
ms.openlocfilehash: 2d851bc8d5af7f824512cc9f14e6b1120026dd07
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785156"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT e leggere i dati di telemetria dall'hub con un'applicazione di back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questa Guida rapida, si inviano dati di telemetria da un'applicazione del dispositivo simulato, tramite l'Hub IoT, a un'applicazione di back-end per l'elaborazione.

La guida introduttiva usa un'applicazione Python già pronta per inviare i dati di telemetria e un'utilità da riga di comando per leggere i dati di telemetria dall'hub. Prima di eseguire queste due applicazioni, creare un hub IoT e registrare un dispositivo con l'hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in Python. È necessario un Python 2.7.x o 3,5.x nel computer di sviluppo.

È possibile scaricare Python per più piattaforme da [Python.org](https://www.python.org/downloads/). Il programma di installazione di Python scelto deve essere basato sull'architettura del sistema in uso. Se l'architettura della CPU di sistema è a 32 bit, scaricare la versione x86, ovvero il programma di installazione predefinito in Python.org, mentre per l'architettura a 64 bit è necessario scaricare il programma di installazione x86-64.

È possibile verificare la versione corrente di Python installata nel computer di sviluppo tramite uno dei comandi seguenti:

```python
python --version
```

```python
python3 --version
```

Scaricare il progetto di esempio di Python da https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip ed estrarre l'archivio ZIP.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa l'interfaccia della riga di comando di Azure per registrare un dispositivo simulato.

1. Aggiungere l'estensione della riga di comando dell'hub IoT CLI e creare l'identità del dispositivo. Sostituire `{YourIoTHubName}` con il nome scelto per l'hub IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Se si sceglie un nome diverso per il dispositivo, aggiornare il nome del dispositivo nell'applicazione di esempio prima di eseguirla.

1. Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto: `Hostname=...=`. Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. In una finestra del terminale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

1. Aprire il file **SimulatedDevice.py** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `CONNECTION_STRING` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche nel file **SimulatedDevice.py**.

1. Nella finestra del terminale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione del dispositivo simulato:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Nella finestra del terminale eseguire i comandi seguenti per eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'estensione dell'interfaccia della riga di comando dell'hub IoT può connettersi all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'estensione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

Eseguire i comandi dell'interfaccia della riga di comando di Azure seguenti, sostituendo `{YourIoTHubName}` con il nome dell'hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyPythonDevice --hub-name {YourIoTHubName}
```

La schermata seguente mostra l'output mentre l'estensione riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

![Eseguire l'applicazione back-end](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida, è stata configurata un hub IoT, è stato registrato un dispositivo, è stata inviata una telemetria simulata all'hub usando un'applicazione Python e sono stati letti i dati di telemetria dall'hub usando una semplice applicazione di back-end.

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: Controllare un dispositivo connesso a un hub IoT](quickstart-control-device-python.md)