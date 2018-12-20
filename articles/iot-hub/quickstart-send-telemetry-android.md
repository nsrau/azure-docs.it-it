---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (Android) | Microsoft Docs
description: In questa guida introduttiva si esegue un'applicazione Android di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: d90595d3d557293bfbc6b06aca638eb1b7c08d30
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164314"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Guida introduttiva: inviare dati di telemetria IoT da un dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questa guida introduttiva, si invieranno dati di telemetria a un hub IoT da un'applicazione Android in esecuzione su un dispositivo simulato o fisico.

La guida introduttiva usa un'applicazione Android già scritta per inviare i dati di telemetria, che verranno letti dall'hub IoT con Azure Cloud Shell. Prima di eseguire questa applicazione, creare un hub IoT e registrare un dispositivo con l'hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Android Studio da https://developer.android.com/studio/. Per altre informazioni relative all'installazione di Android Studio, vedere [android-installation](https://developer.android.com/studio/install). 

* L'esempio in questo articolo usa Android SDK 27. 

* L'[applicazione Android di esempio](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) eseguita in questa guida introduttiva è parte del repository azure-iot-samples-java su GitHub. Scaricare o clonare il repository [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).



## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire i comandi seguenti in Azure Cloud Shell per aggiungere l'estensione dell'interfaccia della riga di comando dell'hub IoT e per creare l'identità del dispositivo. 

   **YourIoTHubName**: Sostituire il segnaposto in basso con ilnome scelto per l'hub IoT.

   **MyAndroidDevice**: MyAndroidDevice è il nome specificato per il dispositivo registrato. Usare MyAndroidDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

    **YourIoTHubName**: Sostituire il segnaposto in basso con ilnome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva per l'invio dei dati di telemetria.

## <a name="send-telemetry"></a>Inviare dati di telemetria

1. Aprire il progetto Android di esempio di GitHub in Android Studio. Il progetto si trova nella seguente directory della copia clonata o scaricata del repository [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. In Android Studio, aprire *gradle.properties* per il progetto di esempio e sostituire il segnaposto **Device_Connection_String** con la stringa di connessione dispositivo annotata in precedenza.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. In Android Studio fare clic su **File** > **Sync Project with Gradle Files**. Verificare il completamento della compilazione.

4. Una volta completata la compilazione, fare clic su **Esegui** > **Esegui "app"**. Configurare l'app in modo da eseguirla su un dispositivo Android fisico o un emulatore Android. Per altre informazioni sull'esecuzione di un'app Android in un dispositivo fisico o un emulatore, vedere [Eseguire l'app](https://developer.android.com/training/basics/firstapp/running-app).

5. Una volta caricata l'app, fare clic su **Avvia** per avviare l'invio di dati di telemetria all'hub IoT:

    ![Applicazione](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

In questa sezione si eseguirà il monitoraggio dei messaggi inviati dal dispositivo Android usando Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

1. Tramite Azure Cloud Shell, eseguire il comando seguente per connettersi e leggere i messaggi dall'hub IoT:

   **YourIoTHubName**: Sostituire il segnaposto in basso con ilnome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    La schermata seguente mostra l'output mentre l'hub IoT riceve i dati di telemetria inviati dal dispositivo Android:

      ![Leggere i messaggi del dispositivo tramite l'interfaccia della riga di comando di Azure](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub usando un'applicazione Android e i dati di telemetria sono stati letti dall'hub usando Azure Cloud Shell.

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: controllare un dispositivo connesso a un hub IoT](quickstart-control-device-android.md)

