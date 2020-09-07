---
title: Connettere il codice C del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) in Linux o Windows per la connessione a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 65b35c30d796f733e91c71e569634b61f9f7c52b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959290"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT (anteprima) di esempio in esecuzione in Linux o Windows all'hub IoT (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Questo argomento di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in C ed è inclusa in Azure IoT SDK per dispositivi per C. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per questo argomento di avvio rapido è possibile usare Linux o Windows. I comandi della shell in questo argomento di avvio rapido seguono la convenzione Linux per i separatori di percorso '`/`'. Se si usa Windows, assicurarsi di sostituire questi separatori con '`\`'.

I prerequisiti variano in base al sistema operativo:

### <a name="linux"></a>Linux

Questa guida di avvio rapido presuppone l'uso di Ubuntu Linux. I passaggi in questo argomento di avvio rapido sono stati testati con Ubuntu 18.04.

Per completare questo argomento di avvio rapido, installare il software seguente nell'ambiente Linux locale:

Installare **GCC**, **Git**, **cmake** e tutte le dipendenze necessarie usando il comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verificare che la versione di `cmake` sia superiore alla **2.8.12** e che la versione di **GCC** sia superiore alla **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Per completare questo argomento di avvio rapido in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questo argomento di avvio rapido, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire questo comando per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Scaricare il codice

In questo argomento di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare l'SDK dell'hub IoT di Azure per dispositivi per C.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [SDK e librerie di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) in tale percorso:

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

Il completamento di questa operazione richiederà alcuni minuti.

## <a name="build-the-code"></a>Compilare il codice

Usare l'SDK per dispositivi per compilare il codice di esempio incluso:

1. Creare una sottodirectory _cmake_ nella cartella radice dell'SDK per dispositivi e passare a tale cartella:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per compilare l'SDK e gli esempi:

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> In Windows è possibile aprire la soluzione generata dal comando `cmake` in Visual Studio 2019. Aprire il file di progetto *azure_iot_sdks.sln* nella directory _cmake_ e impostare il progetto **pnp_simple_thermostat** come progetto di avvio nella soluzione. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

Per eseguire l'applicazione di esempio nell'SDK che simula un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT:

Creare due variabili di ambiente per configurare l'esempio in modo da usare una stringa di connessione per la connessione all'hub IoT:

- **IOTHUB_DEVICE_SECURITY_TYPE** con il valore `"connectionString"`
- **IOTHUB_DEVICE_CONNECTION_STRING** per archiviare la stringa di connessione del dispositivo annotata in precedenza.

Dalla cartella _cmake_ passare alla cartella che contiene il file eseguibile ed eseguirlo:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> Per tracciare l'esecuzione del codice in Visual Studio in Windows, aggiungere un punto di interruzione alla funzione `main` nel file _pnp_simple_thermostat.c_.

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](concepts-components.md) di Plug and Play IoT. Il [file di modello DTDL per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Il codice del dispositivo usa la funzione standard per la connessione all'hub IoT:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Il dispositivo invia l'ID del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Il codice che aggiorna le proprietà, gestisce i comandi e invia i dati di telemetria è identico al codice per un dispositivo che non usa le convenzioni di Plug and Play IoT.

Il codice usa la libreria Parson per analizzare oggetti JSON nei payload inviati dall'hub IoT:

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
