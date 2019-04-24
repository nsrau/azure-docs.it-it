---
title: Informazioni sul Centro sicurezza di Azure per IoT modulo di sicurezza per IoT Edge | Microsoft Docs
description: Comprendere l'architettura e le funzionalità del Centro sicurezza di Azure per IoT modulo di protezione di IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: c6ac3d9dbbb16caed51243fea852adea541b9f04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505404"
---
# <a name="azure-iot-edge-security-module"></a>Modulo di sicurezza di Azure IoT Edge

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per worklo§1ads di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) offre potenti funzionalità per gestire ed eseguire flussi di lavoro aziendali nei dispositivi perimetrali.
La parte principale di IoT Edge svolge negli ambienti IoT lo rendono particolarmente interessanti per gli attori dannosi.

Azure Security Center (Centro sicurezza di AZURE) per il modulo di sicurezza IoT offre una soluzione di sicurezza completa per l'IoT Edge dispositivi.
Centro sicurezza di AZURE per il modulo di IoT raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e system del contenitore in avvisi e raccomandazioni sulla sicurezza utili.

Analogamente al Centro sicurezza di AZURE per IoT gli agenti di protezione per i dispositivi IoT, il Centro sicurezza di AZURE per il modulo di IoT Edge è altamente personalizzabile tramite il modulo gemello.
Visualizzare [configurare l'agente](how-to-agent-configuration.md) per altre informazioni.

Centro sicurezza di AZURE per IoT modulo di sicurezza per IoT Edge offre le funzionalità seguenti:

- Raccoglie gli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux) e i sistemi di contenitore di IoT Edge.
  
  Visualizzare [Centro sicurezza di AZURE per la configurazione dell'agente di IoT](how-to-agent-configuration.md) per altre informazioni sugli agenti di raccolta dati sicurezza disponibili.

- Analisi dei manifesti di distribuzione di IoT Edge.

- Aggrega gli eventi di sicurezza non elaborati in messaggi inviati tramite [Hub di IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Rimuovere la configurazione tramite l'uso del dispositivo gemello del modulo di protezione.

  Visualizzare [configurare un Centro sicurezza di AZURE per l'agente di IoT](how-to-agent-configuration.md) per altre informazioni.

Centro sicurezza di AZURE per IoT modulo di sicurezza per IoT Edge viene eseguito in modalità con privilegi in IoT Edge.
Modalità con privilegi è necessaria per consentire al modulo di monitoraggio del sistema operativo e altri moduli IoT Edge.

## <a name="agent-supported-platforms"></a>Piattaforme supportate dell'agente

Centro sicurezza di AZURE per IoT modulo di sicurezza per IoT Edge è attualmente disponibile solo per Linux.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso sull'architettura e la funzionalità del Centro sicurezza di AZURE per il modulo di sicurezza IoT per IoT Edge.

Per altre informazioni sul con Centro sicurezza di AZURE per la distribuzione IoT, usare gli articoli seguenti:

- Distribuire [modulo di sicurezza per IoT Edge](how-to-deploy-edge.md)
- Informazioni su come [configurare il modulo di protezione](how-to-agent-configuration.md)
- Esaminare il Centro sicurezza di AZURE per IoT [prerequisiti del servizio](service-prerequisites.md)
- Informazioni su come [Abilita Centro sicurezza di AZURE per il servizio IoT nell'IoT Hub](quickstart-onboard-iot-hub.md)
- Altre informazioni sul servizio dal [ASC per domande frequenti su IoT](resources-frequently-asked-questions.md)