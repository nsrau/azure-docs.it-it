---
title: Guida per gli sviluppatori di dispositivi (C)-Plug and Play Microsoft Docs
description: Descrizione delle Plug and Play Internet per gli sviluppatori di dispositivi C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 3aa236570e518b142adb8382387a8cdea4fc08a0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453271"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Guida per gli sviluppatori di dispositivi Plug and Play

Internet delle cose Plug and Play consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

Un Smart Device può essere implementato direttamente, usare i [moduli](../iot-hub/iot-hub-devguide-module-twins.md)o usare [IOT Edge moduli](../iot-edge/about-iot-edge.md).

Questa guida descrive i passaggi di base necessari per la creazione di un modulo di dispositivo, modulo o IoT Edge che segue le [convenzioni plug and Play](../iot-pnp/concepts-convention.md).

Per compilare un modulo Plug and Play di dispositivi, moduli o IoT Edge, seguire questa procedura:

1. Verificare che il dispositivo usi il protocollo MQTT o MQTT over WebSocket per connettersi all'hub di Azure.
1. Creare un modello [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) per descrivere il dispositivo. Per altre informazioni, vedere informazioni [sui componenti nei modelli plug and Play](concepts-components.md).
1. Aggiornare il dispositivo o il modulo per annunciare `model-id` come parte della connessione del dispositivo.
1. Implementare la telemetria, le proprietà e i comandi usando le [convenzioni plug and Play](concepts-convention.md)

Quando l'implementazione del dispositivo o del modulo è pronta, usare [Esplora risorse di Azure](howto-use-iot-explorer.md) per verificare che il dispositivo segua le convenzioni plug and Play.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le informazioni sullo sviluppo di dispositivi Plug and Play, di seguito sono riportate alcune risorse aggiuntive:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](/rest/api/iothub/device)
- [Componenti del modello](concepts-components.md)
- [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md)
- [Guida per gli sviluppatori del servizio Plug and Play](concepts-developer-guide-service.md)