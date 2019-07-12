---
title: Piattaforma dati di monitoraggio Azure | Microsoft Docs
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
ms.openlocfilehash: 00c0fea9d8ca7ee299a9a19473917eba90edd675
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606982"
---
# <a name="azure-monitor-data-platform"></a>Piattaforma dati di monitoraggio Azure

Abilitare l'osservabilità in complessi ambienti di elaborazione attuali in esecuzione distribuite le applicazioni basate sul cloud e servizi in locale, è necessario raccolta dei dati operativi da ogni livello e ogni componente del sistema distribuito. È necessario essere in grado di eseguire approfondite in merito ai dati e viene possibile consolidare in un singolo riquadro con diverse prospettive per supportare la grande varietà delle parti interessate all'interno dell'organizzazione.

[Monitoraggio di Azure](../overview.md) raccoglie e aggrega i dati da diverse origini in una piattaforma dati comune in cui può essere utilizzato per l'analisi, visualizzazione e avvisi. Fornisce un'esperienza coerente all'inizio dei dati da più origini, che fornisce informazioni molto dettagliate in tutte le risorse monitorate e anche con i dati da altri servizi che archiviano i dati in Monitoraggio di Azure.


![Panoramica di Monitoraggio di Azure](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Data l'osservabilità in Monitoraggio di Azure
Le metriche, log e tracce distribuite sono comunemente detto a tre i pilastri dell'osservabilità. Questi sono i diversi tipi di dati di uno strumento di monitoraggio deve raccogliere e analizzare per fornire l'osservabilità sufficiente di un sistema monitorato. L'osservabilità può essere ottenuta tramite correlazione dei dati da più aree e l'aggregazione dei dati nell'intero set di risorse da monitorare. Dal momento che monitoraggio di Azure archivia i dati da più origini insieme, i dati possono essere correlati e analizzati usando un set comune di strumenti. Correla inoltre i dati tra più sottoscrizioni di Azure e i tenant, oltre a ospitare i dati per altri servizi.

Le risorse di Azure generano una quantità significativa di dati di monitoraggio. Monitoraggio di Azure consente di consolidare i dati insieme ai dati da altre origini di monitoraggio in uno una piattaforma di metriche o log. Ognuno è ottimizzato per scenari di monitoraggio specifici, e ognuno supporta diverse funzionalità in Monitoraggio di Azure. Funzionalità quali l'analisi dei dati, visualizzazioni o gli avvisi è necessario comprendere le differenze in modo che è possibile implementare lo scenario richiesto in modo più efficiente e conveniente. Insights in Monitoraggio di Azure, ad esempio [Application Insights](../app/app-insights-overview.md) oppure [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) dispone di strumenti di analisi che consentono di concentrarsi sullo scenario di monitoraggio specifico senza dover conoscere il differenze tra i due tipi di dati. 


### <a name="metrics"></a>metrics
Le [metriche](data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Vengono raccolti a intervalli regolari e vengono identificati con un timestamp, un nome, un valore e una o più etichette definizione. Le metriche possono essere aggregate usando un'ampia gamma di algoritmi, rispetto alle altre metriche e analizzare le tendenze nel tempo. 

Le metriche in Monitoraggio di Azure vengono archiviate in un database di serie temporali che è ottimizzato per l'analisi dei dati con data / ora. In questo modo le metriche particolarmente adatto per gli avvisi e veloce rilevamento dei problemi. Essi possono indicano le prestazioni di sistema ma in genere devono essere combinati con i log per identificare la causa radice dei problemi.

Sono disponibili per l'analisi interattiva nel portale di Azure con le metriche [Esplora metriche](../app/metrics-explorer.md). Possono essere aggiunti a un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati e usati per quasi in tempo reale [avvisi](alerts-metric.md).

Altre informazioni sulle metriche di monitoraggio di Azure tra cui le relative origini dei dati in [le metriche in Monitoraggio di Azure](data-platform-metrics.md).

### <a name="logs"></a>Log
[I log](data-platform-logs.md) sono eventi che si sono verificati all'interno del sistema. Si possono contenere diversi tipi di dati e possono essere strutturati o testo con un timestamp in formato libera. E possono essere creati sporadicamente come gli eventi nell'ambiente di generano le voci di log e un sistema con un carico pesante di solito viene generato più volumi di log.

I log in Monitoraggio di Azure vengono archiviati in un'area di lavoro di Log Analitica basato sul [Esplora dati di Azure](/azure/data-explorer/) che fornisce un motore di analisi avanzate e [linguaggio di query avanzato](/azure/kusto/query/). In genere, i log forniscono informazioni sufficienti per fornire il contesto completo del problema identificato e sono utili per identificare la causa principale dei problemi.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e origini dei dati di log in Azure. Ad esempio, in cui vengono scritti gli eventi a livello di sottoscrizione di Azure a un [log attività](activity-logs-overview.md) che è possibile visualizzare dal menu di monitoraggio di Azure. La maggior parte delle risorse scriverà le informazioni operative per un [log di diagnostica](diagnostic-logs-overview.md) che è possibile inoltrare a posizioni diverse. Log di monitoraggio di Azure è una piattaforma di dati di log che raccoglie i log attività e nei log di diagnostica con altri dati di monitoraggio per fornire un'analisi approfondita in tutto il set di risorse.


 È possibile rivolgersi [query di log](../log-query/log-query-overview.md) in modo interattivo con [Log Analitica](../log-query/portals.md) nel portale di Azure oppure aggiungere i risultati in un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati. È anche possibile creare [gli avvisi del log](alerts-log.md) che attiverà un avviso in base ai risultati di una query di pianificazione.

Altre informazioni sul log di monitoraggio di Azure tra cui le relative origini dei dati in [log in Monitoraggio di Azure](data-platform-logs.md).

### <a name="distributed-traces"></a>Tracce distribuite
Le tracce sono serie di eventi correlati che seguono una richiesta dell'utente tramite un sistema distribuito. Possono essere utilizzati per determinare il comportamento del codice dell'applicazione e le prestazioni delle transazioni diverse. Mentre i log, verranno spesso creati da singoli componenti di un sistema distribuito, una traccia misura l'operazione e le prestazioni dell'applicazione sull'intero set di componenti.

Analisi distribuita in Monitoraggio di Azure sono abilitata con la [Application Insights SDK](../app/distributed-tracing.md), e i dati di traccia viene archiviati con altri dati di log dell'applicazione raccolti da Application Insights. Questo rende disponibili per gli stessi strumenti di analisi come altri dati di log tra cui le query di log, i dashboard e avvisi.

Altre informazioni su analisi in distribuita [che cos'è analisi distribuita?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Confrontare i log e metriche di monitoraggio di Azure

La tabella seguente confronta le metriche e log in Monitoraggio di Azure.

| Attributo  | metrics | Log |
|:---|:---|:---|
| Vantaggi | Caricamento leggero e in grado di supportare scenari di quasi in tempo reale, ad esempio gli avvisi. Ideale per il rilevamento rapido dei problemi. | Analizzati con avanzato linguaggio di query. Ideale per un'analisi approfondita e identificazione di causa principale. |
| Data | Solo i valori numerici | Dati numerici o di testo |
| Struttura | Set standard di proprietà, tra cui tempo di campionamento, risorsa che si sta controllando, un valore numerico. Alcune metriche includono più dimensioni per un'ulteriore definizione. | Set di proprietà in base al tipo di log univoco. |
| Collection | Raccolti a intervalli regolari. | Può essere raccolto sporadicamente come eventi che attivano un record da creare. |
| Consente di visualizzare nel portale di Azure | Esplora metriche | Log Analytics |
| Le origini dati includono | Piattaforma metriche raccolte dalle risorse di Azure.<br>Applicazioni monitorate da Application Insights.<br>Definito dall'applicazione o l'API personalizzata. | Applicazione e i log di diagnostica.<br>Monitoraggio delle soluzioni.<br>Gli agenti e le estensioni di VM.<br>Le richieste dell'applicazione e le eccezioni.<br>Centro sicurezza di Azure.<br>API di raccolta dati. |

## <a name="collect-monitoring-data"></a>Raccogliere i dati di monitoraggio
Diversi [origini dei dati per monitoraggio di Azure](data-sources.md) verranno scritti in un'area di lavoro di Analitica di Log (log) o il database di metriche di monitoraggio di Azure (metriche) o entrambi. Alcune origini scriverà direttamente a questi archivi dati, mentre altri possono scrivere in un altro percorso, ad esempio archiviazione di Azure e richiede alcune operazioni di configurazione per popolare i log o metriche. 

Visualizzare [le metriche in Monitoraggio di Azure](data-platform-metrics.md) e [log in Monitoraggio di Azure](data-platform-logs.md) per un elenco di origini dati diverse che consentono di popolare ogni tipo.


## <a name="stream-data-to-external-systems"></a>Trasmettere dati a sistemi esterni
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, potrebbe essere necessario inoltrarli a uno strumento esterno, ad esempio un prodotto di informazioni di sicurezza e gestione degli eventi. Questo inoltro viene in genere eseguito direttamente dalle risorse monitorate tramite [Hub eventi di Azure](/azure/event-hubs/). Alcune origini possono essere configurati per inviare dati direttamente a un hub eventi, sebbene sia possibile usare un altro processo, ad esempio un'App per la logica per recuperare i dati necessari. Visualizzare [Stream monitoraggio di Azure i dati a un hub eventi per l'uso da uno strumento esterno](stream-monitoring-data-event-hubs.md) per informazioni dettagliate.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni, vedere [le metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Altre informazioni, vedere [log in Monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
