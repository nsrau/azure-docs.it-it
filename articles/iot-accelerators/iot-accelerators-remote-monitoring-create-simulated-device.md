---
title: Simulazione di dispositivi con Monitoraggio remoto IoT - Azure | Microsoft Docs
description: Questa guida alle procedure mostra come usare il simulatore di dispositivi con l'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/28/2018
ms.topic: conceptual
ms.openlocfilehash: 06909c06087219cdfa6edccbfed70ea1694e5a03
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633728"
---
# <a name="create-and-test-a-new-simulated-device"></a>Creare e testare un nuovo dispositivo simulato

L'acceleratore di soluzione Monitoraggio remoto consente di definire dispositivi simulati personalizzati. Questo articolo illustra come definire un nuovo dispositivo lampadina simulato e quindi testarlo in locale. L'acceleratore di soluzione include dispositivi simulati, ad esempio refrigeratori e veicoli. Tuttavia, è possibile definire dispositivi simulati personalizzati per testare le soluzioni IoT prima di distribuire i dispositivi reali.

> [!NOTE]
> L'articolo descrive le modalità secondo cui usare i dispositivi simulati ospitati nel servizio di simulazione del dispositivo. Se l'utente vuole creare un dispositivo fisico, vedere [Connettere il dispositivo all'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-connecting-devices.md).

Questa guida alle procedure illustra come personalizzare il microservizio di simulazione dispositivi. Questo microservizio fa parte dell'acceleratore di soluzione Monitoraggio remoto. Per illustrare le funzionalità di simulazione di dispositivi, questa guida alle procedure usa due scenari nell'applicazione IoT Contoso:

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha illustrato come creare tipi di dispositivi simulati personalizzati e testarli eseguendo il microservizio di simulazione dispositivi in locale.

Il passaggio successivo consigliato è apprendere come distribuire i tipi di dispositivi simulati personalizzati nell'[acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
