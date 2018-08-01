---
title: Raccolta dei dati di monitoraggio in Azure | Microsoft Docs
description: Panoramica dei dati di monitoraggio raccolti dall'applicazione e dai servizi di Azure e degli strumenti usati per analizzarli.
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
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213300"
---
# <a name="collecting-monitoring-data-in-azure"></a>Raccolta dei dati di monitoraggio in Azure
Questo articolo offre una panoramica dei dati di monitoraggio raccolti dall'applicazione e dai servizi di Azure e degli strumenti usati per analizzarli. 

## <a name="types-of-monitoring-data"></a>Tipi di dati di monitoraggio
Tutti i dati di monitoraggio rientrano in una delle due tipologie fondamentali, le metriche e i log. Ogni tipologia presenta caratteristiche distinte e risulta più adatta per determinati scenari, come descritto di seguito.

### <a name="metrics"></a>Metriche
Le metriche sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Includono dati distinti, tra cui il valore stesso, l'ora in cui il valore è stato raccolto, il tipo di misurazione che il valore rappresenta e la risorsa specifica a cui il valore è associato. Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Si può ad esempio raccogliere l'utilizzo del processore in una macchina virtuale ogni minuto o il numero di utenti connessi all'applicazione ogni 10 minuti.

Le metriche sono elementi leggeri e in grado di supportare scenari quasi in tempo reale. Sono particolarmente utili per la creazione di avvisi poiché le metriche possono essere campionate frequentemente e un avviso può essere generato rapidamente con una logica relativamente semplice. Ad esempio, è possibile attivare un avviso quando una metrica supera un valore di soglia oppure quando la differenza tra il valore di due metriche raggiunge un determinato valore.

Ogni metrica, se considerata singolarmente, non offre informazioni particolarmente dettagliate. Specifica un unico valore senza altro contesto che il confronto con una soglia semplice. Le metriche sono utili se usate in combinazione con altre metriche per identificare modelli e tendenze o in combinazione con i log, che specificano il contesto relativo a determinati valori. Ad esempio, il numero di utenti che usano l'applicazione in un determinato momento può non dire molto sull'integrità dell'applicazione. Un calo improvviso del numero di utenti, indicato da più valori della stessa metrica, può però indicare un problema. Un numero eccessivo di eccezioni generate dall'applicazione e indicate da una metrica separata può identificare un problema dell'applicazione che causa il calo. Gli eventi creati dall'applicazione che identificano un errore in determinati componenti dell'applicazione possono agevolare l'identificazione della causa principale.

Gli avvisi basati sui log non sono reattivi come gli avvisi basati sulle metriche, ma possono includere una logica più complessa. È possibile creare un avviso in base ai risultati di ogni query che esegue analisi complesse sui dati provenienti da più origini.

### <a name="logs"></a>Log
I log contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. Possono contenere valori numerici come le metriche ma in genere contengono dati di testo con descrizioni dettagliate. Inoltre differiscono dalle metriche per il fatto che hanno una struttura variabile e spesso non vengono raccolti a intervalli regolari.

Un tipo di voce di log comune è un evento. Gli eventi vengono raccolti sporadicamente man mano che vengono creati da un'applicazione o un servizio e in genere includono informazioni sufficienti per offrire un contesto completo.  Ad esempio, un evento può indicare che una particolare risorsa è stata creata o modificata, che è stato avviato un nuovo host in risposta all'aumento del traffico o che è stato rilevato un errore in un'applicazione.

I log sono particolarmente utili per combinare dati provenienti da origini diverse per le analisi complesse e l'individuazione di tendenze nel tempo. Poiché il formato dei dati può variare, le applicazioni sono in grado di creare log personalizzati usando la struttura necessaria. Anche le metriche possono essere replicate nei registri e combinate con altri dati di monitoraggio per l'individuazione delle tendenze e altre operazioni di analisi dei dati.


## <a name="monitoring-tools-in-azure"></a>Strumenti di monitoraggio in Azure
I dati di monitoraggio di Azure vengono raccolti e analizzati usando più origini che sono descritte nelle sezioni seguenti.

### <a name="azure-metrics"></a>Metriche di Azure
Le metriche provenienti da applicazioni e risorse di Azure vengono raccolte in Metriche di Azure. I dati relativi alle metriche vengono integrati nelle pagine del portale di Azure per risorse di Azure specifiche, ad esempio le macchine virtuali, che includono grafici di metriche come l'utilizzo di CPU e rete per il computer selezionato. Possono anche essere analizzati usando [Esplora metriche](../monitoring-and-diagnostics/monitoring-metric-charts.md) in cui vengono rappresentati graficamente i valori di più metriche nel tempo.  È possibile visualizzare i grafici in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni. È anche possibile recuperare le metriche con l'[API REST di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Si possono ottenere dettagli sui dati delle metriche raccolti da diversi tipi di risorse di Azure in [Origini dei dati di monitoraggio in Azure](monitoring-data-sources.md). 

![Esplora metriche](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure Activity Log 
Il [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) archivia i log sulla configurazione e l'integrità dei servizi di Azure. È possibile usare l'utilità di esplorazione dei log attività per visualizzare i log nel portale di Azure, ma normalmente vengono [copiati in Log Analytics](../log-analytics/log-analytics-activity.md) per essere analizzati con altri dati di log.

È possibile usare l'utilità di esplorazione dei log attività per visualizzare il log attività filtrato in modo da soddisfare determinati criteri.  Nel menu della maggior parte delle risorse presenti nel portale di Azure è inoltre disponibile un'opzione Log attività che consente di visualizzare l'utilità di esplorazione dei log attività filtrata per tale risorsa. È anche possibile recuperare i log attività con l'[API REST di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Utilità di esplorazione dei log attività](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics offre una piattaforma dati comune per la gestione in Azure. È il servizio principale usato per l'archiviazione e l'analisi dei log in Azure, la raccolta dei dati da diverse origini, tra cui gli agenti nelle macchine virtuali, le soluzioni di gestione e diverse risorse di Azure. I dati di altre origini, tra cui metriche e log attività, possono essere copiati in Log Analytics per creare un repository centrale completo dei dati di monitoraggio.

Log Analytics usa un linguaggio di query avanzato per analizzare i dati che raccoglie.  È possibile usare i [portali per la ricerca log](../log-analytics/log-analytics-log-search-portals.md) per scrivere e testare le query in modo interattivo e analizzarne i risultati. È anche possibile [creare visualizzazioni](../log-analytics/log-analytics-view-designer.md) per visualizzare i risultati delle ricerche log o incollare i risultati di una query direttamente in un dashboard di Azure.  Le soluzioni di gestione includono ricerche log e visualizzazioni in Log Analytics per analizzare i dati raccolti. Altri servizi, ad esempio Application Insights, archiviano i dati in Log Analytics e mettono a disposizione strumenti aggiuntivi per l'analisi.  

![Log](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights raccoglie dati di telemetria per le applicazioni Web installate in un'ampia gamma di piattaforme. Archivia i propri dati in Metriche di Azure e Log Analytics e offre un'ampia gamma di strumenti avanzati, oltre agli strumenti esistenti per l'analisi dei dati, per analizzare e visualizzare i dati. In questo modo è possibile sfruttare un set comune di servizi, ad esempio avvisi, ricerche log e dashboard, che si usano per altri tipi di monitoraggio.


![Informazioni sull'app](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Elenco dei servizi
L'elenco dei servizi offre una rappresentazione visiva delle macchine virtuali con i relativi processi e dipendenze. Archivia la maggior parte di questi dati in Log Analytics in modo che sia possibile analizzarli con altri dati di gestione. La console Elenco dei servizi recupera anche i dati da Log Analytics per presentarli nel contesto della macchina virtuale che si sta analizzando.

![Elenco dei servizi](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Trasferimento dei dati di monitoraggio

### <a name="metrics-to-logs"></a>Da metriche a log
Le metriche possono anche essere replicate in Log Analytics per eseguire analisi complesse con altri tipi di dati usando un linguaggio di query avanzato. È inoltre possibile conservare i dati dei log per periodi più lunghi rispetto alle metriche, e questo consente di eseguire analisi delle tendenze nel tempo. Quando le metriche o altri dati sulle prestazioni vengono archiviati in Log Analytics, i dati fungono da log. Usare le metriche per supportare la generazione di avvisi e l'analisi quasi in tempo reale quando si usano i log per l'individuazione delle tendenze e l'analisi con altri dati.

Per indicazioni sulla raccolta delle metriche dalle risorse di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../log-analytics/log-analytics-azure-storage.md). Le linee guida per la raccolta di metriche dalle risorse PaaS di Azure sono disponibili in [Configurare la raccolta di metriche delle risorse PaaS di Azure con Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Da log a metriche
Come descritto in precedenza, le metriche sono più reattive rispetto ai log e consentono di creare avvisi con una minore latenza e a un costo inferiore. Log Analytics raccoglie una quantità significativa di dati numerici che potrebbero essere adatti alle metriche, ma che non vengono archiviati in Metriche di Azure. Un esempio comune sono i dati sulle prestazioni raccolti dagli agenti e dalle soluzioni di gestione. Alcuni di questi valori possono essere copiati in Metriche di Azure in cui sono disponibili per la generazione di avvisi e l'analisi con Esplora metriche.

La spiegazione di questa funzionalità è riportata nel post di blog sugli [avvisi delle metriche per i log ora disponibili in un'anteprima pubblica limitata](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). L'elenco degli elementi supportati è disponibile in [Metriche e metodi di creazione supportati per i nuovi avvisi delle metriche](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Hub eventi
Oltre a usare gli strumenti di Azure per analizzare i dati di monitoraggio, è possibile inoltrare i dati a uno strumento esterno, ad esempio un prodotto per le informazioni di sicurezza e la gestione degli eventi (SIEM). Questa operazione viene in genere eseguita usando l'[hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/). 

Le indicazioni relative ai diversi tipi di dati di monitoraggio sono disponibili in [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dati di monitoraggio disponibili](monitoring-data-sources.md) per diverse risorse in Azure. 