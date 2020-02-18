---
title: Connettere il codice del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT | Microsoft Docs
description: Con C# (.NET) è possibile compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) per la connessione a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121024"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Avvio rapido: Connettere un'applicazione dispositivo Plug and Play IoT (anteprima) di esempio all'hub IoT (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Questa guida di avvio rapido illustra come creare un'applicazione dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C# (con .NET) ed è inclusa nella raccolta degli esempi di Azure IoT per C# (.NET). Uno sviluppatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

Per completare questa guida di avvio rapido, è necessario installare .NET Core 3.0 nel computer di sviluppo. È possibile scaricare questa versione di .NET Core SDK per più piattaforme dalla pagina [Download di .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Per verificare la versione di .NET presente nel computer di sviluppo, eseguire il comando seguente in una finestra del terminale locale: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Installare Azure IoT Explorer

Scaricare e installare la versione più recente di **Azure IoT Explorer** dalla pagina [repository](https://github.com/Azure/azure-iot-explorer/releases) dello strumento, selezionando il file con estensione MSI in "Assets" per l'aggiornamento più recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prenderne nota per usarla in un secondo momento:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

In questa guida di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare gli esempi di Azure IoT per C# (.NET).

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Azure IoT per C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) in questo percorso:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Il completamento dell'operazione può richiedere alcuni minuti.

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

Usare il codice di esempio clonato per creare un'applicazione che simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. In una finestra del terminale locale passare alla cartella del repository clonato e quindi alla cartella **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**. 

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Eseguire un'applicazione di esempio per simulare un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT. Nella stessa finestra del terminale per compilare i pacchetti necessari ed eseguire l'applicazione di esempio, usare il comando seguente:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Vengono visualizzati messaggi per informare che il dispositivo è stato registrato correttamente ed è in attesa di aggiornamenti dal cloud. Questi messaggi indicano che il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà ed ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Per assicurarsi che lo strumento possa leggere le definizioni del modello di interfaccia dal dispositivo, selezionare **Settings** (Impostazioni). È possibile che **On the connected device** (Nel dispositivo connesso) sia già presente nelle configurazioni Plug and Play nel menu Settings (Impostazioni); in caso contrario, selezionare **+ Add module definition source** (+ Aggiungi origine definizione modulo) e quindi **On the connected device** (Nel dispositivo connesso) per aggiungerlo.

1. Tornare alla pagina di panoramica **Devices** (Dispositivi) per trovare l'identità del dispositivo creata in precedenza. Con l'applicazione del dispositivo ancora in esecuzione nel prompt dei comandi, verificare che lo **stato di connessione** del dispositivo in Azure IoT Explorer risulti _connesso_ (in caso contrario, fare clic su **Aggiorna** fino a quando non lo è). Selezionare il dispositivo per visualizzare altri dettagli.

1. Espandere l'interfaccia con ID **urn:csharp_sdk_sample:EnvironmentalSensor:1** per visualizzare le primitive dell'interfaccia e Plug and Play IoT, ovvero le proprietà, i comandi e i dati di telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi e interagire con un dispositivo Plug and Play IoT (anteprima)](howto-develop-solution.md)
