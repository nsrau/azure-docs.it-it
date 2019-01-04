---
title: Monitoraggio dei dati raccolti da Monitoraggio di Azure | Microsoft Docs
description: Il monitoraggio dei dati raccolti da Monitoraggio di Azure è separato tra metriche leggere che sono in grado di supportare scenari praticamente in tempo reale e log che vengono usati per l'analisi avanzata.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: 84ab63d145d9726fad83b7b2337542fef5c8743d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718963"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Monitoraggio dei dati raccolti da Monitoraggio di Azure
[Monitoraggio di Azure](../overview.md) è un servizio che permette di monitorare le applicazioni e le risorse su cui si basano. Al centro di questa funzione vi è l'archiviazione dei dati di telemetria e di altro tipo dalle risorse monitorate. Questo articolo presenta una descrizione completa di come questi dati vengono archiviati e usati da Monitoraggio di Azure.

Tutti i dati raccolti da Monitoraggio di Azure appartengono a uno di due tipi essenziali, ovvero le [metriche](#metrics) e i [log](#logs). Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Sono elementi leggeri in grado di supportare scenari praticamente in tempo reale. I log contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. I dati di telemetria, come eventi e tracce, vengono archiviati come log insieme ai dati sulle prestazioni in modo da poter essere combinati per l'analisi.

![Panoramica di Monitoraggio di Azure](media/data-collection/overview.png)

## <a name="metrics"></a>Metriche
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento, Sono elementi leggeri in grado di supportare scenari praticamente in tempo reale. Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. 

È ad esempio possibile raccogliere i dati relativi all'uso del processore da una macchina virtuale ogni minuto o il numero di utenti connessi all'applicazione ogni 10 minuti. È possibile attivare un avviso quando uno dei valori raccolti o anche la differenza tra due valori supera una soglia definita.

Le metriche in Azure hanno le caratteristiche specifiche seguenti:

* Vengono raccolte a una frequenza di un minuto, se non specificato diversamente nella definizione della metrica.
* Sono identificate in modo univoco da un nome di metrica e uno spazio dei nomi che funge da categoria.
* Vengono archiviate per 93 giorni. È possibile copiare metriche nei log per l'identificazione delle tendenze a lungo termine.

Ogni valore di metrica ha le proprietà seguenti:
* Ora in cui è stato raccolto il valore.
* Tipo di misurazione rappresentata dal valore.
* Risorsa cui è associato il valore.
* Valore stesso.
* Alcune metriche possono avere più dimensioni, come descritto nella prossima sezione. Le metriche personalizzate possono avere fino a 10 dimensioni.

### <a name="multi-dimensional-metrics"></a>Metriche multidimensionali
Le dimensioni di una metrica sono coppie nome/valore contenenti dati aggiuntivi per descrivere il valore della metrica. Ad esempio, la metrica _Spazio su disco disponibile_ può avere una dimensione denominata _Unità_ con i valori _C:_ e _D:_, che permettono la visualizzazione dello spazio su disco disponibile in tutte le unità o per ogni unità singolarmente. 

L'esempio seguente mostra due set di dati per un'ipotetica metrica denominata _Velocità effettiva di rete_. Il primo set di dati non ha dimensioni. Il secondo set di dati mostra i valori con due dimensioni, Indirizzo IP e _Direzione_:

### <a name="network-throughput"></a>Velocità effettiva della rete

 |Timestamp        | Valore della metrica | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1.331,8 Kbps | 
   | 8/9/2017 8:15 | 1.141,4 Kbps |
   | 8/9/2017 8:16 | 1.110,2 Kbps |

Questa metrica non dimensionale può solo rispondere a una domanda di base come "Qual è la velocità effettiva della rete in un determinato momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Velocità effettiva della rete + due dimensioni ("IP" e "Direzione")

| Timestamp          | Dimensione "IP" | Dimensione "Direzione" | Valore della metrica| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Invio"    | 646,5 Kbps |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direzione = "Ricezione" | 420,1 Kbps |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Invio"    | 150,0 Kbps | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 115,2 Kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Invio"    | 515,2 Kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direzione = "Ricezione" | 371,1 Kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Invio"    | 155,0 Kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direzione = "Ricezione" | 100,1 Kbps |

Questa metrica può rispondere a domande del tipo "Qual è la velocità effettiva di rete per ogni indirizzo IP?" e "Quanti dati sono stati inviati rispetto a quelli ricevuti?" Le metriche multidimensionali presentano valori analitici e diagnostici aggiuntivi rispetto alle metriche dimensionali.

### <a name="value-of-metrics"></a>Valore delle metriche
Ogni metrica, se considerata singolarmente, non offre informazioni particolarmente dettagliate. Specifica un unico valore senza altro contesto che il confronto con una soglia semplice. Sono preziose se combinate con altre metriche per identificare schemi e tendenze o se combinate con log che forniscono contesto per determinati valori. 

Ad esempio, un certo numero di utenti connessi all'applicazione in un determinato momento potrebbe non dire molto sull'integrità dell'applicazione, mentre un improvviso calo nel numero degli utenti, indicato da più valori della stessa metrica, potrebbe indicare un problema. Un numero eccessivo di eccezioni generate dall'applicazione, e indicato da una metrica separata, potrebbe identificare un problema dell'applicazione che causa il calo. Gli eventi che l'applicazione crea per identificare gli errori dei componenti possono essere utili ai fini dell'identificazione della causa principale.

### <a name="sources-of-metric-data"></a>Origini dei dati delle metriche
Sono disponibili tre origini principali delle metriche raccolte da Monitoraggio di Azure. Tutte queste metriche sono disponibili nell'archivio delle metriche, in cui possono essere valutate insieme indipendentemente dalla rispettiva origine.

Le **metriche della piattaforma** vengono create da risorse di Azure e offrono visibilità riguardo a integrità e prestazioni. Ogni tipo di risorsa crea un [set distinto di metriche](../../azure-monitor/platform/metrics-supported.md) senza che sia necessaria alcuna configurazione. 

Le **metriche dell'applicazione** vengono create da Application Insights per le applicazioni monitorate e permettono di rilevare i problemi di prestazioni e tenere traccia delle tendenze relative all'uso dell'applicazione. Sono inclusi valori come il _tempo di risposta del server_ e le _eccezioni del browser_.

Le **metriche personalizzate** sono metriche definite dall'utente oltre a quelle standard disponibili automaticamente. Le metriche personalizzate devono essere create rispetto a una singola risorsa nella stessa area della risorsa. È possibile creare metriche personalizzate tramite i metodi seguenti:
    - [Definire metriche personalizzate nell'applicazione](../../azure-monitor/app/api-custom-events-metrics.md) monitorata da Application Insights. Queste metriche si aggiungono al set standard di metriche dell'applicazione.
    - Pubblicare metriche personalizzate dalle macchine virtuali Windows usando l'[estensione di diagnostica Windows](../../azure-monitor/platform/diagnostics-extension-overview.md).
    - Pubblicare metriche personalizzate dalle macchine virtuali Linux usando l'[agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).
    - Scrivere metriche personalizzate da un servizio di Azure usando l'API per le metriche personalizzate.
    
![Panoramica delle metriche](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>A cosa servono le metriche?
Tra le attività che è possibile eseguire con le metriche sono incluse le seguenti:

- Uso di [Esplora metriche](../../azure-monitor/platform/metrics-charts.md) per analizzare le metriche raccolte e presentarle tracciando un grafico. Monitoraggio delle prestazioni di una risorsa, ad esempio una macchina virtuale, un sito Web o un'app per la logica, aggiungendo grafici a un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurazione di una [regola di avviso per la metrica](alerts-metric.md) che invia una notifica o esegue un'[operazione automatica](action-groups.md) quando la metrica supera una soglia.
- Uso della [scalabilità automatica](../../azure-monitor/platform/autoscale-overview.md) per aumentare o ridurre le risorse in base a una metrica che supera una soglia.
- Reindirizzamento di metriche a Log Analytics per analizzare i dati delle metriche insieme ai dati di log e archiviare i valori delle metriche per più di 93 giorni. 
- Trasmissione di metriche a un [hub eventi](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) per reindirizzarle ad [Analisi di flusso di Azure](../../stream-analytics/stream-analytics-introduction.md) o a sistemi esterni.
- [Archiviare](../../azure-monitor/learn/tutorial-archive-data.md) la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di conformità, verifica o creazione di report offline.
- Accesso ai valori delle metriche da una riga di comando o da un'applicazione personalizzata usando [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) o l'[API REST](../../azure-monitor/platform/rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Visualizzazione di metriche
Le metriche in Monitoraggio di Azure vengono archiviate in una serie temporale ottimizzata per il recupero rapido e in cui sono archiviati i valori delle metriche per 93 giorni. È possibile copiare metriche nei log per l'analisi e l'identificazione delle tendenze a lungo termine.

I dati delle metriche vengono usati in molti modi diversi, come descritto in precedenza. È possibile usare [Esplora metriche](../../azure-monitor/platform/metrics-charts.md) per analizzare direttamente i dati nell'archivio delle metriche e tracciare un grafico dei valori di più metriche nel tempo. È possibile visualizzare i grafici in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](../../azure-monitor/platform/rest-api-walkthrough.md).

![Esplora metriche](media/data-collection/metrics-explorer.png)





## <a name="logs"></a>Log
I log contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. Possono contenere valori numerici come le metriche, ma in genere contengono dati di testo con descrizioni dettagliate. Inoltre differiscono dalle metriche per il fatto che hanno una struttura variabile e spesso non vengono raccolti a intervalli regolari.

Un tipo di voce di log comune è un evento raccolto sporadicamente. Sono creati da un'applicazione o da un servizio e in genere includono informazioni sufficienti per fornire da soli un contesto completo. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

I log sono particolarmente utili per combinare dati da diverse origini, per analisi complesse e per l'identificazione delle tendenze nel corso del tempo. Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. Le metriche possono anche essere replicate in log per combinarle con altri dati di monitoraggio per l'analisi delle tendenze e di altri dati.



### <a name="sources-of-log-data"></a>Origini dei dati di log
Monitoraggio di Azure può raccogliere dati di logo da un'ampia gamma di origini sia all'interno di Azure sia da risorse locali. Le origini dei dati di log includono quanto segue:

- [Log attività](collect-activity-logs.md) dalle risorse di Azure che includono informazioni sulla configurazione e sull'integrità e [log di diagnostica](../../azure-monitor/platform/diagnostic-logs-stream-log-store.md) che forniscono informazioni dettagliate sul funzionamento di queste risorse.
- Agenti in macchine virtuali [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) che inviano dati di telemetria dal sistema operativo guest e dalle applicazioni a Monitoraggio di Azure in base alle [origini dati](data-sources.md) configurate.
- Dati applicazione raccolti da [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Dati che forniscono informazioni dettagliate su un determinato servizio o applicazione da [soluzioni di monitoraggio](../insights/solutions.md) o funzionalità come informazioni dettagliate sui contenitori, sulle macchine virtuali o sui gruppi di risorse.
- Dati sulla sicurezza raccolti dal [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/).
- [Metriche](#metrics) raccolte da risorse di Azure. È possibile archiviare le metriche per più di 93 giorni e analizzarle con altri dati di log.
- Dati di telemetria scritti in [Archiviazione di Azure](azure-storage-iis-table.md).
- Dati personalizzati da qualsiasi client API REST usando il client [API di raccolta dati HTTP](data-collector-api.md) o da un flusso di lavoro di [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/).

![Panoramica dei log](media/data-collection/logs-overview.png)




### <a name="what-can-you-do-with-logs"></a>A che cosa servono i log?
Tra le attività che è possibile eseguire con i log sono incluse le seguenti:

- Uso di [Log Analytics](../log-query/get-started-portal.md) nel portale di Azure per scrivere query per l'analisi dei dati di log.  Aggiunta dei risultati visualizzati come tabelle o grafici in un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico.
- Creazione di un flusso di lavoro basato su dati di log in [App per la logica](~/articles/logic-apps/index.yml).
- Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.
- Accesso ai valori delle metriche da una riga di comando o da un'applicazione personalizzata usando [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) o l'[API REST](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Visualizzazione di dati di log
Tutti i dati di log in Monitoraggio di Azure vengono recuperati utilizzando una [query di log](../log-query/log-query-overview.md) scritta con il [linguaggio di query di Esplora dati](../log-query/get-started-queries.md), che consente di recuperare, consolidare e analizzare velocemente i dati raccolti. Usare [Log Analytics](../log-query/portals.md) per scrivere e testare le query nel portale di Azure. È possibile lavorare con i risultati in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare i log usando l'[API REST di monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

> [!IMPORTANT]
> I dati da Application Insights vengono archiviati in una partizione separata rispetto ad altri dati di log in Monitoraggio di Azure. Questo approccio supporta le stesse funzionalità di altri dati di log, ma è necessario usare la [console di Application Insights](/azure-monitor/app/analytics.md) o l'[API di Application Insights](https://dev.applicationinsights.io/) per accedere a questi dati. È possibile usare una [query tra risorse](../log-query/cross-workspace-query.md) per analizzare i dati dell'applicazione insieme ad altri dati di log.

![Log](media/data-collection/logs.png)


## <a name="convert-monitoring-data"></a>Convertire i dati di monitoraggio

### <a name="metrics-to-logs"></a>Da metriche a log
È possibile copiare le metriche nei log per eseguire analisi complesse con altri tipi di dati usando il linguaggio di query avanzato di Monitoraggio di Azure. È anche possibile conservare i dati di log per periodi più lunghi delle metriche per identificare le tendenze nel corso del tempo. Usare le metriche per supportare la generazione di avvisi e l'analisi quasi in tempo reale quando si usano i log per l'individuazione delle tendenze e l'analisi con altri dati.

Per indicazioni sulla raccolta delle metriche dalle risorse di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Monitoraggio di Azure](collect-azure-metrics-logs.md). Le linee guida per la raccolta di metriche dalle risorse PaaS di Azure sono disponibili in [Configurare la raccolta di metriche delle risorse PaaS di Azure con Monitoraggio di Azure](collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Da log a metriche
Come descritto sopra, le metriche sono più reattive rispetto ai log e di conseguenza permettono di creare avvisi con latenza e costi inferiori. Una quantità significativa di dati numerici viene archiviata come log che sono adatti alle metriche, ma non vengono archiviati come metriche in Monitoraggio di Azure.  Un esempio comune sono i dati sulle prestazioni raccolti dagli agenti e dalle soluzioni di gestione. Alcuni di questi valori possono essere copiati in metriche in cui sono disponibili per la generazione di avvisi e l'analisi con Esplora metriche.

La spiegazione di questa funzionalità è riportata in [Creare avvisi delle metriche per i log in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-logs.md). L'elenco dei valori supportati è disponibile in [Metriche supportate con il monitoraggio di Azure](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Trasmettere dati a sistemi esterni
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, potrebbe essere necessario inoltrarli a uno strumento esterno, ad esempio un prodotto di informazioni di sicurezza e gestione degli eventi. Questo inoltro viene in genere eseguito direttamente dalle risorse monitorate tramite [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/). 

Le indicazioni relative ai diversi tipi di dati di monitoraggio sono disponibili in [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
