---
title: Comportamento di un dispositivo simulato nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive come usare JavaScript per definire il comportamento di un dispositivo simulato nella soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 70f9ccbbe737bad4d6f88365e804d4421c418d28
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164008"
---
# <a name="implement-the-device-model-behavior"></a>Implementare il comportamento del modello di dispositivo

L'articolo [Understand the device model schema](iot-accelerators-remote-monitoring-device-schema.md) (Conoscere lo schema del modello di dispositivo) descrive lo schema che definisce un modello di dispositivo simulato. L'articolo fa riferimento a due tipi di file JavaScript che implementano il comportamento di un dispositivo simulato:

- File JavaScript di tipo **State** che vengono eseguiti a intervalli prefissati per aggiornare lo stato interno del dispositivo.
- File JavaScript di tipo **Method** che vengono eseguiti quando la soluzione richiama un metodo nel dispositivo.

In questo articolo viene spiegato come:

>[!div class="checklist"]
> * Controllare lo stato di un dispositivo simulato.
> * Definire come deve rispondere un dispositivo simulato a una chiamata al metodo dalla soluzione di monitoraggio remoto
> * Eseguire il debug degli script.

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha spiegato come definire il comportamento di un modello di dispositivo simulato personalizzato. Sono state illustrate le procedure per eseguire queste operazioni:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controllare lo stato di un dispositivo simulato.
> * Definire come deve rispondere un dispositivo simulato a una chiamata al metodo dalla soluzione di monitoraggio remoto
> * Eseguire il debug degli script.

Ora che si è appreso come specificare il comportamento di un dispositivo simulato, il passaggio successivo consigliato consente di apprendere come [creare un dispositivo simulato](iot-accelerators-remote-monitoring-create-simulated-device.md).

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
