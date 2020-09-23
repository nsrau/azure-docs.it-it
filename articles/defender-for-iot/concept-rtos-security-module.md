---
title: Spiegazione concettuale delle nozioni di base del modulo Security per Azure RTO
description: Informazioni di base sul modulo di sicurezza per i concetti e il flusso di lavoro di Azure RTO.
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
ms.openlocfilehash: 9f816b35fd79d3f158ea12fd2c7e1d811823dc8a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939505"
---
# <a name="security-module-for-azure-rtos-preview"></a>Modulo di sicurezza per Azure RTO (anteprima)

Usare questo articolo per comprendere meglio il modulo di sicurezza per Azure RTO, incluse funzionalità e vantaggi, nonché collegamenti a risorse di configurazione e di riferimento pertinenti. 

## <a name="azure-rtos-iot-security-module"></a>Modulo sicurezza Azure RTO

Il modulo Security per Azure RTO offre una soluzione di sicurezza completa per i dispositivi RTO di Azure come parte dell'offerta NetX Duo. All'interno dell'offerta NetX Duo, Azure RTO è incluso nel modulo di sicurezza Azure Internet e fornisce la copertura per le minacce comuni sui dispositivi del sistema operativo in tempo reale, una volta attivati. 

Il modulo di sicurezza per Azure RTO viene eseguito in background e offre un'esperienza utente uniforme, mentre invia messaggi di sicurezza usando le connessioni univoche di ogni cliente all'hub Internet. Il modulo di sicurezza per Azure RTO è abilitato per impostazione predefinita.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo è uno stack di rete TCP/IP avanzato e di livello industriale progettato in modo specifico per le applicazioni in tempo reale e in tempo reale incorporate. Azure RTO NetX Duo è uno stack di rete IPv4 e IPv6 duale che fornisce un set completo di protocolli, tra cui sicurezza e cloud. Scopri di più sulle soluzioni [Azure RTO NETX Duo](https://aka.ms/netxduo) .

Il modulo offre le funzionalità seguenti:

- **Rilevare le attività di rete dannosa**
- **Linee di base del comportamento del dispositivo basate su avvisi personalizzati**
- **Migliorare l'igiene della sicurezza dei dispositivi**

## <a name="security-module-for-azure-rtos-architecture"></a>Modulo di sicurezza per l'architettura di Azure RTO

Il modulo di sicurezza per Azure RTO viene inizializzato dalla piattaforma middleware di Azure e usa i client dell'hub Internet per inviare i dati di telemetria di sicurezza all'hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagramma di stato del modulo di sicurezza di Azure e flusso di informazioni":::

Il modulo Security per Azure RTO monitora le attività e le informazioni del dispositivo seguenti con tre agenti di raccolta:
- Attività di rete del dispositivo **TCP**, **UDP**e **ICM**
- Informazioni di sistema come versioni di **threadX** e **NETX Duo**
- Eventi heartbeat

Ogni agente di raccolta è collegato a un gruppo di priorità e a ogni gruppo di priorità è associato un intervallo con i valori possibili di **basso**, **medio**e **alto**. Gli intervalli influiscono sull'intervallo di tempo in cui i dati vengono raccolti e inviati.

Ogni intervallo di tempo è configurabile e i connettori di Internet delle cose possono essere abilitati e disabilitati per [personalizzare ulteriormente la soluzione](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Avvisi e raccomandazioni di sicurezza supportati

Il modulo Security per Azure RTO supporta avvisi e raccomandazioni di sicurezza specifici. Dopo aver completato la configurazione iniziale, assicurarsi di [rivedere e personalizzare i valori di avviso e di raccomandazione pertinenti](concept-rtos-security-alerts-recommendations.md) per il servizio.

## <a name="ready-to-begin"></a>Pronti per iniziare?

Il modulo Security per Azure RTO è disponibile come download gratuito per i dispositivi Internet delle cose. Il servizio cloud Defender for Internet è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. [Scarica subito il modulo Security](https://github.com/azure-rtos/azure-iot-preview/releases) e inizia a usare. 

## <a name="next-steps"></a>Passaggi successivi

- Introduzione al modulo Security per i [prerequisiti e l'installazione](quickstart-azure-rtos-security-module.md)di Azure RTO.
- Altre informazioni sul modulo Security per gli avvisi di sicurezza di Azure RTO [e il supporto per le raccomandazioni](concept-rtos-security-alerts-recommendations.md). 
- Usare il modulo di sicurezza per l' [API di riferimento](azure-rtos-security-module-api.md)di Azure RTO.

