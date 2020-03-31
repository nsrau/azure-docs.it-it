---
title: Informazioni sul Centro sicurezza di Azure per l'aggregazione di eventi IoT Documenti Microsoft
description: Informazioni sul Centro sicurezza di Azure per l'aggregazione di eventi IoT.Learn about Azure Security Center for IoT event aggregation.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928966"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Centro sicurezza di Azure per l'aggregazione di eventi IoTAzure Security Center for IoT event aggregation

Il Centro sicurezza di Azure per gli agenti di sicurezza IoT raccoglie dati ed eventi di sistema dal dispositivo locale e li invia al cloud di Azure per l'elaborazione e l'analisi. L'agente di sicurezza raccoglie molti tipi di eventi del dispositivo, inclusi nuovi processi e nuovi eventi di connessione. Sia il nuovo processo che i nuovi eventi di connessione possono legittimamente verificarsi frequentemente su un dispositivo in un secondo e, sebbene sia importante per una sicurezza solida e completa, il numero di messaggi che gli agenti di sicurezza sono costretti a inviare può raggiungere o superare rapidamente l'hub IoT limiti di quota e di costo. Tuttavia, questi eventi contengono informazioni di sicurezza di grande valore che sono cruciali per la protezione del dispositivo.

Per ridurre la quota e i costi aggiuntivi mantenendo protetti i dispositivi, il Centro sicurezza PC di Azure per gli agenti IoT aggrega questi tipi di eventi.

L'aggregazione degli eventi è **attivata** per impostazione predefinita e, sebbene non è consigliata, può essere **disattivata** manualmente in qualsiasi momento.

L'aggregazione è attualmente disponibile per i seguenti tipi di eventi:
* ProcessCreate (Creazione processo)
* ConnectionCrea
* ProcessTerminate (solo Windows)

## <a name="how-does-event-aggregation-work"></a>Come funziona l'aggregazione degli eventi?
Quando l'aggregazione di eventi viene **lasciata attivata,** centro sicurezza di Azure per gli agenti IoT aggrega gli eventi per il periodo di intervallo o l'intervallo di tempo.
Una volta trascorso il periodo di intervallo, l'agente invia gli eventi aggregati al cloud di Azure per un'ulteriore analisi.
Gli eventi aggregati vengono archiviati in memoria fino a quando non vengono inviati al cloud di Azure.The aggregated events are stored in memory until being sent to the Azure cloud.

Per ridurre il footprint di memoria dell'agente, ogni volta che l'agente raccoglie un evento identico a uno che è già mantenuto in memoria, l'agente aumenta il numero di hit di questo evento specifico. Quando passa l'intervallo di tempo di aggregazione, l'agente invia il numero di passaggi di ogni tipo specifico di evento che si è verificato. L'aggregazione di eventi è semplicemente l'aggregazione dei conteggi dei riscontri di ogni tipo di evento raccolto.

Gli eventi sono considerati identici solo quando vengono soddisfatte le seguenti condizioni: 

* Eventi ProcessCreate - quando **commandLine**, **executable**, **username**e **userid** sono identici
* Eventi ConnectionCreate - quando **commandLine**, **userId**, **direction**, **indirizzo locale**, **indirizzo remoto**, protocollo e porta di **destinazione** sono identici
* Eventi ProcessTerminate - quando lo stato **dell'eseguibile** e **dell'uscita** sono identici

### <a name="working-with-aggregated-events"></a>Utilizzo di eventi aggregati

Durante l'aggregazione, le proprietà degli eventi non aggregate vengono eliminate e visualizzate nell'analisi dei log con un valore pari a 0.During aggregation, event properties that are not aggregated are discarded, and appear in log analytics with a value of 0. 
* Eventi ProcessCreate - **processId**e **parentProcessId** sono impostati su 0
* Gli eventi ConnectionCreate - **processId**e la **porta di origine** sono impostati su 0

## <a name="event-aggregation-based-alerts"></a>Avvisi basati sull'aggregazione di eventi 
Dopo l'analisi, il Centro sicurezza di Azure per l'IoT crea avvisi di sicurezza per eventi aggregati sospetti. Gli avvisi creati da eventi aggregati vengono visualizzati una sola volta per ogni evento aggregato.

L'ora di inizio, l'ora di fine e il numero di passaggi per ogni evento vengono registrati nel campo **ExtraDetails** dell'evento in Log Analytics da utilizzare durante le indagini. 

Ogni evento aggregato rappresenta un periodo di 24 ore di avvisi raccolti. Utilizzando il menu delle opzioni dell'evento nell'angolo in alto a sinistra di ogni evento, è possibile **ignorare** ogni singolo evento aggregato.    

## <a name="event-aggregation-twin-configuration"></a>Configurazione doppia di aggregazione degli eventi
Apportare modifiche alla configurazione del Centro sicurezza di Azure per l'aggregazione di eventi IoT all'interno dell'oggetto di [configurazione dell'agente](how-to-agent-configuration.md) dell'identità del modulo gemello del modulo **azureiotsecurity.**

| Nome configurazione | Valori possibili | Dettagli | Osservazioni |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Abilitare/disabilitare l'aggregazione di eventi per gli eventi di creazione del processoEnable / disable event aggregation for process create events |
| aggregationIntervalProcessCreate | Stringa timespan ISO8601 | Intervallo di aggregazione per gli eventi di creazione del processo |
| aggregationEnabledConnectionCreate | boolean| Abilitare/disabilitare l'aggregazione di eventi per gli eventi di creazione della connessioneEnable / disable event aggregation for connection create events |
| aggregationIntervalConnectionCreate | Stringa timespan ISO8601 | Intervallo di aggregazione per gli eventi di creazione della connessione |
| aggregazioneEnabledProcessTerminate | boolean | Abilita/ disabilita l'aggregazione di eventi per gli eventi di terminazione del processoEnable / disable event aggregation for process terminate events | Solo Windows|
| aggregationIntervalProcessTerminate | Stringa timespan ISO8601 | Intervallo di aggregazione per gli eventi di terminazione processo | Solo Windows|
|

## <a name="default-configurations-settings"></a>Impostazioni di configurazione predefinite

| Nome configurazione | Valori predefiniti |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregazioneEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi il Centro sicurezza di Azure per l'aggregazione degli agenti di sicurezza IoT e sulle opzioni di configurazione degli eventi disponibili.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione IoT, usare gli articoli seguenti:To continue getting started with Azure Security Center for IoT deployment, use the following articles:

- Comprendere i metodi di [autenticazione dell'agente di sicurezzaUnderstand Security agent authentication methods](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente di sicurezza](how-to-deploy-agent.md)
- Esaminare il Centro sicurezza di Azure per [i prerequisiti](service-prerequisites.md) del servizio IoTReview Azure Security Center for IoT service prerequisites
- Informazioni su come abilitare il Centro sicurezza di [Azure per il servizio IoT nell'hub IoT](quickstart-onboard-iot-hub.md)
- Altre informazioni sul servizio dal Centro sicurezza di [Azure per IoT](resources-frequently-asked-questions.md)
