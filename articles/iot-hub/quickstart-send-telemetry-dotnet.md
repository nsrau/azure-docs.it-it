---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (C#) | Microsoft Docs
description: In questa guida introduttiva si eseguono due applicazioni C# di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/20/2018
ms.author: dobett
ms.openlocfilehash: 501a8f2dad0537f0288dfd6a6d7bedcac054ab86
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619302"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT e leggere i dati di telemetria dall'hub con un'applicazione di back-end (C#)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questa Guida rapida, si inviano dati di telemetria da un'applicazione del dispositivo simulato, tramite l'Hub IoT, a un'applicazione di back-end per l'elaborazione.

La guida introduttiva usa due applicazioni C# già pronte, una per inviare i dati di telemetria e una per leggere i dati di telemetria dall'hub. Prima di eseguire queste due applicazioni, creare un hub IoT e registrare un dispositivo con l'hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in C#. È necessario .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo.

È possibile scaricare .NET Core SDK per più piattaforme da [.NET](https://www.microsoft.com/net/download/all).

È possibile verificare la versione corrente di C# installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
dotnet --version
```

Scaricare il progetto C# di esempio da https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ed estrarre l'archivio ZIP.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa l'interfaccia della riga di comando di Azure per registrare un dispositivo simulato.

1. Aggiungere l'estensione della riga di comando dell'hub IoT CLI e creare l'identità del dispositivo. Sostituire `{YourIoTHubName}` con il nome scelto per l'hub IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

    Se si sceglie un nome diverso per il dispositivo, aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

2. Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto: `Hostname=...=`. Il valore verrà usato più avanti in questa guida introduttiva.

3. È necessario anche l'_endpoint compatibile con gli hub eventi_, il _percorso compatibile con gli hub eventi_, e la _chiave primaria iothubowner_ dall'hub IoT dell'utente per consentire all'applicazione back-end di connettersi all'hub di IoT e recuperare i messaggi. I comandi seguenti recuperano questi valori per l'hub IoT:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Prendere nota di questi tre valori, che saranno utili successivamente durante la guida introduttiva.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria simulati di temperatura e umidità.

1. In una finestra del terminale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device**.

2. Aprire il file **SimulatedDevice.cs** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `s_connectionString` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche nel file **SimulatedDevice.cs**.

3. Nella finestra del terminale eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

4. Nella finestra del terminale eseguire il comando seguente per compilare ed eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet run
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

L'applicazione back-end si connette all'endpoint **Eventi** sul lato servizio dell'hub IoT. L'applicazione riceve i messaggi da dispositivo a cloud inviati dal dispositivo simulato. In genere, un'applicazione di back-end di hub IoT viene eseguita nel cloud per ricevere ed elaborare i messaggi da dispositivo a cloud.

1. In un'altra finestra del terminale passare alla cartella radice del progetto C# di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\read-d2c-messages**.

2. Aprire il file **ReadDeviceToCloudMessages.cs** in un editor di testo di propria scelta. Aggiornare le variabili seguenti e salvare le modifiche nel file.

    | Variabile | Valore |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Sostituire il valore della variabile con l'endpoint compatibile di Hub eventi annotato in precedenza. |
    | `s_eventHubsCompatiblePath`     | Sostituire il valore della variabile con il percorso compatibile con Hub eventi annotato in precedenza. |
    | `s_iotHubSasKey`                | Sostituire il valore della variabile con la chiave primaria iothubowner annotata in precedenza. |

3. Nella finestra del terminale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione back-end:

    ```cmd/sh
    dotnet restore
    ```

4. Nella finestra del terminale eseguire i comandi seguenti per compilare ed eseguire l'applicazione back-end:

    ```cmd/sh
    dotnet run
    ```

    La schermata seguente mostra l'output mentre l'applicazione back-end riceve i dati di telemetria inviati dal dispositivo simulato all'hub:

    ![Eseguire l'applicazione back-end](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida, è stata configurata un hub IoT, è stato registrato un dispositivo, è stata inviata una telemetria simulata all'hub usando un'applicazione C# e sono stati letti i dati di telemetria dall'hub usando una semplice applicazione di back-end.

Per informazioni su come controllare il dispositivo simulato da un'applicazione back-end, continuare nella Guida introduttiva successiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: Controllare un dispositivo connesso a un hub IoT](quickstart-control-device-dotnet.md)