---
title: Piattaforma dati Monitoraggio di Azure | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457264"
---
# <a name="azure-monitor-data-platform"></a>Piattaforma dati Monitoraggio di Azure

Per abilitare l'osservabilità nei complessi ambienti di elaborazione odierni, su cui vengono eseguite applicazioni distribuite che si basano su servizi cloud e locali, è richiesta la raccolta di dati operativi da ogni livello e ogni componente del sistema distribuito. È necessario essere in grado di analizzare in modo approfondito questi dati e consolidarli in un unico riquadro con prospettive diverse a supporto dei moltissimi stakeholder presenti nell'organizzazione.

[Monitoraggio di Azure](../overview.md) raccoglie e aggrega i dati di diverse origini in una piattaforma dati comune, nella quale è possibile usare tali dati a scopo di analisi, visualizzazione e creazione di avvisi. Questo servizio offre un'esperienza coerente sui dati provenienti da più origini e mette a disposizione degli utenti informazioni cognitive dettagliate su tutte le risorse monitorate e persino sui dati di altri servizi che si servono di Monitoraggio di Azure per l'archiviazione.


![Panoramica di Monitoraggio di Azure](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dati di osservabilità in Monitoraggio di Azure
Le metriche, i log e le tracce distribuite sono comunemente definiti come i tre pilastri dell'osservabilità. Si tratta di diversi tipi di dati che gli strumenti di monitoraggio devono raccogliere e analizzare per fornire un livello di osservabilità sufficiente di un sistema monitorato. L'osservabilità può essere ottenuta mettendo in correlazione i dati di più pilastri e aggregandoli nell'intero set di risorse monitorate. Dal momento che Monitoraggio di Azure archivia i dati di più origini, questi ultimi possono essere correlati e analizzati tramite un set di strumenti comune. Inoltre, il servizio mette in correlazione i dati di più sottoscrizioni e tenant di Azure, oltre a ospitare i dati di altri servizi.

Le risorse di Azure generano una quantità significativa di dati di monitoraggio. Monitoraggio di Azure consolida questi dati con quelli di monitoraggio provenienti da altre origini in una piattaforma di metriche o log. Ognuna è ottimizzata per scenari di monitoraggio specifici e supporta funzionalità diverse in Monitoraggio di Azure. Per usare funzionalità come l'analisi dei dati, le visualizzazioni o la creazione di avvisi, è necessario conoscere le differenze per essere in grado di implementare lo scenario più adatto nel modo più efficiente ed economicamente conveniente. Le informazioni cognitive dettagliate in Monitoraggio di Azure, come [Application Insights](../app/app-insights-overview.md) o [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md), dispongono di strumenti di analisi che consentono agli utenti di concentrarsi su uno scenario di monitoraggio specifico senza la necessità di conoscere le differenze tra i due tipi di dati. 


### <a name="metrics"></a>Metriche
Le [metriche](data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Vengono raccolte a intervalli regolari e identificate con un timestamp, un nome, un valore e una o più etichette di definizione. Le metriche possono essere aggregate tramite un'ampia gamma di algoritmi, confrontate con altre metriche e analizzate per individuare i trend nel tempo. 

Le metriche in Monitoraggio di Azure sono archiviate in un database di serie temporale ottimizzato per l'analisi dei dati con timestamp. Ciò le rende particolarmente adatte alla creazione di avvisi e al rilevamento rapido dei problemi. Le metriche consentono di visualizzare le prestazioni del sistema, ma in genere devono essere combinate con i log per identificare la causa radice dei problemi.

Le metriche sono disponibili per l'analisi interattiva nel portale di Azure con [Esplora metriche di Azure](../platform/metrics-getting-started.md). È possibile aggiungerle a un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per visualizzarle in combinazione con altri dati e usarle per la [creazione di avvisi](alerts-metric.md) quasi in tempo reale.

Per altre informazioni sulle metriche di Monitoraggio di Azure, incluse le relative origini dei dati, vedere [Metriche in Monitoraggio di Azure](data-platform-metrics.md).

### <a name="logs"></a>Log
I [log](data-platform-logs.md) sono eventi che si sono verificati nel sistema. Possono contenere tipi diversi di dati ed essere strutturati o con testo in formato libero con un timestamp. È possibile crearli sporadicamente quando gli eventi nell'ambiente generano voci di log (tenere presente che un sistema con carico elevato genera solitamente un volume di log maggiore).

I log in Monitoraggio di Azure sono archiviati in un'area di lavoro Log Analytics basata su [Esplora dati di Azure](/azure/data-explorer/) che fornisce un potente motore di analisi e un [linguaggio di query avanzato](/azure/kusto/query/). I log contengono in genere informazioni sufficienti per comprendere il contesto generale del problema in fase di analisi e sono utili per identificare la causa radice dei problemi.

> [!NOTE]
> È importante distinguere tra i log di Monitoraggio di Azure e le origini dei dati di log in Azure. Ad esempio, gli eventi a livello di sottoscrizione in Azure vengono scritti in un [log attività](platform-logs-overview.md) che è possibile visualizzare dal menu Monitoraggio di Azure. La maggior parte delle risorse scrive informazioni operative in un [log delle risorse](platform-logs-overview.md) che è possibile inviare a destinazioni diverse. I log di Monitoraggio di Azure sono piattaforma di dati di log che raccoglie log attività e log delle risorse insieme ad altri dati di monitoraggio, per offrire analisi approfondite nell'intero set di risorse.


 È possibile usare [query di log](../log-query/log-query-overview.md) in modo interattivo con [Log Analytics](../log-query/portals.md) nel portale di Azure o aggiungere i risultati a un [dashboard di Azure](../learn/tutorial-app-dashboards.md) per visualizzarli in combinazione con altri dati. È anche possibile creare [avvisi relativi ai log](alerts-log.md) che verranno attivati in base ai risultati delle query di pianificazione.

Per altre informazioni sui log di Monitoraggio di Azure, incluse le relative origini dei dati, vedere [Log in Monitoraggio di Azure](data-platform-logs.md).

### <a name="distributed-traces"></a>Tracce distribuite
Le tracce sono serie di eventi correlati che seguono una richiesta dell'utente in un sistema distribuito. Possono essere usate per determinare il comportamento del codice dell'applicazione e le prestazioni di diverse transazioni. Mentre i log vengono di norma creati dai singoli componenti di un sistema distribuito, una traccia misura il funzionamento e le prestazioni dell'applicazione nell'intero set di componenti.

La traccia distribuita in Monitoraggio di Azure è abilitata con l'[SDK di Application Insights](../app/distributed-tracing.md) e i dati di traccia vengono archiviati con altri dati del registro applicazioni raccolti da Application Insights. Di conseguenza, la traccia distribuita è disponibile per gli stessi strumenti di analisi degli altri dati di log, tra cui query, dashboard e avvisi relativi ai log.

Per altre informazioni sulla traccia distribuita, vedere [Che cos'è la traccia distribuita?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Confronto tra le metriche e i log di Monitoraggio di Azure

La tabella seguente confronta le metriche e i log di Monitoraggio di Azure.

| Attributo  | Metriche | Log |
|:---|:---|:---|
| Vantaggi | Sono leggere e in grado di creare scenari in tempo quasi reale, come la creazione di avvisi. Sono ideali per il rilevamento rapido dei problemi. | Sono analizzate con il linguaggio di query avanzato. Sono ideali per l'analisi approfondita e l'identificazione della causa radice. |
| Data | Solo valori numerici | Dati numerici o di testo |
| Struttura | Set standard di proprietà tra cui l'intervallo di campionamento, la risorsa monitorata e un valore numerico. Alcune metriche includono più dimensioni per un'ulteriore definizione. | Set univoco di proprietà in base al tipo di log. |
| Raccolta | La raccolta avviene a intervalli regolari. | La raccolta può essere sporadica quando gli eventi attivano la creazione di un record. |
| Visualizzare nel portale di Azure | Esplora metriche | Log Analytics |
| Le origini dati includono | Metriche di piattaforma raccolte dalle risorse di Azure.<br>Applicazioni monitorate da Application Insights.<br>Definizioni personalizzate dall'applicazione o dall'API. | Log delle applicazioni e delle risorse.<br>Soluzioni di monitoraggio.<br>Agenti ed estensioni VM.<br>Richieste ed eccezioni dell'applicazione.<br>Centro sicurezza di Azure.<br>API dell'agente di raccolta dati. |

## <a name="collect-monitoring-data"></a>Raccogliere i dati di monitoraggio
Le diverse [origini dei dati di Monitoraggio di Azure](data-sources.md) scrivono in un'area di lavoro Log Analytics (log) o nel database delle metriche di Monitoraggio di Azure (metriche) o in entrambi. Alcune origini scrivono direttamente in questi archivi dati, mentre altre possono scrivere in un'altra posizione, come l'archiviazione di Azure, e richiedono alcune configurazioni per la compilazione dei log o delle metriche. 

Per un elenco delle diverse origini dati che compilano ogni tipo, vedere [Metriche in Monitoraggio di Azure](data-platform-metrics.md) e [Log in Monitoraggio di Azure](data-platform-logs.md).


## <a name="stream-data-to-external-systems"></a>Trasmettere dati a sistemi esterni
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, potrebbe essere necessario inoltrarli a uno strumento esterno, ad esempio un prodotto di informazioni di sicurezza e gestione degli eventi. Questo inoltro viene in genere eseguito direttamente dalle risorse monitorate tramite [Hub eventi di Azure](/azure/event-hubs/). È possibile configurare alcune origini sull'invio dei dati direttamente a un hub eventi, mentre è possibile usare un altro processo, ad esempio un'app per la logica, per recuperare i dati necessari. Per informazioni dettagliate, vedere [Trasmettere i dati di Monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](stream-monitoring-data-event-hubs.md).



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [Metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Altre informazioni sui [Log in Monitoraggio di Azure](data-platform-logs.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
