---
title: Piattaforma dati di Monitor di Azure - Documenti Microsoft
description: Il monitoraggio dei dati raccolti da Monitoraggio di Azure è separato tra metriche leggere che sono in grado di supportare scenari praticamente in tempo reale e log che vengono usati per l'analisi avanzata.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457264"
---
# <a name="azure-monitor-data-platform"></a>Piattaforma dati di Monitoraggio di AzureAzure Monitor data platform

Per consentire l'osservabilità negli ambienti di elaborazione complessi di oggi che eseguono applicazioni distribuite che si basano su servizi cloud e locali, è necessaria la raccolta di dati operativi da ogni livello e da ogni componente del sistema distribuito. È necessario essere in grado di eseguire informazioni approfondite su questi dati e consolidarli in un unico riquadro di vetro con prospettive diverse per supportare la moltitudine di parti interessate nell'organizzazione.

[Monitoraggio](../overview.md) di Azure raccoglie e aggrega i dati da un'ampia gamma di origini in una piattaforma dati comune in cui possono essere usati per l'analisi, la visualizzazione e gli avvisi. Fornisce un'esperienza coerente in cima ai dati provenienti da più origini, che offre informazioni dettagliate su tutte le risorse monitorate e anche con i dati di altri servizi che archiviano i dati in Monitoraggio di Azure.It provides a consistent experience on top of data from multiple sources, which gives you deep insights across all your monitored resources and even with data from other services that store their data in Azure Monitor.


![Panoramica di Monitoraggio di Azure](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dati di osservabilità in Monitoraggio di AzureObservability data in Azure Monitor
Le metriche, i log e le tracce distribuite sono comunemente indicati come i tre pilastri dell'osservabilità. Questi sono i diversi tipi di dati che uno strumento di monitoraggio deve raccogliere e analizzare per fornire sufficiente osservabilità di un sistema monitorato. L'osservabilità può essere ottenuta correlando i dati da più pilastri e aggregando i dati nell'intero set di risorse monitorate. Poiché Monitoraggio di Azure archivia insieme i dati da più origini, i dati possono essere correlati e analizzati usando un set comune di strumenti. Correla inoltre i dati tra più sottoscrizioni e tenant di Azure, oltre a ospitare dati per altri servizi.

Le risorse di Azure generano una quantità significativa di dati di monitoraggio. Monitoraggio di Azure consolida questi dati insieme ai dati di monitoraggio da altre origini in una piattaforma Metrica o Log.Azure Monitor consolidates this data along with monitoring data from other sources into either a Metrics or Logs platform. Each is optimized for particular monitoring scenarios, and each supports different features in Azure Monitor. Funzionalità quali l'analisi dei dati, le visualizzazioni o gli avvisi richiedono di comprendere le differenze in modo da poter implementare lo scenario richiesto nel modo più efficiente ed economico. Le informazioni dettagliate in Monitoraggio di Azure, ad esempio [Application Insights](../app/app-insights-overview.md) o Monitoraggio di Azure per le [macchine virtuali,](../insights/vminsights-overview.md) dispongono di strumenti di analisi che consentono di concentrarsi sullo scenario di monitoraggio specifico senza dover comprendere le differenze tra i due tipi di dati. 


### <a name="metrics"></a>Metriche
Le [metriche](data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Vengono raccolti a intervalli regolari e identificati con un timestamp, un nome, un valore e una o più etichette di definizione. Le metriche possono essere aggregate utilizzando una varietà di algoritmi, rispetto ad altre metriche e analizzate per le tendenze nel tempo. 

Le metriche in Monitoraggio di Azure vengono archiviate in un database di serie temporali ottimizzato per l'analisi dei dati con timestamp. Questo rende le metriche particolarmente adatte per gli avvisi e il rilevamento rapido dei problemi. Possono indicare le prestazioni del sistema, ma in genere devono essere combinati con i registri per identificare la causa principale dei problemi.

Le metriche sono disponibili per l'analisi interattiva nel portale di Azure con [Azure Metrics Explorer.](../platform/metrics-getting-started.md) Possono essere aggiunti a un dashboard di [Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati e utilizzati per [gli avvisi](alerts-metric.md)in tempo quasi reale.

Altre informazioni sulle metriche di Monitoraggio di Azure, incluse le origini dei dati in [Metriche in Monitoraggio di Azure.Read](data-platform-metrics.md)more about Azure Monitor Metrics including their sources of data in Metrics in Azure Monitor .

### <a name="logs"></a>Log
[I registri](data-platform-logs.md) sono eventi che si sono verificati all'interno del sistema. Possono contenere diversi tipi di dati e possono essere strutturati o testo in formato libero con un timestamp. Possono essere creati sporadicamente come gli eventi nell'ambiente generano voci di log e un sistema con carico elevato in genere genera più volume di registro.

I log in Monitoraggio di Azure vengono archiviati in un'area di lavoro di Log Analytics basata su [Azure Data Explorer,](/azure/data-explorer/) che offre un potente motore di analisi e un linguaggio di [query avanzato.](/azure/kusto/query/) I log forniscono in genere informazioni sufficienti per fornire il contesto completo del problema identificato e sono utili per identificare il caso radice dei problemi.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e le origini dei dati di log in Azure.It's important to distinguish between Azure Monitor Logs and sources of log data in Azure. Ad esempio, gli eventi a livello di sottoscrizione in Azure vengono scritti in un log attività che è possibile visualizzare dal menu Monitoraggio di Azure.For example, subscription level events in Azure are written to an [activity log](platform-logs-overview.md) that you can view from the Azure Monitor menu. La maggior parte delle risorse scriverà le informazioni operative in un [log risorse](platform-logs-overview.md) che è possibile inoltrare a posizioni diverse. Registri di monitoraggio di Azure è una piattaforma di log che raccoglie i log attività e i log delle risorse insieme ad altri dati di monitoraggio per fornire un'analisi approfondita dell'intero set di risorse.


 È possibile usare le query di [log in](../log-query/log-query-overview.md) modo interattivo con [Log Analytics](../log-query/portals.md) nel portale di Azure o aggiungere i risultati a un dashboard di [Azure](../learn/tutorial-app-dashboards.md) per la visualizzazione in combinazione con altri dati. È inoltre possibile creare [avvisi di log](alerts-log.md) che attiveranno un avviso in base ai risultati di una query di pianificazione.

Per altre informazioni sui log di Monitoraggio di Azure, incluse le origini dei dati [in Logs in Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Tracce distribuite
Le tracce sono serie di eventi correlati che seguono una richiesta utente tramite un sistema distribuito. Possono essere utilizzati per determinare il comportamento del codice dell'applicazione e le prestazioni delle diverse transazioni. Mentre i log vengono spesso creati da singoli componenti di un sistema distribuito, una traccia misura il funzionamento e le prestazioni dell'applicazione nell'intero set di componenti.

La traccia distribuita in Monitoraggio di Azure viene abilitata con [Application Insights SDK](../app/distributed-tracing.md)e i dati di traccia vengono archiviati con altri dati del log dell'applicazione raccolti da Application Insights. In questo modo è disponibile agli stessi strumenti di analisi degli altri dati di log, tra cui query di log, dashboard e avvisi.

Per ulteriori informazioni sull'analisi distribuita, [vedere Che cos'è la traccia distribuita?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Confronto tra metriche e log di Monitoraggio di AzureConfronto metriche e log di Monitoraggio di Azure

La tabella seguente confronta metriche e log in Monitoraggio di Azure.The following table compares Metrics and Logs in Azure Monitor.

| Attributo  | Metriche | Log |
|:---|:---|:---|
| Vantaggi | Leggero e capace di scenari quasi in tempo reale come gli avvisi. Ideale per il rilevamento rapido dei problemi. | Analizzato con linguaggio di query avanzato. Ideale per analisi approfondite e per identificare la causa principale. |
| Data | Solo valori numerici | Dati testuali o numerici |
| Struttura | Set standard di proprietà, tra cui il tempo di campionamento, la risorsa monitorata, un valore numerico. Alcune metriche includono più dimensioni per un'ulteriore definizione. | Set univoco di proprietà a seconda del tipo di log. |
| Raccolta | Raccolti a intervalli regolari. | Può essere raccolto sporadicamente come eventi innescano un record da creare. |
| Visualizzare nel portale di Azure | Esplora metriche | Log Analytics |
| Le origini dati includono | Metriche della piattaforma raccolte dalle risorse di Azure.Platform metrics collected from Azure resources.<br>Applicazioni monitorate da Application Insights.<br>Personalizzato definito dall'applicazione o dall'API. | Log dell'applicazione e delle risorse.<br>Soluzioni di monitoraggio.<br>Agenti ed estensioni della macchina virtuale.<br>Richieste ed eccezioni dell'applicazione.<br>Centro sicurezza di Azure.Azure Security Center.<br>API dell'agente di raccolta dati. |

## <a name="collect-monitoring-data"></a>Raccogliere i dati di monitoraggioCollect monitoring data
Origini di dati diverse [per Monitoraggio di Azure](data-sources.md) verranno scritte in un'area di lavoro di Log Analytics (Logs) o nel database delle metriche di Monitoraggio di Azure (metriche) o in entrambi. Alcune origini scriveranno direttamente in questi archivi dati, mentre altre potrebbero scrivere in un'altra posizione, ad esempio Archiviazione di Azure, e richiedere una configurazione per popolare log o metriche. 

Vedere [Metriche in Monitoraggio di Azure](data-platform-metrics.md) e Log in Monitoraggio di [Azure](data-platform-logs.md) per un elenco di origini dati diverse che popolano ogni tipo.


## <a name="stream-data-to-external-systems"></a>Trasmettere dati a sistemi esterni
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, potrebbe essere necessario inoltrarli a uno strumento esterno, ad esempio un prodotto di informazioni di sicurezza e gestione degli eventi. Questo inoltro viene in genere eseguito direttamente dalle risorse monitorate tramite [Hub eventi di Azure](/azure/event-hubs/). Alcune origini possono essere configurate per inviare i dati direttamente a un hub eventi, mentre è possibile usare un altro processo, ad esempio un'app per la logica per recuperare i dati necessari. Per informazioni dettagliate, vedere Trasmettere in streaming i dati di [monitoraggio di Azure in un hub eventi per l'utilizzo da parte di uno strumento esterno.](stream-monitoring-data-event-hubs.md)



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche in Monitoraggio di Azure](data-platform-metrics.md).Read more about Metrics in Azure Monitor .
- Altre informazioni sui [log in Monitoraggio di Azure](data-platform-logs.md).Read more about Logs in Azure Monitor .
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
