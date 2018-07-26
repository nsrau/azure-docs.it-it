---
title: Usare un hub IoT esistente con la soluzione Simulazione dispositivi - Azure | Microsoft Docs
description: Questo articolo descrive come configurare l'acceleratore di soluzione Simulazione dispositivi per un hub IoT esistente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967512"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Usare un hub IoT esistente con l'acceleratore della soluzione Simulazione dispositivi

Quando si effettua il provisioning dell'acceleratore di soluzione Simulazione dispositivi, è possibile scegliere di distribuire un hub IoT nel gruppo di risorse dell'acceleratore di soluzione da usare nella simulazione.

Se si sceglie di non distribuire l'hub IoT facoltativo, è necessario usare il proprio hub per le simulazioni. Se si sceglie di distribuire l'hub IoT facoltativo, è possibile scegliere di usare questo hub facoltativo o il proprio hub.

Se non è disponibile un hub IoT, è sempre possibile crearne uno nuovo dal [portale di Azure](https://portal.azure.com).

Per usare un hub IoT pre-esistente, è necessaria una stringa di connessione per il criterio di accesso condiviso **iothubowner**. È possibile ottenere la stringa di connessione dal [portale di Azure](https://portal.azure.com):

1. Nella pagina di configurazione dell'hub nel portale, fare clic su **Criteri di accesso condiviso**.
1. Fare clic su **iothubowner**.
1. Copiare la stringa di connessione primaria o secondaria.

[![Ottenere una stringa di connessione](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Usare la stringa di connessione copiata quando si configura la simulazione:

[![Configurare una simulazione](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

In questa guida pratica si è appreso come usare un hub IoT esistente in una simulazione. Successivamente sarà possibile vedere come [Configurare un modello di dispositivo personalizzato](iot-accelerators-device-simulation-custom-model.md) per una simulazione.
