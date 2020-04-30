---
title: Interfacce comuni-Plug and Play anteprima | Microsoft Docs
description: Descrizione delle interfacce comuni per gli sviluppatori Plug and Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770480"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfacce comuni Plug and Play anteprima

Per tutti i dispositivi Plug and Play si prevede di implementare alcune interfacce comuni. Le interfacce comuni traggono vantaggio dalle soluzioni Internet perché forniscono funzionalità coerenti. Per la [certificazione](tutorial-build-device-certification.md) è necessario che il dispositivo implementi diverse interfacce comuni. È possibile recuperare le definizioni di interfaccia comuni dal repository del modello pubblico.

## <a name="summary-of-common-interfaces"></a>Riepilogo delle interfacce comuni

| Name | ID | Descrizione | Implementato da Azure Internets SDK | Deve essere dichiarata nel modello di funzionalità |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informazioni sul modello | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Per i dispositivi per dichiarare l'ID del modello di funzionalità e le interfacce. Obbligatorio per tutti i dispositivi Plug and Play. | Sì | No |
| Informazioni su Digital Twin client SDK | urn: azureiot: client: SDKInformation: 1 | SDK client per la connessione del dispositivo ad Azure. Obbligatorio per la [certificazione](tutorial-build-device-certification.md) | Sì | No |
| Informazioni sul dispositivo | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informazioni sull'hardware e sul sistema operativo del dispositivo. Obbligatorio per la [certificazione](tutorial-build-device-certification.md) | No | Sì |
| Definizione del modello | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Per i dispositivi dichiarare la definizione completa per il modello di funzionalità e le interfacce. Deve essere implementato quando le definizioni di modello non sono ospitate in un repository di modelli. | No | Sì |
| Gemelli digitali | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Per gli sviluppatori di soluzioni recuperare l'ID del modello di funzionalità e gli ID interfaccia per un dispositivo gemello digitale. Questa interfaccia non è dichiarata o implementata da un dispositivo Plug and Play. | No | No |

- Implementata da Azure Internets SDK, che l'SDK di Azure è in cui vengono implementate le funzionalità dichiarate nelle interfacce. I dispositivi Plug and Play che usano l'SDK di Azure Internet non devono implementare questa interfaccia.
- Deve essere dichiarata nel modello di funzionalità: se è' Sì', questa interfaccia deve `"implements":` essere dichiarata all'interno della sezione del modello di funzionalità del dispositivo per questo plug and Play dispositivo.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperare le definizioni di interfaccia dal repository pubblico

### <a name="cli"></a>CLI

È possibile usare l'estensione Azure per l'interfaccia della riga di comando di Azure per recuperare le interfacce comuni dal repository del modello pubblico.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Visual Studio Code

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **plug and Play** e quindi selezionare il comando **plug and Play Internet: aprire il repository del modello** . Scegliere **repository pubblico**. Il repository del modello pubblico viene aperto in VS Code.

1. Nel repository del modello pubblico immettere il nome dell'interfaccia nel campo di ricerca.

1. Per creare una copia locale dell'interfaccia, selezionarla nei risultati della ricerca e quindi selezionare **Scarica**.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le interfacce comuni, di seguito sono riportate alcune risorse aggiuntive:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
