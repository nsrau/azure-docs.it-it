---
title: Origini dei dati in Monitoraggio di Azure | Microsoft Docs
description: Descrive i dati disponibili per monitorare l'integrità e le prestazioni delle risorse di Azure e le applicazioni che si basano su tali dati.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 6d03c219025c8cd39214bd8ab6807125709f9742
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60235888"
---
# <a name="sources-of-data-in-azure-monitor"></a>Origini dei dati in Monitoraggio di Azure
Questo articolo descrive le origini dei dati raccolti da Monitoraggio di Azure per monitorare l'integrità e le prestazioni delle risorse e le applicazioni che si basano su tali dati. Queste risorse possono trovarsi in Azure, in un altro cloud o in locale.  Vedere [Dati raccolti da Monitoraggio di Azure](data-platform.md) per informazioni dettagliate sul modo in cui vengono archiviati questi dati e su come visualizzarli.

I dati di monitoraggio in Azure provengono da varie origini che possono essere organizzate in livelli, dove il livello più alto è la propria applicazione unitamente ai sistemi operativi e quelli più bassi sono i componenti della piattaforma di Azure. Questa organizzazione è illustrata nel diagramma seguente e ogni livello è descritto in dettaglio nelle sezioni che seguono.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![Livelli dei dati di monitoraggio](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Tenant di Azure
I dati di telemetria correlati al tenant di Azure vengono raccolti da servizi a livello di tenant, ad esempio Azure Active Directory.

![Raccolta del tenant di Azure](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Log di controllo di Azure Active Directory
I [report di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contengono la cronologia dell'attività di accesso e l'audit trail delle modifiche apportate all'interno di un tenant specifico. Questi log di controllo possono essere scritti in Monitoraggio di Azure per essere analizzati con altri dati di log.


## <a name="azure-platform"></a>Piattaforma Azure
I dati di telemetria correlati all'integrità e al funzionamento di Azure stesso includono i dati sul funzionamento e sulla gestione della sottoscrizione di Azure. Includono i dati di integrità dei servizi archiviati nel log attività di Azure e nei log di controllo di Azure Active Directory.

![Raccolta della sottoscrizione di Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Integrità dei servizi di Azure
[Integrità dei servizi di Azure](service-notifications.md) presenta informazioni sull'integrità dei servizi di Azure nella sottoscrizione su cui si basano l'applicazione e le risorse in uso. È possibile creare avvisi che notifichino eventuali problemi critici attuali e previsti che potrebbero influire negativamente sull'applicazione. I record dell'integrità dei servizi vengono archiviati nel [log attività di Azure](activity-logs-overview.md), pertanto è possibile visualizzarli nell'utilità di esplorazione del log attività e copiarli nei log di Monitoraggio di Azure.

### <a name="azure-activity-log"></a>Azure Activity Log
Il [log attività di Azure](activity-logs-overview.md) include i record di integrità dei servizi insieme ai record sulle modifiche alla configurazione apportate alle risorse di Azure. Il log attività è disponibile per tutte le risorse di Azure e ne rappresenta la visualizzazione _esterna_. I tipi specifici di record nel log attività sono descritti in [Azure Activity Log event schema](activity-log-schema.md) (Schema degli eventi del log attività di Azure).

È possibile visualizzare il log attività per una risorsa specifica nella pagina corrispondente nel portale di Azure o visualizzare i log di più risorse nell'[utilità di esplorazione dei log attività](activity-logs-overview.md). È particolarmente utile copiare le voci di log in Monitoraggio di Azure per combinarle con altri dati di monitoraggio. È possibile inoltre inviarle ad altre posizioni usando gli [hub eventi](activity-logs-stream-event-hubs.md).



## <a name="azure-services"></a>Servizi di Azure.
I log di diagnostica a livello di metrica e di risorsa offrono informazioni sul funzionamento _interno_ delle risorse di Azure. Questi log sono disponibili per la maggior parte dei servizi di Azure e le soluzioni di gestione offrono informazioni approfondite sui servizi specifici.

![Raccolta di risorse di Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metriche
La maggior parte dei servizi di Azure genera [metriche di piattaforma](data-platform-metrics.md) che ne riflettono le prestazioni e il funzionamento. Le [metriche specifiche variano in base al tipo di risorsa](metrics-supported.md).  Sono accessibili dall'analisi delle metriche e possono essere copiate in log per eseguire l'analisi delle tendenze e altre analisi usando Log Analytics.


### <a name="resource-diagnostic-logs"></a>Log di diagnostica delle risorse
Mentre il log attività include informazioni sulle operazioni eseguite nelle risorse di Azure, i [log di diagnostica](diagnostic-logs-overview.md) a livello di risorsa presentano informazioni approfondite sul funzionamento della risorsa stessa.   I requisiti di configurazione e il contenuto di questi log [variano in base al tipo di risorsa](diagnostic-logs-schema.md).

Non è possibile visualizzare direttamente i log di diagnostica nel portale di Azure, ma è possibile [inviarli ad Archiviazione di Azure per l'archiviazione](archive-diagnostic-logs.md) ed esportarli nell'[hub eventi](../../event-hubs/event-hubs-about.md) per il reindirizzamento ad altri servizi o a [Monitoraggio di Azure](diagnostic-logs-stream-log-store.md) per l'analisi. Alcune risorse possono scrivere direttamente in Monitoraggio di Azure mentre altre scrivono in un account di archiviazione prima dell'[importazione in Log Analytics](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Soluzioni di monitoraggio
 Le [soluzioni di monitoraggio](../../azure-monitor/insights/solutions.md) raccolgono i dati per offrire informazioni dettagliate aggiuntive sul funzionamento di un determinato servizio o applicazione. Queste soluzioni raccolgono i dati nei log di Monitoraggio di Azure, dove possono essere analizzati usando il [linguaggio delle query](../../azure-monitor/log-query/log-query-overview.md) o le [visualizzazioni](view-designer.md) normalmente incluse nella soluzione.


## <a name="guest-operating-system"></a>Sistema operativo guest
Le risorse di calcolo in Azure, in altri cloud e in locale dispongono di un sistema operativo guest per il monitoraggio. Con l'installazione di uno o più agenti, è possibile raccogliere i dati di telemetria dal sistema operativo guest negli stessi strumenti di monitoraggio dei servizi di Azure.

![Raccolta di risorse di calcolo di Azure](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Estensione Diagnostica di Azure
L'estensione di diagnostica di Azure fornisce un livello di base del monitoraggio raccogliendo i log e i dati sulle prestazioni dal sistema operativo client delle risorse di calcolo di Azure.   

### <a name="log-analytics-agent"></a>Agente di Log Analytics
Il monitoraggio e la gestione completi delle macchine virtuali o del computer fisico Windows o Linux vengono forniti con l'agente di Log Analytics. La macchina virtuale può essere in esecuzione in Azure, in un altro cloud o in locale e l'agente si connette a Monitoraggio di Azure direttamente o tramite System Center Operations Manager e consente di raccogliere i dati da [origini dati](agent-data-sources.md) configurate o da [soluzioni di monitoraggio](../../azure-monitor/insights/solutions.md) che offrono informazioni dettagliate aggiuntive sulle applicazioni in esecuzione nella macchina virtuale.


### <a name="dependency-agent"></a>Dependency Agent
Le soluzioni [Mapping dei servizi](../insights/service-map.md) e [Monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md) richiedono un Dependency Agent nelle macchine virtuali Windows e Linux. Si integra con l'agente di Log Analytics per la raccolta dei dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze da processi esterni. Archivia questi dati in Monitoraggio di Azure e visualizza i componenti interconnessi individuati.  

Per comprendere meglio le differenze tra gli agenti e scegliere quale usare a seconda dei requisiti di monitoraggio, vedere [Panoramica degli agenti di monitoraggio](agents-overview.md).

## <a name="applications"></a>APPLICAZIONI
Oltre ai dati di telemetria che l'applicazione può scrivere nel sistema operativo guest, il monitoraggio dettagliato delle applicazioni viene eseguito con [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights raccoglie i dati da applicazioni in esecuzione su un'ampia gamma di piattaforme. L'applicazione può essere eseguita in Azure, un altro cloud o in locale.

![Raccolta di dati dell'applicazione](media/data-sources/application-collection.png)


### <a name="application-data"></a>Dati dell'applicazione
Quando si abilita Application Insights per un'applicazione mediante l'installazione di un pacchetto di strumentazione, questo servizio raccoglie le metriche e i log relativi alle prestazioni e al funzionamento dell'applicazione. Sono incluse le informazioni dettagliate sulle visualizzazioni di pagina, le richieste dell'applicazione e le eccezioni. Application Insights archivia i dati che raccoglie in Monitoraggio di Azure. Include strumenti estensivi per l'analisi dei dati, che tuttavia possono essere analizzati anche con dati provenienti da altre fonti tramite strumenti quali l'analisi delle metriche e dei log.

È anche possibile usare Application Insights per [creare una metrica personalizzata](../../application-insights/app-insights-api-custom-events-metrics.md).  In questo modo è possibile definire la propria logica per calcolare un valore numerico e quindi archiviarlo con altre metriche che possono essere accessibili dall'analisi delle metriche e usate per la [scalabilità automatica](autoscale-custom-metric.md) e gli avvisi delle metriche.


### <a name="dependencies"></a>Dependencies
Per monitorare operazioni logiche diverse di un'applicazione, è necessario [raccogliere i dati di telemetria tra più componenti](../../application-insights/app-insights-transaction-diagnostics.md). Application Insights supporta la [correlazione di dati di telemetria distribuita](../../application-insights/application-insights-correlation.md) che identifica le dipendenze tra componenti, consentendo in tal modo di analizzarli insieme.

### <a name="availability-tests"></a>Test della disponibilità
I [test di disponibilità](../../application-insights/app-insights-monitor-web-app-availability.md) in Application Insights consentono di verificare la disponibilità e velocità di risposta dell'applicazione da posizioni diverse nella rete Internet pubblica. È possibile effettuare un semplice test ping per verificare che l'applicazione sia attiva o usare Visual Studio per creare un test Web che simula uno scenario utente.  I test di disponibilità non richiedono alcuna strumentazione nell'applicazione.

## <a name="custom-sources"></a>Origini personalizzate
Oltre ai livelli standard di un'applicazione, potrebbe essere necessario monitorare altre risorse che contengono dati di telemetria che non possono essere raccolti con le altre origini dati. Per queste risorse è necessario scrivere tali dati tramite un'API di Monitoraggio di Azure.

![Raccolta di dati personalizzati](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API dell'Agente di raccolta dati
Monitoraggio di Azure può raccogliere dati di log da qualsiasi client REST tramite l'[API dell'agente di raccolta dati](../../azure-monitor/platform/data-collector-api.md). In questo modo è possibile creare scenari di monitoraggio personalizzati ed estendere il monitoraggio alle risorse che non espongono dati di telemetria tramite altre origini.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [tipi di dati di monitoraggio raccolti da Monitoraggio di Azure](data-platform.md) e su come visualizzare e analizzare i dati.
