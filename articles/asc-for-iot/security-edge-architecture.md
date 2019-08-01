---
title: Informazioni sul centro sicurezza di Azure per il modulo di sicurezza Internet per IoT Edge Preview | Microsoft Docs
description: Informazioni sull'architettura e le funzionalità del Centro sicurezza di Azure per il modulo di sicurezza Internet per IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596863"
---
# <a name="azure-iot-edge-security-module"></a>Modulo di sicurezza Azure IoT Edge

> [!IMPORTANT]
> Il Centro sicurezza di Azure per il servizio IoT Edge è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/) offre potenti funzionalità per la gestione e l'esecuzione di flussi di lavoro aziendali nei dispositivi perimetrali.
La parte principale che IoT Edge gioca negli ambienti Internet è particolarmente interessante per gli attori malintenzionati.

Il modulo sicurezza del Centro sicurezza di Azure offre una soluzione di sicurezza completa per i dispositivi IoT Edge.
Il Centro sicurezza di Azure per il modulo cose raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema di contenitori in raccomandazioni e avvisi di sicurezza di utilità pratica.

Analogamente al centro sicurezza di Azure per gli agenti di sicurezza Internet per dispositivi Internet, il Centro sicurezza di Azure per il modulo IoT Edge è altamente personalizzabile tramite il modulo gemello.
Per altre informazioni, vedere [configurare l'agente](how-to-agent-configuration.md) .

Il Centro sicurezza di Azure per il modulo di sicurezza Internet per la IoT Edge offre le funzionalità seguenti:

- Raccoglie gli eventi di sicurezza non elaborati dal sistema operativo (Linux) sottostante e dai sistemi contenitore IoT Edge.
  
  Per ulteriori informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere il [Centro sicurezza di Azure per la configurazione dell'agente](how-to-agent-configuration.md) Internet.

- Analisi dei manifesti di distribuzione di IoT Edge.

- Aggrega gli eventi di sicurezza non elaborati in messaggi inviati tramite [Hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Rimuovere la configurazione tramite l'uso del modulo di sicurezza gemello.

  Per altre informazioni, vedere [configurare un centro sicurezza di Azure per l'agente](how-to-agent-configuration.md) .

Il Centro sicurezza di Azure per il modulo di sicurezza Internet per le IoT Edge viene eseguito in modalità privilegiata in IoT Edge.
La modalità con privilegi è necessaria per consentire al modulo di monitorare il sistema operativo e altri moduli IoT Edge.

## <a name="module-supported-platforms"></a>Piattaforme supportate dal modulo

Il Centro sicurezza di Azure per il modulo di sicurezza Internet per la IoT Edge è attualmente disponibile solo per Linux. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate le funzionalità e l'architettura del Centro sicurezza di Azure per il modulo Security per IoT Edge.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Distribuire il [modulo di sicurezza per IOT Edge](how-to-deploy-edge.md)
- Informazioni su come [configurare il modulo di sicurezza](how-to-agent-configuration.md)
- Esaminare il Centro sicurezza di Azure per i [prerequisiti del servizio](service-prerequisites.md) Internet
- Informazioni su come [abilitare il Centro sicurezza di Azure per il servizio](quickstart-onboard-iot-hub.md) Internet delle cose nell'hub Internet delle cose
- Per altre informazioni sul servizio, vedere [domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)