---
title: Procedura dettagliata per la manutenzione predittiva | Documentazione Microsoft
description: Una procedura dettagliata della soluzione preconfigurata di manutenzione predittiva di Azure IoT.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9b2947d9ce00083c168635811395bc86b3e60b78
ms.contentlocale: it-it
ms.lasthandoff: 04/26/2017


---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procedura dettagliata della soluzione preconfigurata di manutenzione predittiva

## <a name="introduction"></a>Introduzione

La soluzione preconfigurata di manutenzione predittiva di IoT Suite è una soluzione end-to-end per uno scenario aziendale che consente di stimare il punto in cui è probabile che si verifichino errori. È possibile usare questa soluzione preconfigurata in modo proattivo per attività come l'ottimizzazione della manutenzione. La soluzione combina i servizi chiave di Azure IoT Suite, ad esempio Hub IoT, Analisi di flusso e un'area di lavoro di [Azure Machine Learning][lnk-machine-learning]. L'area di lavoro contiene un modello, basato su un set di dati di esempio pubblico, per stimare la vita utile rimanente del motore di un velivolo. La soluzione implementa completamente lo scenario aziendale IoT come punto di partenza per poter pianificare e implementare una soluzione che soddisfi i propri requisiti aziendali.

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![][img-architecture]

Gli elementi blu sono servizi di Azure di cui viene effettuato il provisioning nell'area selezionata durante il provisioning della soluzione preconfigurata. L'elenco di aree in cui è possibile distribuire la soluzione preconfigurata viene visualizzato nella [pagina di provisioning][lnk-azureiotsuite].

L'elemento verde è un dispositivo simulato che rappresenta un motore di aereo. Altre informazioni su questi dispositivi simulati sono disponibili nella sezione seguente.

Gli elementi grigi rappresentano i componenti che implementano le funzionalità di *gestione del dispositivo*. La versione corrente della soluzione preconfigurata di manutenzione predittiva non effettua il provisioning di queste risorse. Per altre informazioni sulla gestione del dispositivo, vedere la [soluzione preconfigurata per il monitoraggio remoto][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivi simulati

Nella soluzione preconfigurata un dispositivo simulato rappresenta un motore di aereo. Il provisioning della soluzione viene effettuato con due motori associati a un singolo aereo. Ogni motore genera quattro tipi di dati di telemetria: il sensore 9, il sensore 11, il sensore 14 e il sensore 15 forniscono i dati necessari per il modello di Machine Learning che calcola la vita utile rimanente per il motore. Ogni dispositivo simulato invia i messaggi di telemetria seguenti all'hub IoT:

*Conteggio dei cicli*. Un ciclo rappresenta un volo completato con una durata compresa tra due e dieci ore. Durante il volo, i dati di telemetria vengono acquisiti ogni mezz'ora.

*Telemetria*. Sono presenti quattro sensori che rappresentano gli attributi del motore. I sensori sono indicati genericamente con l'etichetta Sensore 9, Sensore 11, Sensore 14 e Sensore 15. Questi quattro sensori rappresentano dati di telemetria sufficienti per ottenere risultati importanti dal modello di vita utile rimanente. Il modello usato nella soluzione preconfigurata viene creato da un set di dati pubblico che include i dati reali dei sensori del motore. Per altre informazioni sulla modalità di creazione del modello dal set di dati originale, vedere il [modello di manutenzione predittiva in Cortana Intelligence Gallery][lnk-cortana-analytics].

I dispositivi simulati possono gestire i comandi seguenti inviati dall'hub IoT nella soluzione:

| Comando | Descrizione |
| --- | --- |
| StartTelemetry |Controlla lo stato della simulazione.<br/>Avvia il dispositivo che invia i dati di telemetria |
| StopTelemetry |Controlla lo stato della simulazione.<br/>Arresta il dispositivo che invia i dati di telemetria |

L'hub IoT fornisce il riconoscimento dei comandi del dispositivo.

## <a name="azure-stream-analytics-job"></a>Processo di Analisi di flusso di Azure
**Processo: Telemetria** agisce sul flusso di dati di telemetria in ingresso dai dispositivi con due istruzioni. La prima seleziona tutti i dati di telemetria dai dispositivi e li invia all'archivio BLOB da dove vengono visualizzati nell'app Web. La seconda istruzione calcola i valori medi dei sensori in una finestra temporale scorrevole di due minuti e invia i dati tramite l'Hub eventi a un **processore di eventi**.

## <a name="event-processor"></a>Processore di eventi
L'**host processore di eventi** viene eseguito in un processo Web di Azure. Il **processore di eventi** considera i valori medi dei sensori per un ciclo completato e quindi li passa a un'API che espone il modello con training per calcolare la vita utile rimanente di un motore. L'API viene esposta da un'area di lavoro di Machine Learning di cui viene effettuato il provisioning nell'ambito della soluzione.

## <a name="machine-learning"></a>Machine Learning
Il componente Machine Learning usa un modello derivato dai dati raccolti da veri motori di aerei. È possibile passare all'area di lavoro di Machine Learning dal riquadro nella pagina [azureiotsuite.com][lnk-azureiotsuite] per la soluzione di cui è stato effettuato il provisioning quando il relativo stato è **Pronto**.


## <a name="next-steps"></a>Passaggi successivi
Dopo avere esaminato i componenti chiave della soluzione preconfigurata di manutenzione predittiva, è possibile personalizzarli. Vedere [Guida alla personalizzazione di soluzioni preconfigurate][lnk-customize].

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Domande frequenti su IoT Suite][lnk-faq]
* [Sicurezza IoT sin dall'inizio][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
