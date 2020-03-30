---
title: Informazioni sul modulo di sicurezza di Azure per IoT per IoT Edge Documenti Microsoft
description: Comprendere l'architettura e le funzionalità del modulo di sicurezza del Centro sicurezza di Azure per IoT per IoT Edge.Understand the architecture and capabilities of Azure Security Center for IoT security module for IoT Edge.
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
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315899"
---
# <a name="azure-iot-edge-security-module"></a>Modulo di sicurezza Azure IoT EdgeAzure IoT Edge security module

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) offre potenti funzionalità per gestire ed eseguire flussi di lavoro aziendali all'edge.
La parte chiave che IoT Edge svolge in ambienti IoT lo rendono particolarmente attraente per gli attori malintenzionati.

Il modulo di sicurezza di Azure per IoT offre una soluzione di sicurezza completa per i dispositivi IoT Edge.Azure Security Center for IoT security module provides a comprehensive security solution for your IoT Edge devices.
Il Centro sicurezza di Azure per il modulo IoT raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema contenitore in avvisi e suggerimenti per la sicurezza utilizzabile.

Simile al Centro sicurezza di Azure per gli agenti di sicurezza IoT per i dispositivi IoT, il modulo Centro sicurezza di Azure per IoT Edge è altamente personalizzabile tramite il modulo gemello.
Per ulteriori informazioni, vedere [Configurare l'agente.](how-to-agent-configuration.md)

Il modulo di sicurezza di Azure Security Center for IoT per IoT Edge offre le funzionalità seguenti:Azure Security Center for IoT security module for IoT Edge offers the following features:

- Raccoglie gli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux) e dai sistemi IoT Edge Container.
  
  Per altre informazioni sugli agenti di sicurezza disponibili, vedere Centro sicurezza di Azure per la [configurazione dell'agente IoT.See Azure Security Center for IoT agent configuration](how-to-agent-configuration.md) to learn more about available security data collectors.

- Analisi dei manifesti di distribuzione di IoT Edge.

- Aggrega gli eventi di sicurezza non elaborati nei messaggi inviati tramite [IoT Edge Hub.](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)

- Rimuovere la configurazione tramite l'utilizzo del modulo di sicurezza gemello.

  Per altre informazioni, vedere Configurare un agente del Centro sicurezza di [Azure per IoT.See Configure an Azure Security Center for IoT agent](how-to-agent-configuration.md) to learn more.

Azure Security Center for IoT security module for IoT Edge runs in a privileged mode under IoT Edge.
La modalità privilegiata è necessaria per consentire al modulo di monitorare il sistema operativo e altri moduli IoT Edge.

## <a name="module-supported-platforms"></a>Piattaforme supportate da moduli

Azure Security Center for IoT security module for IoT Edge is currently only available for Linux. 

## <a name="next-steps"></a>Passaggi successivi

In this article, you learned about the architecture and capabilities of Azure Security Center for IoT security module for IoT Edge.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione IoT, usare gli articoli seguenti:To continue getting started with Azure Security Center for IoT deployment, use the following articles:

- Distribuire il modulo di [sicurezza per IoT Edge](how-to-deploy-edge.md)
- Scopri come [configurare il modulo](how-to-agent-configuration.md) di sicurezza
- Esaminare il Centro sicurezza di Azure per [i prerequisiti](service-prerequisites.md) del servizio IoTReview the Azure Security Center for IoT Service prerequisites
- Informazioni su come abilitare il Centro sicurezza di [Azure per il servizio IoT nell'hub IoT](quickstart-onboard-iot-hub.md)
- Altre informazioni sul servizio dal Centro sicurezza di [Azure per IoT](resources-frequently-asked-questions.md)