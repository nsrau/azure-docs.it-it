---
title: Visualizzazione dei dati da Monitoraggio di Azure | Microsoft Docs
description: Questo articolo contiene un riepilogo dei metodi disponibili per visualizzare i dati archiviati in Monitoraggio di Azure, inclusi quelli dall'archivio delle metriche e di Log Analytics.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: 4200accee94840a8a4455308f24a857fd5d7034a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632977"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizzazione dei dati da Monitoraggio di Azure
Questo articolo contiene un riepilogo dei metodi disponibili per visualizzare i dati archiviati in Monitoraggio di Azure, tra cui le [metriche nell'archivio delle metriche di Azure](../azure-monitor/platform/data-collection.md#metrics) e i [dati di log in Log Analytics](../azure-monitor/platform/data-collection.md#logs). 

Visualizzazioni come grafici e grafi consentono di analizzare i dati di monitoraggio per il drill-down dei problemi e per identificare i criteri. A seconda dello strumento usato, potrebbe essere possibile anche condividere visualizzazioni con altri utenti all'interno e all'esterno dell'organizzazione.

## <a name="azure-dashboards"></a>Dashboard di Azure
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) sono la tecnologia principale per la gestione di dashboard per Azure. Sono particolarmente utili poiché forniscono un unico riquadro di monitoraggio dell'infrastruttura e dei servizi di Azure per identificare rapidamente i problemi importanti.

![dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Vantaggi
- Integrazione avanzata in Azure. Le visualizzazioni possono essere aggiunte ai dashboard da più pagine di Azure, tra cui Esplora metriche, Log Analytics e Application Insights.
- Supporto sia per le metriche sia per i log.
- Combinazione dei provenienti da più origini, tra cui l'output da [Esplora metriche](../monitoring-and-diagnostics/monitoring-metric-charts.md), [query di Log Analytics](../log-analytics/log-analytics-queries.md), [mappe](../application-insights/app-insights-app-map.md) e [disponibilità]() in Application Insights.
- Opzione per dashboard personali o condivisi, integrati con l'[autenticazione basata su ruoli (RBAC)](../role-based-access-control/overview.md) di Azure.
- Aggiornamento automatico delle metriche in base a un intervallo di tempo con un valore minimo di cinque minuti e dei log con intervallo di un minuto.
- Dashboard delle metriche con parametri con timestamp e parametri personalizzati.
- Opzioni di layout flessibili.
- Modalità schermo intero.


### <a name="limitations"></a>Limitazioni
- Controllo limitato delle visualizzazioni di Log Analytics senza supporto per le tabelle di dati. Il numero totale delle serie di dati è limitato a 10 con altre serie di dati raggruppate in un _altro_ contenitore.
- Nessun supporto dei parametri personalizzati per i grafici di Log Analytics.
- I grafici di Log Analytics sono limitati agli ultimi 30 giorni.
- I grafici di Log Analytics possono essere aggiunti solo ai dashboard condivisi.
- Nessuna interattività con i dati del dashboard.
- Drill-down contestuale limitato.

## <a name="azure-monitor-views"></a>Visualizzazioni di Monitoraggio di Azure
Le [visualizzazioni di Monitoraggio di Azure](../azure-monitor/platform/view-designer.md) consentono di creare visualizzazioni personalizzate con i dati di log archiviati in Log Analytics. Vengono usate dalle [soluzioni di monitoraggio](../azure-monitor/insights/solutions.md) per presentare i dati raccolti.

![Visualizza](media/visualizations/view.png)

### <a name="advantages"></a>Vantaggi
- Visualizzazioni avanzate per i dati di Log Analytics.
- Esportazione e importazione delle visualizzazioni per trasferirle ad altri gruppi di risorse e sottoscrizioni.
- Integrazione nel modello di gestione di Log Analytics con le aree di lavoro e le soluzioni di monitoraggio.
- [Filtri](../azure-monitor/platform/view-designer-filters.md) per i parametri personalizzati.
- Interattività, supporto per il drill-in a più livelli con una visualizzazione che analizza un'altra visualizzazione.

### <a name="limitations"></a>Limitazioni
- Supporto per i log, ma non per le metriche.
- Nessuna visualizzazione personale. Disponibilità per tutti gli utenti con accesso all'area di lavoro.
- Nessun aggiornamento automatico.
- Opzioni di layout limitate.
- Nessun supporto per l'esecuzione di query tra le aree di lavoro di Log Analytics e le applicazioni di Application Insights.
- Le dimensioni della risposta delle query sono limitate a 8 MB e il tempo di esecuzione è 110 secondi.



## <a name="application-insights-workbooks"></a>Cartelle di lavoro di Application Insights
Le [cartelle di lavoro](../application-insights/app-insights-usage-workbooks.md) sono documenti interattivi che forniscono informazioni dettagliate sui dati, sulle analisi e la collaborazione all'interno del team. Esempi specifici in cui le cartelle di lavoro sono utili sono le guide alla risoluzione dei problemi e l'analisi a posteriore degli eventi imprevisti.

![Cartella di lavoro](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantaggi
- Supporto sia per le metriche sia per i log.
- Supporto dei parametri che consentono report interattivi in cui la selezione di un elemento di una tabella aggiorna dinamicamente i grafici e le visualizzazioni associati.
- Flusso simile a quello dei documenti.
- Opzione per cartelle di lavoro personali o condivise.
- Esperienza di creazione facile e collaborativa.
- Supporto per i modelli della raccolta pubblica di modelli basata su GitHub.

### <a name="limitations"></a>Limitazioni
- Nessun aggiornamento automatico.
- Nessun layout ad alta densità, come i dashboard, limitando l'utilità delle cartelle di lavoro come singolo riquadro. Utilizzo specifico per fornire informazioni più dettagliate.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è particolarmente utile per la creazione di dashboard e report incentrati sul business, nonché di report di analisi delle tendenze a lungo termine degli indicatori di prestazioni chiave (KPI). È possibile [importare i risultati di una query di Log Analytics](../log-analytics/log-analytics-powerbi.md) in un set di dati di Power BI in modo da poter sfruttare le funzionalità di questa soluzione, come la possibilità di combinare dati provenienti da origini diverse e di condividere report sul Web e su dispositivi mobili.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantaggi
- Visualizzazioni avanzate.
- Ampia interattività, compresi lo zoom avanti e il filtro incrociato.
- Facilità di condivisione in tutta l'organizzazione.
- Integrazione con altri dati da più origini dati.
- Prestazioni migliori con i risultati memorizzati nella cache in un cubo.


### <a name="limitations"></a>Limitazioni
- Supporto per i log, ma non per le metriche.
- Nessuna integrazione in Azure. Gestione dei dashboard e dei modelli tramite Azure Resource Manager non consentita.
- Necessità di importare i risultati della query nel modello Power BI da configurare. Limitazioni per le dimensioni del risultato e l'aggiornamento.
- Aggiornamento dei dati limitato a otto volte al giorno.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) è una piattaforma aperta, ideale per i dashboard operativi. È particolarmente utile per il rilevamento, l'isolamento e la valutazione degli eventi operativi imprevisti. È possibile aggiungere il [plug-in Grafana dell'origine dati Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-send-to-grafana.md) alla sottoscrizione di Azure per visualizzare i dati delle metriche di Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantaggi
- Visualizzazioni avanzate.
- Ampio ecosistema di origini dati.
- Interattività dei dati, tra cui zoom avanti.
- Supporto dei parametri.

### <a name="limitations"></a>Limitazioni
- Supporto per le metriche, ma non per i log.
- Nessuna integrazione in Azure. Gestione dei dashboard e dei modelli tramite Azure Resource Manager non consentita.
- Costi per supportare l'infrastruttura aggiuntiva di Grafana o costi aggiuntivi per Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Compilare un'applicazione personalizzata
È possibile accedere ai dati nelle metriche di Azure e in Log Analytics tramite la relativa API usando qualsiasi client REST, che consente di compilare applicazioni e siti Web personalizzati.

### <a name="advantages"></a>Vantaggi
- Massima flessibilità nell'interfaccia utente, visualizzazione, interattività e funzionalità.
- Combinazione di metriche e dati di log con altre origini dati.

### <a name="disadvantages"></a>Svantaggi:
- Necessità di un notevole lavoro di progettazione.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [dati raccolti da Monitoraggio di Azure](../azure-monitor/platform/data-collection.md).
- Informazioni sui [dashboard di Azure](../azure-portal/azure-portal-dashboards.md).
- Informazioni sulle [visualizzazioni in Monitoraggio di Azure](../azure-monitor/platform/view-designer.md).
- Informazioni sulle [cartelle di lavoro di Application Insights](../application-insights/app-insights-usage-workbooks.md).
- Informazioni sull'[importazione dei dati di log in Power BI](../log-analytics/log-analytics-powerbi.md).
- Informazioni sul [plug-in Grafana dell'origine dati Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
