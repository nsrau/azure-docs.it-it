---
title: Iniziare a usare Azure Security Center (Centro sicurezza di AZURE) per l'anteprima di IoT | Microsoft Docs
description: Introduzione a comprendere il flusso di lavoro base del Centro sicurezza di Azure per le funzionalità di IoT e del servizio.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: bdc5e858286d7db03281408cf3ed00e0760e0c3a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200633"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Introduzione a Centro sicurezza di Azure per IoT 

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo fornisce una spiegazione dei diversi blocchi predefiniti di Centro sicurezza di Azure (ASC) per il servizio IoT e spiega come iniziare a usare [consentendo al servizio](quickstart-onboard-iot-hub.md). 

Centro sicurezza di AZURE per IoT possono essere integrate perfettamente in IoT Hub per l'analisi di sicurezza della configurazione dell'hub IoT, identità del dispositivo e hub-device i modelli di comunicazione.
Per la funzionalità di sicurezza avanzate, Centro sicurezza di AZURE per IoT fornisce basata su agenti di raccolta dei dati di sicurezza dai dispositivi IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>Centro sicurezza di AZURE per IoT perfetta integrazione dell'IoT Hub

Quando si tenta di proteggere i singoli dispositivi IoT, la possibilità di raccogliere i dati direttamente dai dispositivi, o dalla propria rete è obbligatoria. Per supportare questo sforzo, Centro sicurezza di AZURE per IoT offre un arsenale di agenti di protezione footprint ridotto per fornire protezione avanzata e monitoraggio dei dispositivi.

Nel Centro sicurezza di AZURE per l'anteprima di IoT, architettura di riferimento per Windows e Linux gli agenti di protezione, sia in C# e C sono disponibili.
Gli agenti di gestiscono la raccolta degli eventi non elaborati dal sistema operativo del dispositivo, aggregazione di eventi per ridurre costi e la configurazione tramite un modulo gemello dispositivo.
Tramite l'IoT Hub, vengono inviati messaggi di sicurezza nel Centro sicurezza di AZURE per i servizi IoT analitica.

## <a name="asc-for-iot-basics"></a>Centro sicurezza di AZURE per IoT nozioni di base

Scegliere lo scenario del flusso di lavoro che meglio soddisfa i requisiti di dispositivo e l'ambiente IoT:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Iniziare a usare Centro sicurezza di AZURE per IoT perfetta integrazione dell'IoT Hub 

>[!Note]
>Questo flusso di lavoro consente di usare il servizio senza usare Centro sicurezza di AZURE per gli agenti di sicurezza IoT. 

Per abilitare il monitoraggio del dispositivo gestione delle identità, dispositivo a cloud e da cloud a modelli di comunicazione dei dispositivi, utilizzare i seguenti base del flusso di lavoro per il test e per avviare il servizio: 

1. [Abilita Centro sicurezza di AZURE per il servizio IoT sull'IoT Hub](quickstart-onboard-iot-hub.md)
1. Se l'IoT Hub non sono presenti dispositivi registrati, [registrare un nuovo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Creare un modulo di protezione azureiotsecurity per i dispositivi](quickstart-create-security-twin.md) per i dispositivi. 
1. Definiscono il comportamento normale di dispositivo e sistema attraverso [avvisi personalizzati](quickstart-create-custom-alerts.md). 
1. Eseguire test per verificare lo stato del servizio e dispositivo di sistema. 
1. Esplorare [alerts](concept-security-alerts.md), [raccomandazioni](concept-recommendations.md), e [approfondimento usando Log Analitica](how-to-security-data-access.md) usando l'IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Iniziare a usare Centro sicurezza di AZURE per gli agenti di sicurezza IoT

Assicurarsi di usare Centro sicurezza di AZURE per le funzionalità di sicurezza IoT avanzata, ad esempio il monitoraggio delle connessioni remote, le applicazioni attive, gli eventi di accesso e consigliate di configurazione del sistema operativo usando il flusso di lavoro di base seguente per testare e abilitare il servizio: 

1. [Abilita Centro sicurezza di AZURE per il servizio IoT all'hub IoT](quickstart-onboard-iot-hub.md)
1. Se l'IoT Hub non sono presenti dispositivi registrati, [registrare un nuovo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Creare un modulo di protezione azureiotsecurity](quickstart-create-security-twin.md) per i dispositivi.
1. Per installare l'agente in un dispositivo simulato Azure anziché eseguire l'installazione in un dispositivo effettivo [selezione di una macchina virtuale di Azure nuova (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) in una zona di disponibilità. 
1. [Distribuire un Centro sicurezza di AZURE per l'agente di sicurezza IoT](how-to-deploy-linux-cs.md) sul dispositivo IoT o nuova macchina virtuale.
1. Seguire le istruzioni relative [trigger_events](https://aka.ms/iot-security-github-trigger-events) per eseguire una simulazione di un attacco innocuo.
1. Verificare di Centro sicurezza di AZURE per IoT gli avvisi in risposta all'attacco simulato nel passaggio precedente. Iniziare verifica cinque minuti dopo l'esecuzione dello script.
1. Esplorare [alerts](concept-security-alerts.md), [raccomandazioni](concept-recommendations.md), e [approfondimento usando Log Analitica](how-to-security-data-access.md) usando l'IoT Hub. 

## <a name="next-steps"></a>Passaggi successivi

- Abilitare [Centro sicurezza di AZURE per IoT](quickstart-onboard-iot-hub.md)
- Configurare il [soluzione](quickstart-configure-your-solution.md)
- [Creare moduli di sicurezza](quickstart-create-security-twin.md)
- Configurare [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
