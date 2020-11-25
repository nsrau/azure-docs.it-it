---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 29ab00de030aa270ed33d076333cb06cd73b71d4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511639"
---
Questo argomento di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in C ed è inclusa in Azure IoT SDK per dispositivi per C. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

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

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Scaricare il codice

In questo argomento di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare l'SDK dell'hub IoT di Azure per dispositivi per C.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [SDK e librerie di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) in tale percorso:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
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
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> In Windows è possibile aprire la soluzione generata dal comando `cmake` in Visual Studio 2019. Aprire il file di progetto *azure_iot_sdks.sln* nella directory _cmake_ e impostare il progetto **pnp_simple_thermostat** come progetto di avvio nella soluzione. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Per eseguire l'applicazione di esempio nell'SDK che simula un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT:

Dalla cartella _cmake_ passare alla cartella che contiene il file eseguibile ed eseguirlo:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Per tracciare l'esecuzione del codice in Visual Studio in Windows, aggiungere un punto di interruzione alla funzione `main` nel file _pnp_simple_thermostat.c_.

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](../articles/iot-pnp/concepts-components.md) di Plug and Play IoT. Il [file di modello DTDL per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

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
