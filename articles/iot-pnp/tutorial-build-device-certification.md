---
title: Compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione | Microsoft Docs
description: Uno sviluppatore di dispositivi può apprendere come compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239899"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione

Questa esercitazione illustra agli sviluppatori di dispositivi come compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione.

I test di certificazione verificano che:

- Il codice del dispositivo Plug and Play IoT è installato sul proprio dispositivo.
- Il codice del dispositivo Plug and Play IoT è incorporato in Azure IoT SDK.
- Il codice del dispositivo supporta il [servizio Device Provisioning in hub IoT](../iot-dps/about-iot-dps.md).
- Il codice del dispositivo implementa l'interfaccia Device Information (Informazioni dispositivo).
- Il modello di funzionalità e il codice del dispositivo funzionano con IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

- [Visual Studio Code](https://code.visualstudio.com/download)
- Pacchetto di estensione [Azure IoT Tools per VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

È anche necessario completare l'argomento di avvio rapido [Usare un modello di funzionalità di dispositivo per creare un dispositivo](quickstart-create-pnp-device-windows.md) per Windows. L'argomento di avvio rapido illustra come configurare l'ambiente di sviluppo usando Vcpkg e come creare un progetto di esempio.

## <a name="store-a-capability-model-and-interfaces"></a>Archiviare un modello di funzionalità e le interfacce

Per i dispositivi Plug and Play IoT, è necessario creare un modello di funzionalità e interfacce che definiscono le funzionalità del dispositivo come file JSON.

È possibile archiviare questi file JSON in tre posizioni diverse:

- Repository del modello pubblico.
- Repository del modello aziendale.
- Nel dispositivo.

Attualmente, per certificare il dispositivo, i file devono essere archiviati nel repository del modello aziendale o nel repository del modello pubblico.

## <a name="include-the-required-interfaces"></a>Includere le interfacce necessarie

Per superare il processo di certificazione, è necessario includere e implementare l'interfaccia **Device Information** (Informazioni sul dispositivo) nel modello di funzionalità. Questa interfaccia presenta l'identificazione seguente:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Se è stata completato l'argomento [Avvio rapido: Usare un modello di funzionalità del dispositivo per creare un dispositivo](quickstart-create-pnp-device-windows.md). È già stata inclusa l'interfaccia **Device Information** (Informazioni sul dispositivo) nel modello.

Per includere l'interfaccia **Device Information** (Informazioni sul dispositivo) nel modello di dispositivo, aggiungere l'ID di interfaccia alla proprietà `implements` del modello di funzionalità:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Per visualizzare l'interfaccia **Device Information** (Informazioni sul dispositivo) in VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play**, quindi selezionare il comando **Open Model Repository** di Plug and Play IoT. Scegliere **Open Public Model Repository**. Il repository del modello pubblico viene aperto in VS Code.

1. Nel repository del modello pubblico selezionare la scheda **Interfaces** (Interfacce), selezionare l'icona del filtro e immettere le **Device Information** (Informazioni sul dispositivo) nel campo del filtro.

1. Per creare una copia locale dell'interfaccia **Device Information** (Informazioni sul dispositivo), selezionarla nell'elenco filtrato e quindi selezionare **Download** (Scarica). VS Code visualizza il file di interfaccia.

Per visualizzare l'interfaccia **Informazioni sul dispositivo** usando l'interfaccia della riga di comando di Azure:

1. [Installare l'estensione Azure IoT per l'interfaccia della riga di comando](howto-install-pnp-cli.md).

1. Usare il comando seguente dell'interfaccia della riga di comando di Azure per visualizzare un'interfaccia con l'ID dell'interfaccia di Informazioni sul dispositivo:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Per altre informazioni, vedere [Installare e usare l'estensione Azure IoT per l'interfaccia della riga di comando di Azure](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Aggiornare il codice del dispositivo

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Abilitare il provisioning attraverso il servizio Device Provisioning in Azure IoT (DPS)

Per certificare il dispositivo è necessario abilitare il provisioning attraverso il [servizio Device Provisioning in Azure IoT (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Per aggiungere la possibilità di usare DPS, è possibile generare lo stub del codice C in VS Code. A tale scopo, seguire questa procedura:

1. Aprire la cartella con il file DCM in VS Code, premere **CTRL + MAIUSC + P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

1. Scegliere il file del modello di funzionalità di dispositivo da usare per generare lo stub del codice del dispositivo.

1. Immettere il nome del progetto, ad esempio **sample_device**. Si tratta del nome dell'applicazione del dispositivo.

1. Scegliere **ANSI C** come linguaggio.

1. Scegliere **Via DPS (Device Provisioning Service) symmetric key** come metodo di connessione.

1. Scegliere **CMake Project on Windows** (Progetto CMake in Windows) come modello di progetto.

1. Scegliere la modalità **Via Vcpkg** (Tramite Vcpkg) per includere l'SDK per dispositivi.

1. VS Code apre una nuova finestra contenente i file stub di codice di dispositivo generati.

## <a name="build-and-run-the-code"></a>Compilare ed eseguire il codice

Per compilare lo stub del codice del dispositivo generato, usare il pacchetto Vcpkg. L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. Creare una sottodirectory `cmake` nella cartella `sample_device` e passare a tale cartella:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Eseguire questi comandi per compilare lo stub del codice generato (sostituendo il segnaposto con la directory del repository Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se si usa Visual Studio 2017 o 2015, è necessario specificare il generatore CMake in base agli strumenti di compilazione usati:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se cmake non trova il compilatore C++, vengono visualizzati errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando al [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Dopo aver completato la compilazione, immettere le credenziali DPS (**DPS ID Scope** (Ambito ID DPS), **DPS Symmetric Key** (Chiave simmetrica DPS), **Device Id** (ID dispositivo)) come parametri per l'applicazione. Per ottenere le credenziali dal portale di certificazione, vedere [Connettere e testare il dispositivo Plug and Play IoT](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementare interfacce standard

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementare le interfacce Model Information e SDK Information

Azure IoT SDK per dispositivi Implementa le interfacce Model Information e SDK Information. Se si usa la funzione di generazione del codice in VS Code, il codice del dispositivo usa Plug and Play IoT SDK per dispositivi.

Se si sceglie di non usare Azure IoT SDK per dispositivi, è possibile usare il codice sorgente dell'SDK come riferimento per la propria implementazione.

#### <a name="implement-the-device-information-interface"></a>Implementare l'interfaccia Device Information

Implementare l'interfaccia **Device Information** nel dispositivo e fornire informazioni specifiche del dispositivo in fase di esecuzione.

Come riferimento, è possibile usare un'implementazione di esempio dell'interfaccia **Device Information** per [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementare tutte le funzionalità definite nel modello

Durante la certificazione, il dispositivo viene testato a livello di codice per assicurarsi che implementi le funzionalità definite nelle relative interfacce. Usare il codice di stato HTTP 501 per rispondere alle richieste di comandi e proprietà di lettura e scrittura se il dispositivo non le implementa.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un dispositivo Plug and Play IoT pronto per la certificazione, come passaggio successivo si consiglia di ottenere:

> [!div class="nextstepaction"]
> [Informazioni su come certificare il dispositivo](tutorial-certification-test.md)
