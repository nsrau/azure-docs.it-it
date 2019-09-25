---
title: Piattaforma dati di monitoraggio di Azure | Microsoft Docs
description: Il monitoraggio dei dati raccolti da Monitoraggio di Azure è separato tra metriche leggere che sono in grado di supportare scenari praticamente in tempo reale e log che vengono usati per l'analisi avanzata.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 48357adccea201aaeb99863b39e9c8cabce915ce
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262059"
---
# <a name="azure-monitor-data-platform"></a>Piattaforma dati di monitoraggio di Azure

L'abilitazione dell'osservabilità negli ambienti di elaborazione complessi odierni che eseguono applicazioni distribuite che si basano su servizi cloud e locali, richiede la raccolta di dati operativi da ogni livello e ogni componente del sistema distribuito. È necessario essere in grado di eseguire informazioni approfondite su questi dati e consolidarli in un unico riquadro di vetro con prospettive diverse per supportare la moltitudine di stakeholder nell'organizzazione.

[Monitoraggio di Azure](../overview.md) raccoglie e aggrega i dati da un'ampia gamma di origini in una piattaforma dati comune in cui possono essere usati per l'analisi, la visualizzazione e gli avvisi. Offre un'esperienza coerente sui dati provenienti da più origini, che offre informazioni approfondite su tutte le risorse monitorate e anche sui dati di altri servizi che archiviano i dati in monitoraggio di Azure.


![Panoramica di Monitoraggio di Azure](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dati di osservabilità in monitoraggio di Azure
Le metriche, i log e le tracce distribuite sono comunemente definiti tre pilastri di osservabilità. Questi sono i diversi tipi di dati che uno strumento di monitoraggio deve raccogliere e analizzare per offrire una sufficiente osservabilità di un sistema monitorato. L'osservabilità può essere eseguita correlando i dati di più pilastri e aggregando i dati nell'intero set di risorse monitorate. Poiché monitoraggio di Azure archivia i dati da più origini insieme, i dati possono essere correlati e analizzati usando un set comune di strumenti. Inoltre, mette in correlazione i dati tra più sottoscrizioni e tenant di Azure, oltre a ospitare i dati di altri servizi.

Le risorse di Azure generano una quantità significativa di dati di monitoraggio. Monitoraggio di Azure consolida questi dati insieme ai dati di monitoraggio di altre origini in una piattaforma di metriche o log. Ogni è ottimizzato per scenari di monitoraggio specifici e ognuno supporta funzionalità diverse in monitoraggio di Azure. Le funzionalità, ad esempio l'analisi dei dati, le visualizzazioni o gli avvisi, richiedono la comprensione delle differenze, in modo da poter implementare lo scenario necessario nel modo più efficiente ed economicamente conveniente. Le informazioni dettagliate in monitoraggio di Azure, ad esempio [Application Insights](../app/app-insights-overview.md) o [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) , includono strumenti di analisi che consentono di concentrarsi sullo scenario di monitoraggio specifico senza dover comprendere le differenze tra i due tipi di dati. 


### <a name="metrics"></a>metrics
Le [metriche](data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Vengono raccolti a intervalli regolari e vengono identificati con un timestamp, un nome, un valore e una o più etichette di definizione. Le metriche possono essere aggregate usando un'ampia gamma di algoritmi, rispetto ad altre metriche e analizzate per le tendenze nel tempo. 

Le metriche in monitoraggio di Azure vengono archiviate in un database di serie temporali ottimizzato per l'analisi dei dati con timestamp. In questo modo le metriche sono particolarmente adatte per gli avvisi e il rilevamento rapido dei problemi. È possibile indicare le prestazioni del sistema, ma in genere devono essere combinate con i log per identificare la causa principale dei problemi.

Le metriche sono disponibili per l'analisi interattiva nel portale di Azure con [Esplora metriche](../app/metrics-explorer.md). Possono essere aggiunti a un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati e usati per gli [avvisi](alerts-metric.md)in tempo quasi reale.

Scopri di più sulle metriche di monitoraggio di Azure, incluse le origini dei dati in [metriche in monitoraggio di Azure](data-platform-metrics.md).

### <a name="logs"></a>Log
I [log](data-platform-logs.md) sono eventi che si sono verificati nel sistema. Possono contenere tipi di dati diversi e possono essere strutturati o in formato libero con un timestamp. Possono essere creati sporadicamente mentre gli eventi nell'ambiente generano voci di log e un sistema con carico elevato genererà in genere un numero maggiore di volumi di log.

I log in monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics basata su [azure Esplora dati](/azure/data-explorer/) , che fornisce un potente motore di analisi e un [linguaggio di query avanzato](/azure/kusto/query/). I log forniscono in genere informazioni sufficienti per fornire il contesto completo del problema identificato e sono utili per identificare i casi principali dei problemi.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e le origini dei dati di log in Azure. Gli eventi a livello di sottoscrizione in Azure, ad esempio, vengono scritti in un [log attività](activity-logs-overview.md) che è possibile visualizzare dal menu monitoraggio di Azure. La maggior parte delle risorse scriverà le informazioni operative in un [log di diagnostica](resource-logs-overview.md) che è possibile trasmettere in posizioni diverse. Log di monitoraggio di Azure è una piattaforma di dati di log che raccoglie log attività e log di diagnostica insieme ad altri dati di monitoraggio per offrire un'analisi approfondita nell'intero set di risorse.


 È possibile usare le [query di log](../log-query/log-query-overview.md) in modo interattivo con [log Analytics](../log-query/portals.md) nel portale di Azure o aggiungere i risultati a un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati. È inoltre possibile creare [avvisi del log](alerts-log.md) che attiveranno un avviso in base ai risultati di una query di pianificazione.

Altre informazioni sui log di monitoraggio di Azure, incluse le origini dei dati nei [log in monitoraggio di Azure](data-platform-logs.md).

### <a name="distributed-traces"></a>Tracce distribuite
Le tracce sono serie di eventi correlati che seguono una richiesta dell'utente tramite un sistema distribuito. Possono essere usati per determinare il comportamento del codice dell'applicazione e le prestazioni di transazioni diverse. Sebbene i log vengano spesso creati dai singoli componenti di un sistema distribuito, una traccia misura il funzionamento e le prestazioni dell'applicazione nell'intero set di componenti.

La traccia distribuita in monitoraggio di Azure è abilitata con l' [SDK Application Insights](../app/distributed-tracing.md)e i dati di traccia vengono archiviati con altri dati del registro applicazioni raccolti da Application Insights. Questo lo rende disponibile per gli stessi strumenti di analisi degli altri dati di log, tra cui query, dashboard e avvisi del log.

Per altre informazioni sull'analisi distribuita, vedere [che cos'è la traccia distribuita?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Confrontare le metriche e i log di monitoraggio di Azure

La tabella seguente confronta le metriche e i log in monitoraggio di Azure.

| Attributo  | metrics | Log |
|:---|:---|:---|
| Vantaggi | Scenari semplici e in grado di essere in tempo quasi reale, ad esempio gli avvisi. Ideale per il rilevamento rapido dei problemi. | Analizzato con il linguaggio di query avanzato. Ideale per l'analisi approfondita e l'identificazione della causa radice. |
| Data | Solo valori numerici | Dati di testo o numerici |
| Struttura | Set standard di proprietà, tra cui l'ora di esempio, la risorsa monitorata e un valore numerico. Alcune metriche includono più dimensioni per un'ulteriore definizione. | Set univoco di proprietà a seconda del tipo di log. |
| Collection | Raccolta a intervalli regolari. | Può essere raccolto sporadicamente poiché gli eventi attivano un record da creare. |
| Visualizza in portale di Azure | Esplora metriche | Log Analytics |
| Le origini dati includono | Metriche della piattaforma raccolte dalle risorse di Azure.<br>Applicazioni monitorate da Application Insights.<br>Personalizzato definito dall'applicazione o dall'API. | Log dell'applicazione e di diagnostica.<br>Soluzioni di monitoraggio.<br>Agenti ed estensioni VM.<br>Richieste ed eccezioni dell'applicazione.<br>Centro sicurezza di Azure.<br>API dell'agente di raccolta dati. |

## <a name="collect-monitoring-data"></a>Raccogli dati di monitoraggio
[Origini dati diverse per monitoraggio di Azure](data-sources.md) scriveranno in un'area di lavoro log Analytics (log) o nel database di metriche di monitoraggio di Azure (metriche) o in entrambi i casi. Alcune origini scrivono direttamente in questi archivi dati, mentre altre possono scrivere in un'altra posizione, ad esempio archiviazione di Azure, e richiedono alcune configurazioni per popolare i log o le metriche. 

Vedere [metriche in monitoraggio di Azure](data-platform-metrics.md) e [log in monitoraggio di Azure](data-platform-logs.md) per un elenco di origini dati diverse che popolano ogni tipo.


## <a name="stream-data-to-external-systems"></a>Trasmettere dati a sistemi esterni
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, potrebbe essere necessario inoltrarli a uno strumento esterno, ad esempio un prodotto di informazioni di sicurezza e gestione degli eventi. Questo inoltro viene in genere eseguito direttamente dalle risorse monitorate tramite [Hub eventi di Azure](/azure/event-hubs/). Alcune origini possono essere configurate per inviare dati direttamente a un hub eventi, mentre è possibile usare un altro processo, ad esempio un'app per la logica, per recuperare i dati necessari. Per informazioni dettagliate, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi per l'utilizzo da uno strumento esterno](stream-monitoring-data-event-hubs.md) .



## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulle [metriche in monitoraggio di Azure](data-platform-metrics.md).
- Scopri di più sui [log in monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
