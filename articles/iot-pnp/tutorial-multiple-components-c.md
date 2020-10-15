---
title: Connettere il codice C di un dispositivo di esempio Plug and Play IoT all'hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice C di un dispositivo Plug and Play IoT di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e1a2a45d0f5743874ce0c0b20190d7d396094e43
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046384"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>Esercitazione: Connettere le applicazioni di un dispositivo Plug and Play IoT con più componenti in esecuzione in Linux o Windows a un hub IoT (C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C ed è inclusa in Azure IoT SDK per dispositivi per C. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

È possibile completare questa esercitazione in Linux o Windows. I comandi della shell in questa esercitazione seguono la convenzione Linux per i separatori di percorso '`/`'. Se si usa Windows, assicurarsi di sostituire questi separatori con '`\`'.

I prerequisiti variano in base al sistema operativo:

### <a name="linux"></a>Linux

Questa esercitazione presuppone l'uso di Ubuntu Linux. I passaggi in questa esercitazione sono stati testati con Ubuntu 18.04.

Per completare questa esercitazione, installare il software seguente nell'ambiente Linux locale:

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

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Scaricare il codice

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio in esecuzione in Linux o in Windows all'hub IoT (C)](quickstart-connect-device-c.md), il codice è già stato scaricato.

In questa esercitazione viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT Hub SDK per dispositivi per C.

Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub di [SDK e librerie di Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) in tale percorso:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Il completamento di questa operazione richiederà alcuni minuti.

## <a name="build-and-run-the-code"></a>Compilare ed eseguire il codice

È possibile compilare ed eseguire il codice usando Visual Studio oppure eseguendo `cmake` dalla riga di comando.

### <a name="use-visual-studio"></a>Usare Visual Studio

1. Aprire la cartella radice del repository clonato. Dopo alcuni secondi, il supporto **CMake** in Visual Studio crea tutti gli elementi necessari per l'esecuzione e il debug del progetto.
1. Quando Visual Studio è pronto, in **Esplora soluzioni** passare alla cartella *iothub_client/samples/pnp/pnp_temperature_controller/* di esempio.
1. Fare clic con il pulsante destro del mouse sul file *pnp_temperature_controller.c* e selezionare **Aggiungi configurazione di debug**. Selezionare **Predefinita**.
1. In Visual Studio viene aperto il file *launch.vs.json*. Modificare questo file come illustrato nel frammento di codice seguente per impostare le variabili di ambiente richieste. L'ID ambito e la chiave primaria di registrazione sono stati annotati quando sono state completate le [guide di avvio rapido e le esercitazioni per configurare l'ambiente per Plug and Play IoT](set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Fare clic con il pulsante destro del mouse sul file *pnp_temperature_controller.c* e selezionare **Imposta come elemento di avvio**.
1. Per tracciare l'esecuzione del codice in Visual Studio, aggiungere un punto di interruzione alla funzione `main` nel file *pnp_temperature_controller.c*.
1. È ora possibile eseguire l'esempio ed eseguirne il debug dal menu **Debug**.

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

### <a name="use-cmake-at-the-command-line"></a>Usare `cmake` dalla riga di comando

Per compilare l'esempio:

1. Creare una sottocartella _cmake_ nella cartella radice dell'SDK per dispositivi clonato e passare a tale cartella:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per generare e compilare i file di progetto per l'SDK e gli esempi:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Per eseguire l'esempio:

1. Dalla cartella _cmake_ passare alla cartella che contiene il file eseguibile ed eseguirlo:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. In questo esempio viene implementato un modello con [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

### <a name="iot-plug-and-play-helper-functions"></a>Funzioni helper di Plug and Play IoT

Per questo esempio il codice usa alcune funzioni helper della cartella */common*:

*pnp_device_client_ll* contiene il metodo di connessione per Plug and Play IoT con `model-id` incluso come parametro: `PnP_CreateDeviceClientLLHandle`.

*pnp_protocol* contiene le funzioni helper di Plug and Play IoT:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Queste funzioni helper sono sufficientemente generiche per essere usate nel progetto. In questo esempio vengono usate nei tre file che corrispondono a ogni componente del modello:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Ad esempio, nel file *pnp_deviceinfo_component* la funzione `SendReportedPropertyForDeviceInformation` usa due delle funzioni helper:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Ogni componente dell'esempio è conforme a questo modello.

### <a name="code-flow"></a>flusso del codice

La funzione `main` inizializza la connessione e invia l'ID modello:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Il codice usa `PnP_CreateDeviceClientLLHandle` per connettersi all'hub IoT, impostare `modelId` come opzione e configurare i gestori di callback del metodo del dispositivo e del dispositivo gemello per i metodi diretti e gli aggiornamenti del dispositivo gemello:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` contiene anche le informazioni sulla connessione. La variabile di ambiente **IOTHUB_DEVICE_SECURITY_TYPE** determina se per l'esempio viene usata una stringa di connessione o il servizio di provisioning di dispositivi per connettersi all'hub IoT.

Quando il dispositivo invia un ID modello, diventa un dispositivo Plug and Play IoT.

Se i gestori di callback sono configurati, il dispositivo reagisce agli aggiornamenti del dispositivo gemello e alle chiamate a metodi diretti:

* Per il callback del dispositivo gemello, `PnP_TempControlComponent_DeviceTwinCallback` chiama la funzione `PnP_ProcessTwinData` per elaborare i dati. `PnP_ProcessTwinData` usa il *modello visitor* per analizzare il codice JSON e quindi visitare ogni proprietà, chiamando `PnP_TempControlComponent_ApplicationPropertyCallback` su ogni elemento.

* Per il callback dei comandi, la funzione `PnP_TempControlComponent_DeviceMethodCallback` usa la funzione helper per analizzare i nomi di comandi e componenti:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    La funzione `PnP_TempControlComponent_DeviceMethodCallback` chiama quindi il comando sul componente:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

La funzione `main` inizializza le proprietà di sola lettura inviate all'hub IoT:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

La funzione `main` immette un ciclo per aggiornare i dati di telemetria e degli eventi per ogni componente:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

La `PnP_ThermostatComponent_SendTelemetry` funzione mostra come usare lo struct `PNP_THERMOSTAT_COMPONENT`. Questo struct viene usato nell'esempio per archiviare le informazioni sui due termostati nel dispositivo termoregolatore. Il codice usa la funzione `PnP_CreateTelemetryMessageHandle` per preparare il messaggio e inviarlo:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

La funzione `main`, infine, elimina definitivamente i diversi componenti e chiude la connessione all'hub.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)