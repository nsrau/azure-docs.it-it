---
title: Raccogliere dati di monitoraggio in Azure | Microsoft Docs
description: Presentazione dei dati di monitoraggio raccolti da applicazioni e servizi in Azure e degli strumenti usati per analizzarli.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363986"
---
# <a name="collect-monitoring-data-in-azure"></a>Raccogliere dati di monitoraggio in Azure
Questo articolo contiene una panoramica dei dati di monitoraggio raccolti da applicazioni e servizi in Azure. Descrive inoltre gli strumenti da usare per analizzare questi dati. 

## <a name="types-of-monitoring-data"></a>Tipi di dati di monitoraggio
Tutti i dati di monitoraggio rientrano in una delle due tipologie fondamentali, le metriche e i log. Ogni tipologia presenta caratteristiche uniche ed è specifica di uno scenario particolare.

### <a name="metrics"></a>Metriche
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento, tra cui:

* dati distinti, incluso il valore stesso;
* l'ora in cui è stato raccolto il valore;
* il tipo di misurazione rappresentato dal valore;
* la risorsa a cui è associato il valore. 

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. È ad esempio possibile raccogliere i dati relativi all'uso del processore da una macchina virtuale ogni minuto o il numero di utenti connessi all'applicazione ogni 10 minuti.

Le metriche sono elementi leggeri e in grado di supportare scenari quasi in tempo reale. Sono utili per gli avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. È ad esempio possibile generare un avviso quando una metrica supera un valore soglia o quando la differenza tra due metriche raggiunge un valore specifico.

Ogni metrica, se considerata singolarmente, non offre informazioni particolarmente dettagliate. Specifica un unico valore senza altro contesto che il confronto con una soglia semplice. Sono preziose se combinate con altre metriche per identificare schemi e tendenze o se combinate con log che forniscono contesto per determinati valori. 

Ad esempio, un certo numero di utenti connessi all'applicazione in un determinato momento potrebbe non dire molto sull'integrità dell'applicazione, mentre un improvviso calo nel numero degli utenti, indicato da più valori della stessa metrica, potrebbe indicare un problema. Un numero eccessivo di eccezioni generate dall'applicazione, e indicato da una metrica separata, potrebbe identificare un problema dell'applicazione che causa il calo. Gli eventi che l'applicazione crea per identificare gli errori dei componenti possono essere utili ai fini dell'identificazione della causa principale.

Gli avvisi basati sui log non sono reattivi come gli avvisi basati sulle metriche, ma possono includere una logica più complessa. È possibile creare un avviso in base ai risultati di ogni query che esegue analisi complesse sui dati provenienti da più origini.

### <a name="logs"></a>Log
I log contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. I log potrebbero contenere valori numerici come le metriche, ma in genere contengono dati di testo con descrizioni dettagliate. Inoltre differiscono dalle metriche per il fatto che hanno una struttura variabile e spesso non vengono raccolti a intervalli regolari.

Un tipo di voce di log comune è un evento. Gli eventi vengono raccolti sporadicamente. Sono creati da un'applicazione o da un servizio e in genere includono informazioni sufficienti per fornire da soli un contesto completo. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

I log sono particolarmente utili per combinare dati da una varietà di origini, per analisi complesse e l'identificazione delle tendenze nel corso del tempo. Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. Le metriche possono anche essere replicate in log per essere combinate ad altri dati di monitoraggio per l'analisi delle tendenze e di altri dati.


## <a name="monitoring-tools-in-azure"></a>Strumenti di monitoraggio in Azure
In Azure i dati di monitoraggio vengono raccolti e analizzati attraverso le seguenti origini.

### <a name="azure-monitor"></a>Monitoraggio di Azure
Le metriche associate alle applicazioni e alle risorse di Azure vengono raccolte in Monitoraggio di Azure. I dati delle metriche vengono integrati nelle pagine del portale di Azure per le risorse di Azure. Per le macchine virtuali, i grafici di metriche come l'uso della CPU e della rete vengono visualizzati per la macchina selezionata. 

È anche possibile analizzare i dati usando [Esplora metriche](../monitoring-and-diagnostics/monitoring-metric-charts.md), che rappresenta graficamente i valori di più metriche nel tempo. È possibile visualizzare i grafici in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche usando l'[API REST di monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Per ulteriori informazioni sui dati delle metriche raccolti da tipi diversi di risorse di Azure, vedere [Origini dei dati di monitoraggio di Azure](monitoring-data-sources.md). 

![Esplora metriche](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>Log attività 
Il [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) archivia i log sulla configurazione e l'integrità dei servizi di Azure. È possibile usare l'Utilità di esplorazione dei log attività per visualizzare questi log nel portale di Azure, ma in genere vengono [copiati in Azure Log Analytics](../log-analytics/log-analytics-activity.md) per essere analizzati con altri dati di log.

È possibile usare l'Utilità di esplorazione dei log attività per visualizzare il log attività filtrato in base a determinati criteri. La maggior parte delle risorse dispone di un'opzione **Log attività** nel rispettivo menu nel portale di Azure. Consente di visualizzare l'Utilità di esplorazione dei log attività filtrata in base a quella risorsa. È anche possibile recuperare i log attività usando l'[API REST Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Utilità di esplorazione dei log attività](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics offre una piattaforma dati comune per la gestione in Azure. È il servizio primario usato per l'archiviazione e l'analisi dei log in Azure. Raccoglie dati da una varietà di origini, inclusi agenti su macchine virtuali, soluzioni di gestione e risorse di Azure. È possibile copiare dati da altre origini, incluse metriche e il log attività, per creare un repository centrale completo di dati di monitoraggio.

Log Analytics dispone di un linguaggio di query sofisticato per l'analisi dei dati raccolti. È possibile usare i [portali per la ricerca log](../log-analytics/log-analytics-log-search-portals.md) per scrivere e testare le query in modo interattivo e analizzarne i risultati. È anche possibile [creare visualizzazioni](../log-analytics/log-analytics-view-designer.md) per visualizzare i risultati delle ricerche log o incollare i risultati di una query direttamente in un dashboard di Azure.  

Le soluzioni di gestione includono ricerche nei log e visualizzazioni in Log Analytics per analizzare i dati raccolti. Altri servizi, ad esempio Azure Application Insights, archiviano i dati in Log Analytics e forniscono strumenti di analisi aggiuntivi.  

![Log](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights raccoglie dati di telemetria per le applicazioni Web installate in un'ampia gamma di piattaforme. Archivia i dati in Monitoraggio di Azure e in Log Analytics. Fornisce un set completo di strumenti per l'analisi e la visualizzazione dei relativi dati. Queste funzionalità consentono di usare un set comune di servizi quali avvisi, ricerche nei log e dashboard usati per altri monitoraggi.


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Elenco dei servizi
L'elenco dei servizi offre una rappresentazione visiva delle macchine virtuali con i relativi processi e dipendenze. Archivia la maggior parte di questi dati in Log Analytics in modo che sia possibile analizzarli con altri dati di gestione. La console Elenco dei servizi recupera i dati da Log Analytics per presentarli nel contesto della macchina virtuale oggetto di analisi.

![Elenco dei servizi](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Trasferimento dei dati di monitoraggio

### <a name="metrics-to-logs"></a>Da metriche a log
È possibile replicare le metriche in Log Analytics per eseguire analisi complesse con altri tipi di dati usando il linguaggio di query. È anche possibile conservare i dati di log per periodi più lunghi delle metriche per identificare le tendenze nel corso del tempo. Quando le metriche o altri dati sulle prestazioni vengono archiviati in Log Analytics, i dati fungono da log. Usare le metriche per supportare la generazione di avvisi e l'analisi quasi in tempo reale quando si usano i log per l'individuazione delle tendenze e l'analisi con altri dati.

Per indicazioni sulla raccolta delle metriche dalle risorse di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../log-analytics/log-analytics-azure-storage.md). Le linee guida per la raccolta di metriche dalle risorse PaaS di Azure sono disponibili in [Configurare la raccolta di metriche delle risorse PaaS di Azure con Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Da log a metriche
Come descritto in precedenza, le metriche sono più reattive rispetto ai log, pertanto consentono di creare avvisi con latenza e costi inferiori. Log Analytics raccoglie una quantità significativa di dati numerici che sono adatti alle metriche, ma che non vengono archiviati in Monitoraggio di Azure. 

Un esempio comune sono i dati sulle prestazioni raccolti dagli agenti e dalle soluzioni di gestione. Alcuni di questi valori possono essere copiati in Monitoraggio di Azure, dove sono disponibili per generare avvisi ed eseguire analisi con Esplora metriche.

La spiegazione di questa funzionalità è riportata nel post di blog sugli [avvisi delle metriche per i log ora disponibili in un'anteprima pubblica limitata](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). L'elenco degli elementi supportati è disponibile in [Metriche e metodi di creazione supportati per i nuovi avvisi delle metriche](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hubs"></a>Hub eventi
Oltre a usare gli strumenti in Azure per analizzare i dati di monitoraggio, è possibile inoltrarli a uno strumento esterno, ad esempio un prodotto di gestione degli eventi e delle informazioni di sicurezza (SIEM, security information and event management). Questo inoltro viene in genere eseguito attraverso [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/). 

Le indicazioni relative ai diversi tipi di dati di monitoraggio sono disponibili in [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dati di monitoraggio disponibili](monitoring-data-sources.md) per diverse risorse in Azure. 