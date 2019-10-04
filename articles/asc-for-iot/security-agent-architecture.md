---
title: Informazioni sul centro sicurezza di Azure per l'architettura dell'agente Security Microsoft Docs
description: Comprendere l'architettura dell'agente di sicurezza per gli agenti usati nel centro sicurezza di Azure per il servizio Internet delle cose.
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
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596900"
---
# <a name="security-agent-reference-architecture"></a>Architettura di riferimento dell'agente di sicurezza

Il Centro sicurezza di Azure offre un'architettura di riferimento per gli agenti di sicurezza che registrano, elaborano, aggregano e inviano i dati di sicurezza tramite l'hub Internet.

Gli agenti di sicurezza sono progettati per funzionare in un ambiente di gestione delle risorse umane vincolato e sono altamente personalizzabili in termini di valori forniti rispetto alle risorse che utilizzano.

Gli agenti di sicurezza supportano le funzionalità seguenti:

- Raccogliere gli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux, Windows). Per altre informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere il [Centro sicurezza di Azure per la configurazione dell'agente](how-to-agent-configuration.md)Internet.

- Aggregare gli eventi di sicurezza non elaborati in messaggi inviati tramite l'hub.

- Eseguire l'autenticazione con l'identità del dispositivo esistente o un'identità del modulo dedicata. Per altre informazioni, vedere [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md) .

- Configurare in modalità remota tramite l'uso del dispositivo gemello del modulo **azureiotsecurity** . Per altre informazioni, vedere [configurare un centro sicurezza di Azure per l'agente](how-to-agent-configuration.md).

Il Centro sicurezza di Azure per gli agenti di sicurezza è stato sviluppato come progetto open source e disponibile da GitHub: 

- [Centro sicurezza di Azure per l'agente basato su C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Centro sicurezza di Azure per C#l'agente basato su Internet](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Piattaforme supportate dagli agenti

Il Centro sicurezza di Azure offre diversi agenti di installazione per le finestre a 32 bit e 64 bit e lo stesso per Linux a 32 bit e 64 bit. Verificare che sia installato il programma di installazione dell'agente corretto per ogni dispositivo in base alla tabella seguente:

| Architettura | Linux | Windows |    Dettagli|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| a 32 bit  | C  | C#  ||
| a 64 bit  | C#o C           | C#      | È consigliabile usare l'agente C per i dispositivi con risorse del dispositivo più limitate o minime.|
|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come il Centro sicurezza di Azure per l'architettura dell'agente sicurezza di Internet e i programmi di installazione disponibili.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Informazioni sui [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente di sicurezza](how-to-deploy-agent.md)
- Esaminare il Centro sicurezza di Azure per i [prerequisiti del servizio](service-prerequisites.md) Internet
- Informazioni su come [abilitare il Centro sicurezza di Azure per il servizio](quickstart-onboard-iot-hub.md) Internet delle cose nell'hub Internet delle cose
- Per altre informazioni sul servizio, vedere [domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)
