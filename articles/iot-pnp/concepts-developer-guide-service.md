---
title: Guida per gli sviluppatori di servizi-Plug and Play Internet | Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori di servizi
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521389"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guida per gli sviluppatori del servizio Plug and Play

Internet delle cose Plug and Play consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

Internet delle cose Plug and Play consente di usare i dispositivi che hanno annunciato l'ID modello con l'hub Internet delle cose. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un Plug and Play dispositivo connesso all'hub Internet delle cose, uno degli SDK del servizio Internet:

## <a name="service-sdks"></a>SDK per servizi

Per interagire con i dispositivi e i moduli, usare gli SDK dei servizi di Azure. Ad esempio, è possibile usare gli SDK del servizio per leggere e aggiornare le proprietà dei dispositivi gemelli e richiamare i comandi. Le lingue supportate includono C#, Java, Node.js e Python.

Gli SDK dei servizi consentono di accedere alle informazioni sul dispositivo da una soluzione, ad esempio un'applicazione desktop o Web. Gli SDK dei servizi includono due spazi dei nomi e modelli a oggetti che è possibile usare per recuperare l'ID modello:

- Client del servizio hub Internet. Questo servizio espone l'ID modello come proprietà del dispositivo gemello.

- Client di dispositivi digitali gemelli. La nuova API dei gemelli digitali opera sui costrutti del modello [DTDL (Digital gemelle Definition Language)](concepts-digital-twin.md) , ad esempio componenti, proprietà e comandi. Le API dei dispositivi gemelli digitali semplificano la creazione di soluzioni Plug and Play per i generatori di soluzioni.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)
- [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md)