---
title: Connettere il codice del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) per la connessione a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806529"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Guida introduttiva: Connettere un'applicazione dispositivo Plug and Play IoT (anteprima) di esempio all'hub IoT

Questa guida di avvio rapido illustra come creare un'applicazione dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C ed è inclusa in Azure IoT SDK per dispositivi per C. Uno sviluppatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario installare il software seguente nel computer locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il componente **Gestione pacchetti NuGet** e il carico di lavoro **Sviluppo per desktop con C++** quando si installa Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installare Azure IoT Explorer

Scaricare e installare lo strumento Azure IoT Explorer dalla pagina delle [versioni più recenti](https://github.com/Azure/azure-iot-explorer/releases).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparare un hub IoT

Per completare questa guida di avvio rapido, la sottoscrizione di Azure deve includere anche un hub IoT di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Durante la fase di anteprima pubblica, le funzionalità Plug and Play IoT sono disponibili solo negli hub IoT creati nelle aree **Stati Uniti centrali**, **Europa settentrionale** e **Giappone orientale**.

Aggiungere l'estensione IoT di Microsoft Azure per l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Eseguire il comando seguente per creare l'identità del dispositivo nell'hub IoT. Sostituire i segnaposto **YourIoTHubName** e **YourDevice** con i nomi effettivi. Se non è disponibile alcun hub IoT, seguire [queste istruzioni per crearne uno](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Eseguire i comandi seguenti per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Eseguire i comandi seguenti per ottenere la _stringa di connessione dell'hub IoT_ per l'hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

### <a name="get-azure-iot-device-sdk-for-c"></a>Ottenere Azure IoT SDK per dispositivi per C

In questa guida di avvio rapido verrà preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT SDK per dispositivi per C.

Aprire un prompt dei comandi. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Il completamento di questa operazione richiederà alcuni minuti.

## <a name="build-the-code"></a>Compilare il codice

L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. Creare una sottodirectory `cmake` nella directory radice dell'SDK del dispositivo e passare a tale cartella:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per compilare l'SDK del dispositivo e lo stub del codice generato:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Se cmake non trova il compilatore C++, vengono visualizzati errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando al [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

Eseguire l'applicazione passando come parametro la stringa di connessione del dispositivo dell'hub IoT.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

L'applicazione del dispositivo inizia a inviare dati all'hub IoT.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

1. Aprire Azure IoT Explorer. Viene visualizzata la pagina **App configurations** (Configurazioni app).

1. Immettere la stringa di connessione dell'hub IoT e fare clic su **Connetti**.

1. Dopo la connessione viene visualizzata la pagina della panoramica del dispositivo.

1. Per aggiungere il repository aziendale, selezionare **Impostazioni**, quindi **+ Nuovo** e infine **On the connected device** (Nel dispositivo connesso).

1. Nella pagina della panoramica del dispositivo individuare l'identità del dispositivo creata in precedenza e selezionarla per visualizzare altri dettagli.

1. Espandere l'interfaccia con ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** per visualizzare le primitive Plug and Play IoT, ovvero le proprietà, i comandi e i dati di telemetria.

1. Selezionare la pagina **Telemetria** per visualizzare i dati di telemetria inviati dal dispositivo.

1. Selezionare la pagina **Properties(non-writable)** (Proprietà - non scrivibili) per visualizzare le proprietà non scrivibili segnalate dal dispositivo.

1. Selezionare la pagina **Properties(writable)** (Proprietà - scrivibili) per visualizzare le proprietà scrivibili che è possibile aggiornare.

1. Espandere la proprietà **name**, aggiornarla con un nuovo nome e selezionare **update writable property** (Aggiorna proprietà scrivibile). 

1. Per visualizzare il nuovo nome nella colonna **Reported Property** (Proprietà segnalata), fare clic sul pulsante **Aggiorna** nella parte superiore della pagina.

1. Selezionare la pagina **Comando** per visualizzare tutti i comandi supportati dal dispositivo.

1. Espandere il comando **blink** e impostare un nuovo intervallo di tempo per l'intermittenza. Selezionare **Invia comando** per chiamare il comando nel dispositivo.

1. Passare al dispositivo simulato per verificare che il comando venga eseguito come previsto.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi e interagire con un dispositivo Plug and Play IoT (anteprima)](howto-develop-solution.md)
