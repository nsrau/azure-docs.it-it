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
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357432"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Origini dei dati di monitoraggio per monitoraggio di Azure
Monitoraggio di Azure si basa su un [piattaforma dati di monitoraggio più comuni](data-platform.md) che include [registri](data-platform-logs.md) e [metriche](data-platform-metrics.md). La raccolta di dati in questa piattaforma consente i dati da più risorse da analizzare tra loro tramite un set di strumenti comune in Monitoraggio di Azure. I dati di monitoraggio possono essere inviati anche ad altre posizioni per supportare determinati scenari e alcune risorse possono scrivere in altri percorsi prima di poter essere raccolti in log o metriche.

Questo articolo descrive le diverse origini dei dati raccolti da monitoraggio di Azure oltre i dati di monitoraggio creati dalle risorse di Azure di monitoraggio. Vengono forniti collegamenti a informazioni dettagliate sulla configurazione necessaria per raccogliere i dati in posizioni diverse.

## <a name="application-tiers"></a>Livelli di applicazione

Origini di monitoraggio dei dati dalle applicazioni Azure possono essere organizzate in livelli, i livelli più elevati in corso l'applicazione stessa e i livelli inferiori in corso dei componenti della piattaforma Azure. Il metodo di accesso ai dati da ogni livello varia. Nella tabella seguente sono riepilogati i livelli di applicazione e le origini dei dati in ogni livello di monitoraggio vengono presentate nelle sezioni seguenti. Visualizzare [monitoraggio percorsi dei dati in Azure](data-locations.md) per una descrizione di ogni percorso dati e come è possibile accedere ai dati.


![Livelli di monitoraggio](../media/overview/overview.png)


### <a name="azure"></a>Azure
Nella tabella seguente descrive brevemente i livelli di applicazione specifici in Azure. Seguendo il collegamento per ulteriori dettagli su ogni nelle sezioni seguenti.

| Livello | Descrizione | Metodo di raccolta |
|:---|:---|:---|
| [Tenant di Azure](#azure-tenant) | i dati relativi al funzionamento dei servizi di Azure a livello di tenant, ad esempio Azure Active Directory. | Visualizzare i dati AAD nel portale o configurare la raccolta da monitoraggio di Azure usando un'impostazione di diagnostica del tenant. |
| [Sottoscrizione di Azure](#azure-subscription) | Dati relativi all'integrità e gestione dei servizi di più risorse nella sottoscrizione di Azure, ad esempio Resource Manager e l'integrità del servizio. | Consente di visualizzare nel portale o configurare la raccolta da monitoraggio di Azure usando un profilo di log. |
| [Risorse di Azure](#azure-resources) |  Dati relativi al funzionamento e le prestazioni di ogni risorsa di Azure. | Le metriche raccolte automaticamente, visualizzare in Esplora metriche.<br>Configurare le impostazioni di diagnostica per raccogliere i log in Monitoraggio di Azure.<br>Monitoraggio delle soluzioni e informazioni dettagliate disponibili per il monitoraggio più dettagliato per i tipi di risorse specifico. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, altri cloud o in locale 
Nella tabella seguente descrive brevemente i livelli di applicazione che potrebbero essere in Azure, un altro cloud o in locale. Seguendo il collegamento per ulteriori dettagli su ogni nelle sezioni seguenti.

| Livello | Descrizione | Metodo di raccolta |
|:---|:---|:---|
| [Sistema operativo (guest)](#operating-system-guest) | Dati relativi al sistema operativo in risorse di calcolo. | Installare l'agente di Log Analitica per raccogliere le origini dati client in Monitoraggio di Azure e Dependency agent per raccogliere le dipendenze che supportano il monitoraggio di Azure per le macchine virtuali.<br>Per macchine virtuali di Azure, installare l'estensione diagnostica di Azure per raccogliere i log e metriche in Monitoraggio di Azure. |
| [Codice dell'applicazione](#application-code) | Dati sulle prestazioni e funzionalità dell'applicazione effettivo e del codice, tra cui le analisi delle prestazioni, registri applicazioni e dati di telemetria utente. | Instrumentare il codice per raccogliere i dati in Application Insights. |
| [Origini personalizzate](#custom-sources) | Dati da servizi esterni o altri componenti o dispositivi. | Raccogliere dati di log o metriche in Monitoraggio di Azure da qualsiasi client REST. |

## <a name="azure-tenant"></a>Tenant di Azure
I dati di telemetria correlati al tenant di Azure vengono raccolti da servizi a livello di tenant, ad esempio Azure Active Directory.

![Raccolta del tenant di Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Log di controllo di Azure Active Directory
I [report di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contengono la cronologia dell'attività di accesso e l'audit trail delle modifiche apportate all'interno di un tenant specifico. 

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | Configurare i log di Azure AD da raccogliere in Monitoraggio di Azure per analizzare le con altri dati di monitoraggio. | [Integrare i log di Azure AD con i log di monitoraggio di Azure (anteprima)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Archiviazione di Azure | Esportare i log di Azure AD archiviazione di Azure per l'archiviazione. | [Esercitazione: Archiviare i log di Azure AD a un account di archiviazione di Azure (anteprima)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub eventi | Stream in altri percorsi di hub eventi di log di Azure AD. | [Esercitazione: Log di Azure Active Directory a un hub eventi di Azure (anteprima) di Stream](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Sottoscrizione di Azure
Dati di telemetria relativi all'integrità e l'operazione della sottoscrizione di Azure.

![Sottoscrizione di Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Log attività di Azure 
Il [log attività Azure](activity-logs-overview.md) include record del servizio integrità insieme ai record di modifiche alla configurazione apportate alle risorse nella sottoscrizione di Azure. Il log attività è disponibile per tutte le risorse di Azure e ne rappresenta la visualizzazione _esterna_.

| Destination | Descrizione | Riferimenti |
|:---|:---|
| Log attività | Il log attività verrà raccolti in un proprio archivio dati che è possibile visualizzare dal menu di monitoraggio di Azure o usare per creare avvisi del log attività. | [Eseguire query sul log attività nel portale di Azure](activity-log-view.md#azure-portal) |
| Log di monitoraggio di Azure | Configurare i log di monitoraggio di Azure per raccogliere i log attività per l'analisi con altri dati di monitoraggio. | [Raccogliere e analizzare i log attività di Azure nell'area di lavoro di Log Analitica in Monitoraggio di Azure](activity-log-collect.md) |
| Archiviazione di Azure | Esportare il log attività in archiviazione di Azure per l'archiviazione. | [Archiviare il log attività](activity-log-export.md#archive-activity-log)  |
| Hub eventi | Il log attività in altri percorsi di hub eventi di Stream | [Log di attività a Hub eventi di Stream](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Integrità dei servizi di Azure
[Integrità dei servizi di Azure](../../service-health/service-health-overview.md) presenta informazioni sull'integrità dei servizi di Azure nella sottoscrizione su cui si basano l'applicazione e le risorse in uso.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log attività<br>Log di monitoraggio di Azure | I record di integrità del servizio vengono archiviati nel log attività di Azure, pertanto è possibile visualizzarli nel portale di Azure o eseguire tutte le altre attività che eseguibili con il log attività. | [Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure](service-notifications.md) |


## <a name="azure-resources"></a>Risorse di Azure
I log di diagnostica a livello di metrica e di risorsa offrono informazioni sul funzionamento _interno_ delle risorse di Azure. Questi sono disponibili per servizi di Azure più e monitoraggio di soluzioni e informazioni dettagliate raccolgono dati aggiuntivi per determinati servizi.

![Raccolta di risorse di Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Metriche della piattaforma 
Servizi di Azure più invierà [metriche piattaforma](data-platform-metrics.md) che riflettono le prestazioni e funzionamento direttamente al database di metriche. Le [metriche specifiche variano in base al tipo di risorsa](metrics-supported.md). 

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Metriche di monitoraggio di Azure | Le metriche della piattaforma verranno scritto nel database di metriche di monitoraggio di Azure senza alcuna configurazione. Accedere alle metriche di piattaforma da Esplora metriche.  | [Introduzione a Esplora metriche di Azure](metrics-getting-started.md)<br>[Metriche supportate con il monitoraggio di Azure](metrics-supported.md) |
| Log di monitoraggio di Azure | Copiare le metriche della piattaforma per i log per identificare le tendenza e altre analisi usando Log Analitica. | [Diagnostica di Azure direttamente a Log Analitica](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Hub eventi | Metriche di Stream in altre posizioni usando gli hub eventi. |[Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Log di diagnostica
[I log di diagnostica](diagnostic-logs-overview.md) offrono informazioni approfondite le _interna_ funzionamento di una risorsa di Azure.  I log di diagnostica non sono abilitati per impostazione predefinita. È necessario abilitarle e specificare una destinazione per ogni risorsa. 

I requisiti di configurazione e il contenuto dei log di diagnostica variano in base al tipo di risorsa e non tutti i servizi ancora creano log di diagnostica. Visualizzare [supportati servizi, schemi e categorie per i log di diagnostica di Azure](diagnostic-logs-schema.md) per informazioni dettagliate su ogni servizio e i collegamenti alle procedure di configurazione dettagliati. Se il servizio non è elencato in questo articolo, tale servizio non viene attualmente scritta su log di diagnostica.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | Inviare i log di diagnostica per i log di monitoraggio di Azure per l'analisi con altri dati di log raccolti. Alcune risorse possono scrivere direttamente al monitoraggio di Azure mentre altri scrivere in un account di archiviazione prima di importarlo in un'area di lavoro di Log Analitica. | [Log di diagnostica Azure Stream all'area di lavoro di Log Analitica in Monitoraggio di Azure](diagnostic-logs-stream-log-store.md)<br>[Usare il portale di Azure per raccogliere log da archiviazione di Azure](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Archiviazione | Diagnostica di inviare i log in archiviazione di Azure per l'archiviazione. | [Archiviare i log di diagnostica di Azure](archive-diagnostic-logs.md) |
| Hub eventi | Log di diagnostica di Stream in altre posizioni usando gli hub eventi. |[Log di diagnostica Azure Stream a un hub eventi](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operativo (guest)
Le risorse di calcolo in Azure, in altri cloud e in locale dispongono di un sistema operativo guest per il monitoraggio. Con l'installazione di uno o più agenti, è possibile raccogliere i dati di telemetria dal guest in Monitoraggio di Azure per l'analisi con gli stessi strumenti di monitoraggio di servizi di Azure se stessi.

![Raccolta di risorse di calcolo di Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Estensione Diagnostica di Azure
Abilitazione dell'estensione diagnostica di Azure per macchine virtuali di Azure consente di per raccogliere i log e metriche dal sistema operativo guest di Azure le risorse tra cui il servizio Cloud di Azure (versione classica) Web e ruoli di lavoro, le macchine virtuali, macchine virtuali di calcolo set di scalabilità e Service Fabric.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Archiviazione | Quando si abilita l'estensione di diagnostica, verrà scritta in un account di archiviazione per impostazione predefinita. | [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](diagnostics-extension-to-storage.md) |
| Metriche di monitoraggio di Azure | Quando si configura l'estensione di diagnostica per raccogliere i contatori delle prestazioni, essi vengono scritti nel database di metriche di monitoraggio di Azure. | [Inviare le metriche del sistema operativo Guest per la metrica di monitoraggio di Azure archiviano usando un modello di Resource Manager per una macchina virtuale Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Log di Application Insights | Raccogliere i log e contatori delle prestazioni dalla risorsa di calcolo che supportano l'applicazione per essere analizzati con altri dati dell'applicazione. | [Inviare i dati di diagnostica del servizio Cloud, macchine virtuali o Service Fabric ad Application Insights](diagnostics-extension-to-application-insights.md) |
| Hub eventi | Configurare l'estensione di diagnostica per trasmettere i dati in altre posizioni usando gli hub eventi.  | [Flusso di dati di diagnostica di Azure nel percorso critico tramite hub eventi](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agente di Log Analytics 
Installare l'agente di Log Analitica per il monitoraggio completo e la gestione delle macchine virtuali Windows o Linux. La macchina virtuale può essere eseguita in Azure, un altro cloud o in locale.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | L'agente di Log Analitica si connette ad Azure monitorare direttamente o tramite System Center Operations Manager e consente di raccogliere i dati da origini dati configurate o dal monitoraggio delle soluzioni che forniscono informazioni dettagliate aggiuntive sulle applicazioni in esecuzione nella macchina virtuale. | [Origini dati dell'agente in Monitoraggio di Azure](agent-data-sources.md)<br>[Connettere Operations Manager a monitoraggio di Azure](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali 
[Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) offre un'esperienza di monitoraggio personalizzata per le macchine virtuali che fornisce funzionalità oltre a funzionalità di monitoraggio di Azure di base, tra cui lo stato del servizio e l'integrità della macchina virtuale. Richiede un Dependency Agent nelle macchine virtuali Windows e Linux che si integra con l'agente di Log Analitica per raccogliere i dati individuati sui processi in esecuzione nella macchina virtuale e le dipendenze di processo esterno.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | Archivia i dati sui processi e le dipendenze dell'agente. | [Usare monitoraggio di Azure per le macchine virtuali (anteprima) della mappa per comprendere i componenti dell'applicazione](../insights/vminsights-maps.md) |
| Archiviazione della macchina virtuale | Monitoraggio per le macchine virtuali di Azure archivia le informazioni sullo stato di integrità in un percorso personalizzato. Questo è disponibile solo per il monitoraggio di Azure per le macchine virtuali nel portale di Azure oltre al [integrità risorse di Azure REST API](/rest/api/resourcehealth/). | [Comprendere l'integrità delle macchine virtuali di Azure](../insights/vminsights-health.md)<br>[Integrità risorse di Azure l'API REST](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Codice dell'applicazione
Il monitoraggio dettagliato delle applicazioni in Monitoraggio di Azure, usare [Application Insights](https://docs.microsoft.com/azure/application-insights/) che raccoglie i dati dalle applicazioni eseguite in un'ampia gamma di piattaforme. L'applicazione può essere eseguita in Azure, un altro cloud o in locale.

![Raccolta di dati dell'applicazione](media/data-sources/applications.png)


### <a name="application-data"></a>Dati dell'applicazione
Quando si abilita Application Insights per un'applicazione mediante l'installazione di un pacchetto di strumentazione, questo servizio raccoglie le metriche e i log relativi alle prestazioni e al funzionamento dell'applicazione. Application Insights archivia i dati raccolti nella stessa piattaforma di dati di monitoraggio di Azure usata da altre origini dati. Include strumenti estensivi per l'analisi dei dati, ma consente anche di analizzare i dati da altre origini usando strumenti come Esplora metriche e Log Analitica.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | Dati operativi relativi all'applicazione, tra cui le visualizzazioni pagina, le richieste dell'applicazione, le eccezioni e tracce. | [Analizzare i dati di log in Monitoraggio di Azure](../log-query/log-query-overview.md) |
|                    | Informazioni sulle dipendenze tra componenti dell'applicazione per supportare mappa delle applicazioni e la correlazione di dati di telemetria. | [Correlazione di dati di telemetria in Application Insights](../app/correlation.md) <br> [Mappa delle applicazioni](../app/app-map.md) |
|            | Risultati di test di disponibilità che verificano la disponibilità e velocità di risposta dell'applicazione da diverse posizioni nella rete Internet pubblica. | [Monitorare la disponibilità e la velocità di risposta dei siti Web](../app/monitor-web-app-availability.md) |
| Metriche di monitoraggio di Azure | Application Insights raccoglie le metriche che descrivono le prestazioni e funzionamento dell'applicazione oltre a metriche personalizzate definite nell'applicazione nel database di metriche di monitoraggio di Azure. | [Pre-aggregate e basato su log con le metriche in Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API di Application Insights per metriche ed eventi personalizzati](../app/api-custom-events-metrics.md) |
| Archiviazione di Azure | Inviare i dati dell'applicazione ad archiviazione di Azure per l'archiviazione. | [Esportare i dati di telemetria da Application Insights](../app/export-telemetry.md) |
|            | Dettagli del test di disponibilità vengono archiviati in archiviazione di Azure. Usare Application Insights nel portale di Azure per il download per analisi in locale. Risultati di test di disponibilità vengono archiviati nei log di monitoraggio di Azure. | [Monitorare la disponibilità e la velocità di risposta dei siti Web](../app/monitor-web-app-availability.md) |
|            | I dati di traccia di Profiler viene archiviati in archiviazione di Azure. Usare Application Insights nel portale di Azure per il download per analisi in locale.  | [Profilare le applicazioni di produzione in Azure con Application Insights](../app/profiler-overview.md) 
|            | Eseguire il debug di snapshot dei dati acquisiti per un subset di eccezioni viene archiviati in archiviazione di Azure. Usare Application Insights nel portale di Azure per il download per analisi in locale.  | [Come funzionano gli snapshot](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluzioni di monitoraggio e informazioni dettagliate
[Monitoraggio delle soluzioni](../insights/solutions.md) e [Insights](../insights/insights-overview.md) raccogliere i dati per fornire informazioni aggiuntive dettagliate sul funzionamento di un'applicazione o un servizio specifico. Si possono fare riferimento alle risorse in diversi livelli dell'applicazione e anche più livelli.

### <a name="monitoring-solutions"></a>Soluzioni di monitoraggio

| Destination | Descrizione | Riferimenti
|:---|:---|:---|
| Log di monitoraggio di Azure | Soluzioni di monitoraggio di raccolgono i dati in log di monitoraggio di Azure in cui è possibile analizzarlo usando il linguaggio di query oppure [viste](view-designer.md) che in genere sono incluse nella soluzione. | [Informazioni dettagliate sulla raccolta dei dati per il monitoraggio delle soluzioni in Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
[Monitoraggio di Azure per contenitori](../insights/container-insights-overview.md) offre un'esperienza di monitoraggio personalizzata per [Azure Kubernetes Service (AKS)](/azure/aks/). Raccoglie i dati aggiuntivi relativi a queste risorse descritte nella tabella seguente.

| Destination | Descrizione | Riferimenti |
|:---|:---|:---|
| Log di monitoraggio di Azure | Archivi dati di monitoraggio per AKS inclusi inventario, i log ed eventi. Dati delle metriche vengono archiviati anche nei log per sfruttare le funzionalità di analisi nel portale. | [Conoscere le prestazioni del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori](../insights/container-insights-analyze.md) |
| Metriche di monitoraggio di Azure | I dati di metrica vengono archiviati nel database alla visualizzazione di unità e gli avvisi delle metriche. | [Visualizzare le metriche di contenitore in Esplora metriche](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Servizio Azure Kubernetes | In ordine esperienza quasi in tempo reale, monitoraggio di Azure per contenitori presenta i dati direttamente dal servizio Kubernetes di Azure nel portale di Azure. | [Come visualizzare i log dei contenitori in tempo reale con Monitoraggio di Azure per contenitori (anteprima)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) offre un'esperienza personalizzata per il monitoraggio delle macchine virtuali. Una descrizione dei dati raccolti da monitoraggio di Azure per le macchine virtuali è incluso nel [sistema operativo (guest)](#operating-system-guest) sezione precedente.

## <a name="custom-sources"></a>Origini personalizzate
Oltre ai livelli standard di un'applicazione, potrebbe essere necessario monitorare altre risorse che contengono dati di telemetria che non possono essere raccolti con le altre origini dati. Per queste risorse, scrivere i dati per le metriche o log tramite un'API di monitoraggio di Azure.

![Raccolta personalizzata](media/data-sources/custom.png)

| Destination | Metodo | Descrizione | Riferimenti |
|:---|:---|:---|:---|
| Log di monitoraggio di Azure | API dell'Agente di raccolta dati | Raccogliere i dati di log da qualsiasi client REST e archiviare nell'area di lavoro di Log Analitica. | [Inviare i dati di log per il monitoraggio di Azure con l'API di raccolta dati HTTP (anteprima pubblica)](data-collector-api.md) |
| Metriche di monitoraggio di Azure | API di metriche personalizzate | Raccogliere i dati di metrica da qualsiasi client REST e archiviarlo nel database di metriche di monitoraggio di Azure. | [Inviare metriche personalizzate per una risorsa di Azure nell'archivio delle metriche di monitoraggio di Azure usando un'API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Altri servizi
Altri servizi di Azure scrivono i dati per la piattaforma di dati di monitoraggio di Azure. Ciò consente di analizzare i dati raccolti da questi servizi con i dati raccolti da monitoraggio di Azure e sfruttare la stessa analisi e strumenti di visualizzazione.

| Service | Destination | Descrizione | Riferimenti |
|:---|:---|:---|:---|
| [Centro sicurezza di Azure](/azure/security-center/) | Log di monitoraggio di Azure | Centro sicurezza di Azure archivia i dati di sicurezza che raccolti in un'area di lavoro di Log Analitica che consente di disporre di altri dati di log raccolti da monitoraggio di Azure.  | [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Log di monitoraggio di Azure | Sentinel Azure archivia i dati che raccolti da diverse origini dati in un'area di lavoro di Log Analitica che consente di disporre di altri dati di log raccolti da monitoraggio di Azure.  | [Connettere origini dati](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [tipi di dati di monitoraggio raccolti da Monitoraggio di Azure](data-platform.md) e su come visualizzare e analizzare i dati.
- Elenco di [posizioni diverse risorse di Azure in cui archiviano i dati](data-locations.md) e come è possibile accedervi. 
