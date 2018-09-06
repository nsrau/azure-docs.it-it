---
title: Schema dei dispositivi nella soluzione di simulazione dei dispositivi - Azure | Microsoft Docs
description: Questo articolo descrive lo schema JSON che definisce un dispositivo simulato nella soluzione di simulazione dei dispositivi.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338156"
---
# <a name="understand-the-device-model-schema"></a>Informazioni sullo schema del modello dei dispositivi

È possibile usare l'acceleratore di soluzione Simulazione dispositivi per generare dei carichi di test per le soluzioni che usano un hub IoT. Quando si distribuisce la soluzione di simulazione dei dispositivi, viene eseguito automaticamente il provisioning di una raccolta di dispositivi simulati. È possibile personalizzare i dispositivi simulati esistenti o crearne di nuovi.

Questo articolo descrive lo schema del modello di dispositivo che specifica le funzionalità e il comportamento di un dispositivo simulato. Il modello del dispositivo viene archiviato in un file JSON.

Gli articoli seguenti sono correlati all'articolo corrente:

* [Implement the device model behavior](iot-accelerators-device-simulation-device-behavior.md) (Implementare il comportamento del modello di dispositivo) descrive i file JavaScript usati per implementare il comportamento di un dispositivo simulato.
* [Creare un nuovo dispositivo simulato](iot-accelerators-device-simulation-create-simulated-device.md) riunisce tutti gli aspetti e illustra come distribuire un nuovo tipo di dispositivo simulato nella soluzione.

In questo articolo viene spiegato come:

>[!div class="checklist"]
> * Usare un file JSON per definire un modello di dispositivo simulato
> * Specificare le proprietà del dispositivo simulato
> * Specificare i dati di telemetria inviati dal dispositivo simulato
> * Specificare i metodi da cloud a dispositivo a cui risponde il dispositivo

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come creare un modello di dispositivo simulato personalizzato. In questo articolo sono state presentate le procedure per:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usare un file JSON per definire un modello di dispositivo simulato
> * Specificare le proprietà del dispositivo simulato
> * Specificare i dati di telemetria inviati dal dispositivo simulato
> * Specificare i metodi da cloud a dispositivo a cui risponde il dispositivo

Dopo aver appreso alcune informazioni di base sullo schema JSON, il passaggio successivo consigliato consiste nello scoprire come [implementare il comportamento del dispositivo simulato](iot-accelerators-device-simulation-device-behavior.md).

Per altre informazioni per sviluppatori sulla soluzione di simulazione dei dispositivi, vedere la [guida di riferimento per sviluppatori](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
