---
title: Controllare un dispositivo dalla guida introduttiva di Hub IoT di Azure (Python) | Microsoft Docs
description: In questa guida introduttiva vengono eseguite due applicazioni Python di esempio. Una è un'applicazione back-end che può controllare in remoto i dispositivi connessi all'hub. L'altra applicazione simula un dispositivo connesso all'hub che può essere controllato in remoto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: b36e5d88c67a4aabf530aa8d945c17870e9c126b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892652"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Guida introduttiva: Controllare un dispositivo connesso a un hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Hub IoT è un servizio di Azure che consente di gestire i dispositivi IoT dal cloud e di acquisire volumi elevati di dati di telemetria dai dispositivi nel cloud per l'archiviazione o l'elaborazione. In questa guida introduttiva viene usato un *metodo diretto* per controllare un dispositivo simulato connesso all'hub IoT. È possibile usare metodi diretti per modificare in remoto il comportamento di un dispositivo connesso all'hub IoT.

La guida introduttiva usa due applicazioni Python già scritte.

* Un'applicazione del dispositivo simulato che risponde ai metodi diretti chiamati da un'applicazione back-end. Per ricevere le chiamate dei metodi diretti, l'applicazione si connette a un endpoint specifico del dispositivo nell'hub IoT.

* Un'applicazione back-end che chiama i metodi diretti sul dispositivo simulato. Per chiamare un metodo diretto su un dispositivo l'applicazione si connette a un endpoint sul lato servizio dell'hub IoT.

> [!IMPORTANT]
> In questo articolo l'applicazione back-end usa il client del servizio Python V1 e l'applicazione del dispositivo usa il client del dispositivo Python V2. Il client del servizio V1 si trova nel [ramo v1-deprecated](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated) del repository GitHub di Azure IoT Python SDK. Il pacchetto pip per il client del servizio V1, ovvero *Azure-iothub-Service-client*, prevede requisiti specifici rigorosi per la piattaforma, inclusa la versione di Python installata nel computer di sviluppo. Questi requisiti sono indicati nella sezione **Prerequisiti**.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se non è già stato fatto, scaricare il progetto Python di esempio da https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip ed estrarre l'archivio ZIP.

**Per Windows** sono necessari i prerequisiti seguenti per installare il pacchetto pip del client del servizio dell'hub IoT V1:

* Assicurarsi che sia installata la [versione **3.6.x**](https://www.python.org/downloads/) di Python.

* Assicurarsi che sia installato [Microsoft Visual C++ Redistributable per Visual Studio](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

**Per le piattaforme non Windows**, vedere la [tabella di distribuzione dei pacchetti pip di Python](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md#python-pip-package-distribution-table) nella documentazione dell'SDK V1. Assicurarsi che la versione di Python 3.x specificata per la piattaforma corrente e gli eventuali requisiti associati siano installati nel computer di sviluppo. Se si installa Python 3.x invece della versione 2.7, verranno abilitate le operazioni nel client per dispositivi V2, usate anche in questa guida di avvio rapido.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md), è possibile ignorare questo passaggio.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md), è possibile ignorare questo passaggio.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    **MyPythonDevice**: nome del dispositivo da registrare. È consigliabile usare **MyPythonDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, è necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

3. È necessaria anche una _stringa di connessione del servizio_ per consentire all'applicazione back-end di connettersi all'hub IoT dell'utente e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Annotare la stringa di connessione del servizio, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva. Questa stringa di connessione del servizio è diversa dalla stringa di connessione del dispositivo annotata nel passaggio precedente.

## <a name="listen-for-direct-method-calls"></a>Ascoltare le chiamate dei metodi diretti

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT, invia dati di telemetria simulati e ascolta le chiamate dei metodi diretti dall'hub. In questa guida introduttiva la chiamata dei metodi diretti dall'hub indica al dispositivo di modificare l'intervallo di invio dei dati di telemetria. Il dispositivo simulato invia un acknowledgement all'hub dopo l'esecuzione del metodo diretto.

1. In una finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Eseguire il dispositivo simulato](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chiamare il metodo diretto

L'applicazione back-end si connette a un endpoint sul lato servizio nell'IoT Hub dell'utente. L'applicazione effettua chiamate dei metodi diretti a un dispositivo tramite l'hub IoT e rimane in ascolto degli acknowledgement. Un'applicazione back-end dell'hub IoT in genere viene eseguita nel cloud.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\back-end-application**.

1. Aprire il file **BackEndApplication.py** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `CONNECTION_STRING` con la stringa di connessione del servizio annotata in precedenza. Salvare quindi le modifiche nel file **BackEndApplication.py**.

1. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione del dispositivo simulato:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    La schermata seguente mostra l'output visualizzato quando l'applicazione effettua una chiamata del metodo diretto al dispositivo e riceve un acknowledgement:

    ![Eseguire l'applicazione back-end](./media/quickstart-control-device-python/BackEndApplication.png)

    Dopo l'esecuzione dell'applicazione back-end, viene visualizzato un messaggio nella finestra della console che esegue il dispositivo simulato e cambia la frequenza di invio dei messaggi:

    ![Modifica nel client simulato](./media/quickstart-control-device-python/SimulatedDevice-2.png)

    > [!NOTE]
    > Se viene visualizzato un errore durante l'importazione di *iothub_service_client*, assicurarsi di aver installato la versione corretta di Python e gli eventuali altri artefatti associati specificati per la piattaforma in [Prerequisiti](#prerequisites). Se l'errore persiste anche dopo la verifica dei prerequisiti, può essere necessario ricreare il client del servizio per la piattaforma. Per informazioni su come creare l'SDK per la piattaforma, vedere le [istruzioni per la configurazione di devbox](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) nella documentazione dell'SDK V1.
    >

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato chiamato un metodo diretto su un dispositivo da un'applicazione back-end ed è stata inviata una risposta alla chiamata del metodo diretto in un'applicazione del dispositivo simulato.

Per informazioni su come indirizzare messaggi da dispositivo a cloud a diverse destinazioni nel cloud, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: indirizzare i dati di telemetria a endpoint diversi per l'elaborazione](tutorial-routing.md)