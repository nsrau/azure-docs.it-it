---
title: Informazioni sul centro sicurezza di Azure per l'aggregazione di eventi Internet | Microsoft Docs
description: Informazioni sul centro sicurezza di Azure per l'aggregazione di eventi.
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
ms.openlocfilehash: b1a14cf4c8aec2f3dbfa7bc4fd0800d9fd1fb0aa
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327308"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Centro sicurezza di Azure per l'aggregazione di eventi Internet

Il Centro sicurezza di Azure per gli agenti di sicurezza di Azure raccoglie i dati e gli eventi di sistema dal dispositivo locale e li invia al cloud di Azure per l'elaborazione e l'analisi. L'agente di sicurezza raccoglie molti tipi di eventi del dispositivo, tra cui nuovo processo e nuovi eventi di connessione. Sia i nuovi eventi di connessione che quelli nuovi possono verificarsi in modo legittimo in un dispositivo entro un secondo e, sebbene sia importante per una sicurezza affidabile e completa, il numero di messaggi che gli agenti di sicurezza sono costretti a inviare può raggiungere rapidamente o superare l'hub Internet delle cose quote e limiti di costo. Tuttavia, questi eventi contengono informazioni di sicurezza estremamente utili, cruciali per la protezione del dispositivo.

Per ridurre la quota e i costi aggiuntivi mantenendo protetti i dispositivi, il Centro sicurezza di Azure per gli agenti Internet aggrega questi tipi di eventi.

L'aggregazione di eventi è **attivata** per impostazione predefinita e, sebbene non consigliata, può essere **disattivata manualmente in qualsiasi** momento.

L'aggregazione è attualmente disponibile per i tipi di eventi seguenti:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (solo Windows)

## <a name="how-does-event-aggregation-work"></a>Funzionamento dell'aggregazione di eventi
Quando viene lasciata l' **aggregazione di eventi**, il Centro sicurezza di Azure per gli agenti degli eventi aggrega gli eventi per il periodo intervallo o l'intervallo di tempo.
Una volta trascorso il periodo di intervallo, l'agente invia gli eventi aggregati al cloud di Azure per un'ulteriore analisi.
Gli eventi aggregati vengono archiviati in memoria fino a quando non vengono inviati al cloud di Azure.

Per ridurre il footprint di memoria dell'agente, ogni volta che l'agente raccoglie un evento identico a uno già mantenuto in memoria, l'agente aumenta il numero di passaggi di questo evento specifico. Quando viene passato l'intervallo di tempo di aggregazione, l'agente invia il numero di passaggi di ogni tipo specifico di evento che si è verificato. L'aggregazione di eventi è semplicemente l'aggregazione dei conteggi dei passaggi di ogni tipo di evento raccolto.

Gli eventi sono considerati identici solo quando vengono soddisfatte le condizioni seguenti: 

* Eventi ProcessCreate-quando **CommandLine**, **Executable**, * * username e **userid** sono identici
* Eventi ConnectionCreate-quando **CommandLine**, **userid**, **Direction**, **Local Address**, **Remote Address**, * * Protocol e **Destination Port** sono identici
* Eventi ProcessTerminate-quando **lo stato** dell' **eseguibile** e dell'uscita è identico

### <a name="working-with-aggregated-events"></a>Utilizzo degli eventi aggregati

Durante l'aggregazione, le proprietà dell'evento non aggregate vengono scartate e visualizzate in log Analytics con un valore pari a 0. 
* Eventi ProcessCreate- **ProcessID**e **parentProcessId** sono impostati su 0
* Eventi ConnectionCreate- **ProcessID**e **porta di origine** impostati su 0

## <a name="event-aggregation-based-alerts"></a>Avvisi basati sulle aggregazioni di eventi 
Dopo l'analisi, il Centro sicurezza di Azure per le cose crea avvisi di sicurezza per gli eventi aggregati sospetti. Gli avvisi creati da eventi aggregati vengono visualizzati una sola volta per ogni evento aggregato.

L'ora di inizio dell'aggregazione, l'ora di fine e il numero di passaggi per ogni evento vengono registrati nel campo relativo ai **Dettagli** dell'evento all'interno log Analytics da usare durante le indagini. 

Ogni evento aggregato rappresenta un periodo di 24 ore di avvisi raccolti. Utilizzando il menu Opzioni evento nell'angolo superiore sinistro di ogni evento, è possibile **ignorare** ogni singolo evento aggregato.    

## <a name="event-aggregation-twin-configuration"></a>Configurazione dell'aggregazione di eventi gemelli
Apportare modifiche alla configurazione del Centro sicurezza di Azure per l'aggregazione di eventi Internet nell' [oggetto di configurazione dell'agente](how-to-agent-configuration.md) dell'identità del modulo gemello del modulo **azureiotsecurity** .

| Nome configurazione | Valori possibili | Dettagli | Note |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Abilita/Disabilita l'aggregazione di eventi per gli eventi di creazione del processo |
| aggregationIntervalProcessCreate | Stringa TimeSpan ISO8601 | Intervallo di aggregazione per gli eventi di creazione del processo |
| aggregationEnabledConnectionCreate | boolean| Abilita/Disabilita l'aggregazione di eventi per gli eventi di creazione della connessione |
| aggregationIntervalConnectionCreate | Stringa TimeSpan ISO8601 | Intervallo di aggregazione per gli eventi di creazione della connessione |
| aggregationEnabledProcessTerminate | boolean | Abilita/Disabilita l'aggregazione di eventi per eventi di terminazione processo | Solo Windows|
| aggregationIntervalProcessTerminate | Stringa TimeSpan ISO8601 | Intervallo di aggregazione per eventi di terminazione processo | Solo Windows|
|

## <a name="default-configurations-settings"></a>Impostazioni delle configurazioni predefinite

| Nome configurazione | Valori predefiniti |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come il Centro sicurezza di Azure per l'aggregazione degli agenti di sicurezza Internet e le opzioni di configurazione degli eventi disponibili.

Per continuare a usare il Centro sicurezza di Azure per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Informazioni sui [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente di sicurezza](how-to-deploy-agent.md)
- Esaminare il Centro sicurezza di Azure per i [prerequisiti del servizio](service-prerequisites.md) Internet
- Informazioni su come [abilitare il Centro sicurezza di Azure per il servizio](quickstart-onboard-iot-hub.md) Internet delle cose nell'hub Internet delle cose
- Per altre informazioni sul servizio, vedere [domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)
