---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580070"
---
Internet delle cose Plug and Play consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

Un Smart Device può essere implementato direttamente, usare i [moduli](../articles/iot-hub/iot-hub-devguide-module-twins.md)o usare [IOT Edge moduli](../articles/iot-edge/about-iot-edge.md).

Questa guida descrive i passaggi di base necessari per la creazione di un modulo di dispositivo, modulo o IoT Edge che segue le [convenzioni plug and Play](../articles/iot-pnp/concepts-convention.md).

Per compilare un modulo Plug and Play di dispositivi, moduli o IoT Edge, seguire questa procedura:

1. Verificare che il dispositivo usi il protocollo MQTT o MQTT over WebSocket per connettersi all'hub di Azure.
1. Creare un modello [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) per descrivere il dispositivo. Per altre informazioni, vedere informazioni [sui componenti nei modelli plug and Play](../articles/iot-pnp/concepts-components.md).
1. Aggiornare il dispositivo o il modulo per annunciare `model-id` come parte della connessione del dispositivo.
1. Implementare la telemetria, le proprietà e i comandi usando le [convenzioni plug and Play](../articles/iot-pnp/concepts-convention.md)

Quando l'implementazione del dispositivo o del modulo è pronta, usare [Esplora risorse di Azure](../articles/iot-pnp/howto-use-iot-explorer.md) per verificare che il dispositivo segua le convenzioni plug and Play.
