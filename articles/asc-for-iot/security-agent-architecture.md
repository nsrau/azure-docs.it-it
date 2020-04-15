---
title: Architettura dei security agent
description: Informazioni sull'architettura degli agenti di sicurezza per gli agenti usati nel Centro sicurezza di Azure per il servizio IoT.Understand security agent architecture for the agents used in the Azure Security Center for IoT service.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310675"
---
# <a name="security-agent-reference-architecture"></a>Architettura di riferimento del Security Agent

Azure Security Center for IoT provides reference architecture for security agents that log, process, aggregate, and send security data through IoT Hub.

Gli agenti di sicurezza sono progettati per funzionare in un ambiente IoT vincolato e sono altamente personalizzabili in termini di valori che forniscono rispetto alle risorse che consumano.

Gli agenti di sicurezza supportano le funzionalità seguenti:Security agent support the following features:

- Raccogliere gli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux, Windows). Per altre informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere Configurazione dell'agente di sicurezza di [Azure per ioT](how-to-agent-configuration.md).

- Aggregare gli eventi di sicurezza non elaborati nei messaggi inviati tramite l'hub IoT.Aggregate raw security events into messages sent through IoT Hub.

- Eseguire l'autenticazione con l'identità del dispositivo esistente o con un'identità del modulo dedicata. Per altre informazioni, vedere Metodi di [autenticazione dell'agente](concept-security-agent-authentication-methods.md) di sicurezza.

- Configurare in remoto tramite l'uso del modulo **gemello azureiotsecurity.** Per altre informazioni, vedere Configurare un agente di sicurezza di [Azure per l'IoT.](how-to-agent-configuration.md)

Azure Security Center for IoT Security agents are developed as open-source projects, and are available from GitHub:

- [Agente basato su C di Azure per IoTAzure Security Center for IoT C-based agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Centro sicurezza di Azure per l'agente basato su IoT in C](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Piattaforme supportate dall'agente

Il Centro sicurezza di Azure per IoT offre diversi agenti di installazione per Windows a 32 bit e 64 bit e lo stesso per Linux a 32 bit e 64 bit. Assicurarsi di disporre del programma di installazione dell'agente corretto per ogni dispositivo in base alla tabella seguente:

| Architecture | Linux | Windows |    Dettagli|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64 bit  | C o C           | C#      | È consigliabile usare l'agente C per i dispositivi con risorse di dispositivo più limitate o minime.|
|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi l'architettura dell'agente di sicurezza di Azure per l'architettura degli agenti di sicurezza IoT e i programmi di installazione disponibili.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione IoT, usare gli articoli seguenti:To continue getting started with Azure Security Center for IoT deployment, use the following articles:

- Comprendere i metodi di [autenticazione degli agenti di sicurezzaUnderstand security agent authentication methods](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente di sicurezza](how-to-deploy-agent.md)
- Esaminare i [prerequisiti](service-prerequisites.md) del servizio Di sicurezza di Azure per IoTReview the Azure Security Center for IoT service prerequisites
- Informazioni su come abilitare il Servizio di sicurezza di [Azure per ioT nell'hub IoT](quickstart-onboard-iot-hub.md)
- Altre informazioni sul servizio dal Centro sicurezza di [Azure per IoT](resources-frequently-asked-questions.md)
