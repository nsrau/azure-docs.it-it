---
title: Compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione | Microsoft Docs
description: Uno sviluppatore di dispositivi può apprendere come compilare un dispositivo di anteprima Plug and Play IoT pronto per la certificazione.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: bfa611eba8e7a990626fbace8b930962615e0594
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878752"
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
- [Estensione Azure IoT Workbench per VS Code](https://github.com/Azure/Azure-IoT-PnP-Preview/blob/master/VSCode/README.md#installation)

È necessario anche il dispositivo Plug and Play IoT creato nell'argomento [Avvio rapido: Usare un modello di funzionalità di dispositivo per creare un dispositivo](quickstart-create-pnp-device.md).

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
> Se è stata completato l'argomento [Avvio rapido: Usare un modello di funzionalità del dispositivo per creare un dispositivo](quickstart-create-pnp-device.md). È già stata inclusa l'interfaccia **Device Information** (Informazioni sul dispositivo) nel modello.

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
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Per visualizzare l'interfaccia **Device Information** (Informazioni sul dispositivo) in VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play**, quindi selezionare il comando **Open Model Repository** di Plug and Play IoT. Scegliere **Open Public Model Repository**. Il repository del modello pubblico viene aperto in VS Code.

1. Nel repository del modello pubblico selezionare la scheda **Interfaces** (Interfacce), selezionare l'icona del filtro e immettere le **Device Information** (Informazioni sul dispositivo) nel campo del filtro.

1. Per creare una copia locale dell'interfaccia **Device Information** (Informazioni sul dispositivo), selezionarla nell'elenco filtrato e quindi selezionare **Download** (Scarica). VS Code visualizza il file di interfaccia.

## <a name="update-device-code"></a>Aggiornare il codice del dispositivo

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Abilitare il provisioning attraverso il servizio Device Provisioning in Azure IoT (DPS)

Per certificare il dispositivo è necessario abilitare il provisioning attraverso il [servizio Device Provisioning in Azure IoT (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Per aggiungere la possibilità di usare DPS, è possibile generare lo stub del codice C in VS Code. A tale scopo, seguire questa procedura:

1. Aprire la cartella con il file DCM in VS Code, premere **CTRL + MAIUSC + P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

1. Scegliere il file DCM che si vuole usare per generare lo stub del codice del dispositivo.

1. Immettere il nome del progetto, ovvero il nome dell'applicazione del dispositivo.

1. Scegliere **ANSI C** come linguaggio.

1. Scegliere **CMake Project** come tipo di progetto.

1. Scegliere **Via DPS (Device Provisioning Service) symmetric key** come metodo di connessione.

1. VS Code apre una nuova finestra contenente i file stub di codice generati.

1. Aprire `main.c` e compilare i valori **dpsIdScope**, **sasKey** e **registrationId** preparati. È possibile ottenere queste informazioni dal portale di certificazione. Per altre informazioni, vedere come [connettere e testare il dispositivo Plug and Play IoT](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. Salvare il file.

### <a name="implement-standard-interfaces"></a>Implementare interfacce standard

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementare le interfacce Model Information e SDK Information

Azure IoT SDK per dispositivi Implementa le interfacce Model Information e SDK Information. Se si usa la funzione di generazione del codice in VS Code, il codice del dispositivo usa Azure Plug and Play IoT SDK per dispositivi.

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
