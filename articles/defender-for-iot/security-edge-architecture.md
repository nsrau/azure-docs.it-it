---
title: Modulo di sicurezza di Defender for Internet per IoT Edge
description: Informazioni sull'architettura e sulle funzionalità di Azure Defender per il modulo Security per IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 1933e60892430b907e070ea04f39d8acc86ddac2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937844"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Modulo Azure Defender for IoT Edge Security

[Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/) offre potenti funzionalità per la gestione e l'esecuzione di flussi di lavoro aziendali nei dispositivi perimetrali.
La parte principale che IoT Edge gioca negli ambienti Internet è particolarmente interessante per gli attori malintenzionati.

Defender for Internet Security Module fornisce una soluzione di sicurezza completa per i dispositivi IoT Edge.
Il modulo Defender for Internet raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema di contenitori in consigli e avvisi di sicurezza di utilità pratica.

Analogamente a Defender per gli agenti di sicurezza per i dispositivi Internet delle cose, il Defender per IoT Edge modulo è altamente personalizzabile tramite il modulo gemello.
Per altre informazioni, vedere [configurare l'agente](how-to-agent-configuration.md) .

Defender per il modulo Security per IoT Edge offre le funzionalità seguenti:

- Raccoglie gli eventi di sicurezza non elaborati dal sistema operativo (Linux) sottostante e dai sistemi contenitore IoT Edge.

  Per ulteriori informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere [Defender per la configurazione dell'agente](how-to-agent-configuration.md) Internet.

- Analisi dei manifesti di distribuzione di IoT Edge.

- Aggrega gli eventi di sicurezza non elaborati in messaggi inviati tramite [Hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Rimuovere la configurazione tramite l'uso del modulo di sicurezza gemello.

  Per altre informazioni, vedere [configurare un Defender per l'agente](how-to-agent-configuration.md) .

Defender per il modulo di sicurezza Internet per IoT Edge viene eseguito in modalità privilegiata in IoT Edge.
La modalità con privilegi è necessaria per consentire al modulo di monitorare il sistema operativo e altri moduli IoT Edge.

## <a name="module-supported-platforms"></a>Piattaforme supportate dal modulo

Il modulo di sicurezza di Defender per il IoT Edge è attualmente disponibile solo per Linux.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate l'architettura e le funzionalità di Defender per il modulo Security per IoT Edge.

Per continuare a usare Defender per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Distribuire il [modulo di sicurezza per IOT Edge](how-to-deploy-edge.md)
- Informazioni su come [configurare il modulo di sicurezza](how-to-agent-configuration.md)
- Esaminare i [prerequisiti del servizio](service-prerequisites.md) Defender per l'it
- Informazioni su come [abilitare Defender per il servizio Internet delle cose nell'hub Internet delle](quickstart-onboard-iot-hub.md) cose
- Altre informazioni sul servizio sono disponibili nella pagina [relativa alle domande frequenti su Defender](resources-frequently-asked-questions.md)
