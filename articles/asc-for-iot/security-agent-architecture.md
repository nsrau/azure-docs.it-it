---
title: Informazioni sul Centro sicurezza di Azure per l'architettura dell'agente di sicurezza IoT Preview | Microsoft Docs
description: Comprendere l'architettura dell'agente di protezione per gli agenti utilizzati nel Centro sicurezza di Azure per il servizio IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 3c05b7e9b1c6d1b9214da168f7abfcbb322f8f6d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192525"
---
# <a name="security-agent-reference-architecture"></a>Architettura di riferimento di sicurezza dell'agente

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Il Centro sicurezza di Azure (ASC) per IoT fornisce l'architettura di riferimento per gli agenti di protezione di log, elaborano, aggregano e inviino i dati di sicurezza tramite l'hub IoT.

Gli agenti di protezione sono progettati per funzionare in un ambiente vincolato IoT e sono altamente personalizzabili in termini di valori che forniscono quando confrontato con le risorse utilizzate.

Gli agenti di protezione supportano le funzionalità seguenti:

- Raccogli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux, Windows). Per altre informazioni sugli agenti di raccolta dati sicurezza disponibili, vedere [Centro sicurezza di AZURE per la configurazione dell'agente di IoT](how-to-agent-configuration.md).

- Aggregare eventi di sicurezza non elaborati in messaggi inviati tramite l'hub IoT.

- Eseguire l'autenticazione con l'identità del dispositivo esistente o un'identità del modulo dedicato. Visualizzare [metodi di autenticazione di sicurezza dell'agente](concept-security-agent-authentication-methods.md) per altre informazioni.

- Configurare in remoto tramite l'uso del **azureiotsecurity** modulo gemello. Per altre informazioni, vedere [configurare un Centro sicurezza di AZURE per l'agente di IoT](how-to-agent-configuration.md).

Centro sicurezza di AZURE per gli agenti di sicurezza IoT vengono sviluppate come progetti open source e disponibili da GitHub: 

- [Centro sicurezza di AZURE per l'agente basato su IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Centro sicurezza di AZURE per IoT C#-agente basato su](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Piattaforme supportate dell'agente

Centro sicurezza di AZURE per IoT offre programma di installazione diversi agenti a 32 bit e a 64 bit di Windows e gli stessi per Linux a 64 bit e a 32 bit. Assicurarsi di avere il programma di installazione corretta dell'agente per ognuno dei dispositivi in base alla tabella riportata di seguito:

| 32 bit o 64 bit | Linux | Windows |    Dettagli|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| a 32 bit  | C  | C#  ||
| a 64 bit  | C#o C           | C#      | Usare l'agente di C per i dispositivi con risorse minime|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso sul Centro sicurezza di AZURE per l'architettura dell'agente di sicurezza IoT e i programmi di installazione disponibili.

Per altre informazioni sul con Centro sicurezza di AZURE per la distribuzione IoT, usare gli articoli seguenti:

- Comprendere [i metodi di autenticazione di sicurezza dell'agente](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente protezione](how-to-deploy-agent.md)
- Esaminare il Centro sicurezza di AZURE per IoT [prerequisiti del servizio](service-prerequisites.md)
- Informazioni su come [Abilita Centro sicurezza di AZURE per il servizio IoT nell'IoT Hub](quickstart-onboard-iot-hub.md)
- Altre informazioni sul servizio dal [ASC per domande frequenti su IoT](resources-frequently-asked-questions.md)
