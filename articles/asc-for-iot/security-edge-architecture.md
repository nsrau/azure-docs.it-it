---
title: La comprensione di Centro sicurezza di AZURE per il modulo di sicurezza IoT per IoT Edge | Microsoft Docs
description: Comprendere l'architettura e le funzionalità del Centro sicurezza di AZURE per IoT modulo di protezione di IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 68117370e21c7b667bde5447cef510a4e7c77df0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580764"
---
# <a name="azure-iot-edge-security-module"></a>Modulo di sicurezza di Azure IoT Edge

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per worklo§1ads di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) offre potenti funzionalità per gestire ed eseguire flussi di lavoro aziendali nei dispositivi perimetrali.
La parte principale di IoT Edge svolge negli ambienti IoT lo rendono particolarmente interessanti per gli attori dannosi.

Centro sicurezza di AZURE per il modulo di sicurezza IoT offre una soluzione di sicurezza completa per l'IoT Edge dispositivi.
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