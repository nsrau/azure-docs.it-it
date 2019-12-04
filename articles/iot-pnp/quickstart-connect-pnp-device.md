---
title: Connettere il codice del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT (Windows) | Microsoft Docs
description: Compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) in Windows per la connessione a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152078"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Guida introduttiva: Connettere un'applicazione per dispositivi Plug and Play IoT (anteprima) di esempio in esecuzione in Windows all'hub IoT (C Windows)

Questa guida di avvio rapido illustra come creare un'applicazione dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C ed è inclusa nell'SDK dell'hub IoT di Azure per dispositivi per C. Uno sviluppatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario installare il software seguente nel computer locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il componente **Gestione pacchetti NuGet** e il carico di lavoro **Sviluppo per desktop con C++** quando si installa Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installare Azure IoT Explorer

Scaricare e installare la versione più recente di **Azure IoT Explorer** dalla pagina [repository](https://github.com/Azure/azure-iot-explorer/releases) dello strumento, selezionando il file con estensione MSI in "Assets" per l'aggiornamento più recente.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

In questo argomento di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare l'SDK dell'hub IoT di Azure per dispositivi per C.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [SDK e librerie di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) in tale percorso:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Il completamento di questa operazione richiederà alcuni minuti.

## <a name="build-the-code"></a>Compilare il codice

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

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

Eseguire un'applicazione di esempio nell'SDK per simulare un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT. Per eseguire l'applicazione di esempio, usare questi comandi e passare la _stringa di connessione del dispositivo_ come parametro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

1. Aprire Azure IoT Explorer. Viene visualizzata la pagina **App configurations** (Configurazioni app).

1. Immettere la _stringa di connessione dell'hub IoT_ e selezionare **Connect** (Connetti).

1. Dopo la connessione viene visualizzata la pagina di panoramica **Devices** (Dispositivi).

1. Per assicurarsi che lo strumento possa leggere le definizioni del modello di interfaccia dal dispositivo, selezionare **Settings** (Impostazioni). È possibile che **On the connected device** (Nel dispositivo connesso) sia già presente nelle configurazioni Plug and Play nel menu Settings (Impostazioni); in caso contrario, selezionare **+ Add module definition source** (+ Aggiungi origine definizione modulo) e quindi **On the connected device** (Nel dispositivo connesso) per aggiungerlo.

1. Tornare alla pagina di panoramica **Devices** (Dispositivi) per trovare l'identità del dispositivo creata in precedenza. Con l'applicazione del dispositivo ancora in esecuzione nel prompt dei comandi, verificare che lo **stato di connessione** del dispositivo in Azure IoT Explorer risulti _connesso_ (in caso contrario, fare clic su **Aggiorna** fino a quando non lo è). Selezionare il dispositivo per visualizzare altri dettagli.

1. Espandere l'interfaccia con ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** per visualizzare le primitive dell'interfaccia e Plug and Play IoT, ovvero le proprietà, i comandi e i dati di telemetria.

1. Selezionare la pagina **Telemetria** e scegliere _Start_ per visualizzare i dati di telemetria inviati dal dispositivo.

1. Selezionare la pagina **Properties (non-writable)** (Proprietà - non scrivibili) per visualizzare le proprietà non scrivibili segnalate dal dispositivo.

1. Selezionare la pagina **Properties (writable)** (Proprietà - scrivibili) per visualizzare le proprietà scrivibili che è possibile aggiornare.

1. Espandere la proprietà **name**, aggiornarla con un nuovo nome e selezionare **Update writable property** (Aggiorna proprietà scrivibile). 

1. Per visualizzare il nuovo nome nella colonna **Reported Property** (Proprietà segnalata), selezionare il pulsante **Refresh** (Aggiorna) nella parte superiore della pagina.

1. Selezionare la pagina **Commands** (Comandi) per visualizzare tutti i comandi supportati dal dispositivo.

1. Espandere il comando **blink** e impostare un nuovo intervallo di tempo per l'intermittenza. Selezionare **Invia comando** per chiamare il comando nel dispositivo.

1. Passare al prompt dei comandi del dispositivo simulato e leggere i messaggi di conferma visualizzati per verificare che i comandi siano stati eseguiti come previsto.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi e interagire con un dispositivo Plug and Play IoT (anteprima)](howto-develop-solution.md)
