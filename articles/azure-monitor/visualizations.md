---
title: Visualizzazione dei dati da Monitoraggio di Azure | Microsoft Docs
description: Fornisce un riepilogo dei metodi disponibili per visualizzare i dati di metrica e log archiviati in Monitoraggio di Azure.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2018
ms.author: bwren
ms.openlocfilehash: 6390384220830d473b56f4082a98dfaa8aa686d1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984180"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizzazione dei dati da Monitoraggio di Azure
Questo articolo contiene un riepilogo dei metodi disponibili per visualizzare i dati di metrica e log archiviati in Monitoraggio di Azure.

Visualizzazioni come grafici e grafi consentono di analizzare i dati di monitoraggio per il drill-down dei problemi e per identificare i criteri. A seconda dello strumento usato, potrebbe essere possibile anche condividere visualizzazioni con altri utenti all'interno e all'esterno dell'organizzazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Dashboard di Azure
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) sono la tecnologia principale per la gestione di dashboard per Azure. Sono particolarmente utili poiché forniscono un unico riquadro di monitoraggio dell'infrastruttura e dei servizi di Azure per identificare rapidamente i problemi importanti.

![dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Vantaggi
- Integrazione avanzata in Azure. Le visualizzazioni possono essere aggiunte ai dashboard da più pagine di Azure, tra cui Esplora metriche, Log Analytics e Application Insights.
- Supporto sia per le metriche sia per i log.
- Combina i dati di più origini, tra cui output da [Esplora metriche](platform/metrics-charts.md), [query di log](log-query/log-query-overview.md)e [mappe](app/app-map.md) e disponibilità in Application Insights.
- Opzione per dashboard personali o condivisi, integrati con l'[autenticazione basata su ruoli (RBAC)](../role-based-access-control/overview.md) di Azure.
- Aggiornamento automatico delle metriche in base a un intervallo di tempo con un valore minimo di cinque minuti Registra l'aggiornamento ogni ora, con un'opzione di aggiornamento manuale su richiesta facendo clic sull'icona "Aggiorna" in una determinata visualizzazione o aggiornando il dashboard completo.
- Dashboard delle metriche con parametri con timestamp e parametri personalizzati.
- Opzioni di layout flessibili.
- Modalità schermo intero.


### <a name="limitations"></a>Limitazioni
- Controllo limitato delle visualizzazioni dei log senza supporto per le tabelle di dati. Il numero totale delle serie di dati è limitato a 10 con altre serie di dati raggruppate in un _altro_ contenitore.
- Nessun supporto dei parametri personalizzati per i grafici dei log.
- I grafici dei log sono limitati agli ultimi 30 giorni.
- I grafici dei log possono essere aggiunti solo ai dashboard condivisi.
- Nessuna interattività con i dati del dashboard.
- Drill-down contestuale limitato.

## <a name="azure-monitor-views"></a>Visualizzazioni di Monitoraggio di Azure
Le [visualizzazioni di Monitoraggio di Azure](platform/view-designer.md) consentono di creare visualizzazioni personalizzate con i dati di log. Vengono usate dalle [soluzioni di monitoraggio](insights/solutions.md) per presentare i dati raccolti.

![Visualizza](media/visualizations/view.png)

### <a name="advantages"></a>Vantaggi
- Visualizzazioni avanzate per i dati di log.
- Esportazione e importazione delle visualizzazioni per trasferirle ad altri gruppi di risorse e sottoscrizioni.
- Integrazione nel modello di gestione di Monitoraggio di Azure con aree di lavoro e soluzioni di monitoraggio.
- [Filtri](platform/view-designer-filters.md) per i parametri personalizzati.
- Interattività, supporto per il drill-in a più livelli con una visualizzazione che analizza un'altra visualizzazione.

### <a name="limitations"></a>Limitazioni
- Supporto per i log, ma non per le metriche.
- Nessuna visualizzazione personale. Disponibilità per tutti gli utenti con accesso all'area di lavoro.
- Nessun aggiornamento automatico.
- Opzioni di layout limitate.
- Nessun supporto per l'esecuzione di query tra più aree di lavoro o applicazioni di Application Insights.
- Le dimensioni della risposta delle query sono limitate a 8 MB e il tempo di esecuzione è 110 secondi.


## <a name="workbooks"></a>Cartelle di lavoro
Le [cartelle di lavoro](../azure-monitor/app/usage-workbooks.md) sono documenti interattivi che forniscono informazioni dettagliate sui dati, sulle analisi e la collaborazione all'interno del team. Esempi specifici in cui le cartelle di lavoro sono utili sono le guide alla risoluzione dei problemi e l'analisi a posteriore degli eventi imprevisti.

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
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è particolarmente utile per la creazione di dashboard e report incentrati sul business, nonché di report di analisi delle tendenze a lungo termine degli indicatori di prestazioni chiave (KPI). È possibile [importare i risultati di una query di log](platform/powerbi.md) in un set di dati di Power BI in modo da poter sfruttare le funzionalità di questa soluzione, come la possibilità di combinare dati provenienti da origini diverse e di condividere report sul Web e su dispositivi mobili.

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
[Grafana](https://grafana.com/) è una piattaforma aperta, ideale per i dashboard operativi. È particolarmente utile per il rilevamento, l'isolamento e la valutazione degli eventi operativi imprevisti. È possibile aggiungere il [plug-in Grafana dell'origine dati Monitoraggio di Azure](platform/grafana-plugin.md) alla sottoscrizione di Azure per visualizzare i dati delle metriche di Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantaggi
- Visualizzazioni avanzate.
- Ampio ecosistema di origini dati.
- Interattività dei dati, tra cui zoom avanti.
- Supporto dei parametri.

### <a name="limitations"></a>Limitazioni
- Nessuna integrazione in Azure. Gestione dei dashboard e dei modelli tramite Azure Resource Manager non consentita.
- Costi per supportare l'infrastruttura aggiuntiva di Grafana o costi aggiuntivi per Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Compilare un'applicazione personalizzata
È possibile accedere ai dati di metrica e log in Monitoraggio di Azure tramite la relativa API usando qualsiasi client REST, che consente di compilare applicazioni e siti Web personalizzati.

### <a name="advantages"></a>Vantaggi
- Massima flessibilità nell'interfaccia utente, visualizzazione, interattività e funzionalità.
- Combinazione di metriche e dati di log con altre origini dati.

### <a name="disadvantages"></a>Svantaggi
- Necessità di un notevole lavoro di progettazione.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [dati raccolti da Monitoraggio di Azure](platform/data-platform.md).
- Informazioni sui [dashboard di Azure](../azure-portal/azure-portal-dashboards.md).
- Informazioni sulle [visualizzazioni in Monitoraggio di Azure](platform/view-designer.md).
- Informazioni sulle [cartelle di lavoro](../azure-monitor/app/usage-workbooks.md)di.
- Informazioni sull'[importazione dei dati di log in Power BI](../azure-monitor/platform/powerbi.md).
- Informazioni sul [plug-in Grafana dell'origine dati Monitoraggio di Azure](../azure-monitor/platform/grafana-plugin.md).

