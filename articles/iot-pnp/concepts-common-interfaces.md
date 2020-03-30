---
title: Interfacce comuni - IoT Plug and Play Preview Documenti Microsoft
description: Descrizione delle interfacce comuni per gli sviluppatori IoT Plug and Play
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c6ac90f917b9afc37b3a39d8da679fbcad091778
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234696"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfacce comuni di IoT Plug and Play Preview

Tutti i dispositivi IoT Plug and Play sono tenuti a implementare alcune interfacce comuni. Le interfacce comuni traggono vantaggio da soluzioni IoT perché forniscono funzionalità coerenti. [La certificazione](tutorial-build-device-certification.md) richiede che il dispositivo implementi diverse interfacce comuni. È possibile recuperare le definizioni di interfaccia comuni dal repository dei modelli pubblici.

## <a name="summary-of-common-interfaces"></a>Riepilogo delle interfacce comuni

| Nome | ID | Descrizione | Implementato da Azure IoT SDK | Deve essere dichiarato nel modello di capacità |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informazioni sul modello | urn:azureiot:ModelDiscovery:ModelInformation:1 | Per i dispositivi per dichiarare l'ID e le interfacce del modello di funzionalità. Obbligatorio per tutti i dispositivi IoT Plug and Play. | Sì | No |
| Informazioni sull'SDK del client Gemello Digitale | urn:azureiot:Client:SDKInformation:1 | Client SDK for connecting the device with Azure. Obbligatorio per [la certificazione](tutorial-build-device-certification.md) | Sì | No |
| Informazioni sul dispositivo | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informazioni sull'hardware e sul sistema operativo relative al dispositivo. Obbligatorio per [la certificazione](tutorial-build-device-certification.md) | No | Sì |
| Definizione del modello | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Per i dispositivi dichiarare la definizione completa per il modello di funzionalità e le interfacce. Deve essere implementato quando le definizioni del modello non sono ospitate in un repository di modelli. | No | Sì |
| Gemelli digitali | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Per gli sviluppatori di soluzioni recuperare l'ID del modello di funzionalità e gli ID di interfaccia per un gemello digitale. Questa interfaccia non è dichiarata o implementata da un dispositivo Plug and Play IoT.This interface isn't declared or implemented by an IoT Plug and Play device. | No | No |

- Implementato da Azure IoT SDK - Indica se Azure IoT SDK implementa le funzionalità dichiarate nelle interfacce. I dispositivi Plug and Play IoT che usano l'SDK di Azure IoT non devono implementare questa interfaccia.
- Deve essere dichiarato nel modello di funzionalità - Se 'sì', questa interfaccia deve essere dichiarata all'interno `"implements":` della sezione del modello di funzionalità del dispositivo per questo dispositivo IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperare le definizioni di interfaccia dall'archivio pubblico

### <a name="cli"></a>CLI

È possibile usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per recuperare le interfacce comuni dal repository dei modelli pubblici.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Visual Studio Code

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play,** quindi selezionare il comando **IoT Plug and Play: Open Model Repository.** Scegliere **Archivio pubblico**. Il repository del modello pubblico viene aperto in VS Code.

1. Nel repository del modello pubblico, immettere il nome dell'interfaccia nel campo di ricerca.

1. Per creare una copia locale dell'interfaccia, selezionarla nei risultati della ricerca e quindi selezionare **Scarica**.

## <a name="next-steps"></a>Passaggi successivi

Ora che hai appreso sulle interfacce comuni, ecco alcune risorse aggiuntive:Now that you've learned about common interfaces, here are some additional resources:

- [DTDL (Digital Twin Definition Language)](https://aka.ms/DTDL)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
