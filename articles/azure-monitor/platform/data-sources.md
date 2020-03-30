---
title: Origini dei dati in Monitoraggio di Azure | Microsoft Docs
description: Descrive i dati disponibili per monitorare l'integrità e le prestazioni delle risorse di Azure e le applicazioni che si basano su tali dati.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479842"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Origini dei dati di monitoraggio per Monitoraggio di Azure
Monitoraggio di Azure si basa su una [piattaforma dati](data-platform.md) di monitoraggio comune che include [log](data-platform-logs.md) e [metriche.](data-platform-metrics.md) La raccolta di dati in questa piattaforma consente di analizzare insieme i dati di più risorse usando un set comune di strumenti in Monitoraggio di Azure.Collecting data into this platform allows data from multiple resources to be analyzed together using a common set of tools in Azure Monitor. I dati di monitoraggio possono anche essere inviati ad altre posizioni per supportare determinati scenari e alcune risorse possono scrivere in altre posizioni prima di poter essere raccolte in log o metriche.

Questo articolo descrive le diverse origini dei dati di monitoraggio raccolti da Monitoraggio di Azure oltre ai dati di monitoraggio creati dalle risorse di Azure.This article describes the different sources of monitoring data collected by Azure Monitor in addition to the monitoring data created by Azure resources. Vengono forniti collegamenti a informazioni dettagliate sulla configurazione necessarie per raccogliere questi dati in posizioni diverse.

## <a name="application-tiers"></a>Livelli di applicazione

Le origini dei dati di monitoraggio dalle applicazioni di Azure possono essere organizzate in livelli, i livelli più alti sono l'applicazione stessa e i livelli inferiori come componenti della piattaforma Azure.Sources of monitoring data from Azure applications can be organized into tiers, the highest tiers is your application itself and the lower tiers being components of Azure platform. Il metodo di accesso ai dati da ogni livello varia. I livelli applicazione sono riepilogati nella tabella seguente e le origini dei dati di monitoraggio in ogni livello vengono presentate nelle sezioni seguenti. Vedere [Monitoraggio dei percorsi dati in Azure](data-locations.md) per una descrizione di ogni posizione dati e per le modalità di accesso ai dati.


![Livelli di monitoraggioMonitoring tiers](../media/overview/overview.png)


### <a name="azure"></a>Azure
The following table briefly describes the application tiers that are specific to Azure. Seguendo il link per ulteriori dettagli su ciascuno nelle sezioni seguenti.

| Livello | Descrizione | Metodo di raccolta |
|:---|:---|:---|
| [Tenant di AzureAzure Tenant](#azure-tenant) | dati relativi al funzionamento dei servizi di Azure a livello di tenant, ad esempio Azure Active Directory. | Visualizzare i dati AAD nel portale o configurare la raccolta in Monitoraggio di Azure usando un'impostazione di diagnostica tenant. |
| [Sottoscrizione di AzureAzure subscription](#azure-subscription) | Dati correlati all'integrità e alla gestione dei servizi tra risorse nella sottoscrizione di Azure, ad esempio Resource Manager e Integrità dei servizi. | Visualizzare nel portale o configurare la raccolta in Monitoraggio di Azure usando un profilo di log. |
| [Risorse di Azure](#azure-resources) |  Dati sul funzionamento e sulle prestazioni di ogni risorsa di Azure.Data about the operation and performance of each Azure resource. | Metriche raccolte automaticamente, visualizzazione in Esplora metriche.<br>Configurare le impostazioni di diagnostica per raccogliere i log in Monitoraggio di Azure.Configure diagnostic settings to collect logs in Azure Monitor.<br>Soluzioni di monitoraggio e approfondimenti disponibili per un monitoraggio più dettagliato per tipi di risorse specifici. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, altro cloud o localeAzure, other cloud, or on-premises 
La tabella seguente descrive brevemente i livelli applicazione che possono trovarsi in Azure, in un altro cloud o in locale. Seguendo il link per ulteriori dettagli su ciascuno nelle sezioni seguenti.

| Livello | Descrizione | Metodo di raccolta |
|:---|:---|:---|
| [Sistema operativo (ospite)](#operating-system-guest) | Dati sul sistema operativo nelle risorse di calcolo. | Installare l'agente Log Analytics per raccogliere le origini dati client in Monitoraggio di Azure e nell'agente di dipendenza per raccogliere dipendenze che supportano Monitoraggio di Azure per le macchine virtuali.<br>Per le macchine virtuali di Azure, installare Estensione diagnostica di Azure per raccogliere log e metriche in Monitoraggio di Azure.For Azure virtual machines, install Azure Diagnostic Extension to collect logs and metrics into Azure Monitor. |
| [Codice applicazione](#application-code) | Dati sulle prestazioni e le funzionalità dell'applicazione e del codice effettivi, incluse le tracce delle prestazioni, i log dell'applicazione e i dati di telemetria dell'utente. | Instrumentare il codice per raccogliere dati in Application Insights. |
| [Origini personalizzate](#custom-sources) | Dati da servizi esterni o altri componenti o dispositivi. | Raccogliere i dati del log o delle metriche in Monitoraggio di Azure da qualsiasi client REST. |

## <a name="azure-tenant"></a>Tenant di Azure
I dati di telemetria correlati al tenant di Azure vengono raccolti da servizi a livello di tenant, ad esempio Azure Active Directory.

![Raccolta del tenant di Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Log di controllo di Azure Active Directory
I [report di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contengono la cronologia dell'attività di accesso e l'audit trail delle modifiche apportate all'interno di un tenant specifico. 

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | Configurare i log di Azure AD da raccogliere in Monitoraggio di Azure per analizzarli con altri dati di monitoraggio. | [Integrare i log di Azure AD con i log di Monitoraggio di Azure (anteprima)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Archiviazione di Azure | Esportare i log di Azure AD in Archiviazione di Azure per l'archiviazione. | [Esercitazione: Archiviare i log di Azure AD in un account di archiviazione di Azure (anteprima)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub eventi | Trasmettere i log di Azure AD in altre posizioni usando gli hub eventi. | [Esercitazione: trasmettere i log di Azure Active Directory a un hub eventi di Azure (anteprima).](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="azure-subscription"></a>Sottoscrizione di Azure
Telemetria correlata all'integrità e al funzionamento della sottoscrizione di Azure.Telemetry related to the health and operation of your Azure subscription.

![Sottoscrizione di Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Log attività di Azure 
Il log attività di Azure include i record di integrità del servizio insieme ai record in tutte le modifiche di configurazione apportate alle risorse nella sottoscrizione di Azure.The [Azure Activity log](platform-logs-overview.md) includes service health records along with records on any configuration changes made to the resources in your Azure subscription. Il log attività è disponibile per tutte le risorse di Azure e ne rappresenta la visualizzazione _esterna_.

| Destination | Descrizione | Riferimento |
|:---|:---|
| Log attività | Il log attività viene raccolto nel proprio archivio dati che è possibile visualizzare dal menu Monitoraggio di Azure o usare per creare avvisi del log attività. | [Eseguire una query nel log attività nel portale di AzureQuery the Activity log in the Azure portal](activity-log-view.md#azure-portal) |
| Log di Monitoraggio di Azure | Configurare i log di Monitoraggio di Azure per raccogliere il log attività per analizzarlo con altri dati di monitoraggio. | [Raccogliere e analizzare i log delle attività di Azure nell'area di lavoro Log Analytics in Monitoraggio di AzureCollect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](activity-log-collect.md) |
| Archiviazione di Azure | Esportare il log attività in Archiviazione di Azure per l'archiviazione. | [Registro attività archivio](resource-logs-collect-storage.md)  |
| Hub eventi | Trasmettere il log attività ad altre posizioni tramite hub eventi | [Registro attività flusso all'hub eventi](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Integrità dei servizi di Azure
[Integrità dei servizi di Azure](../../service-health/service-health-overview.md) presenta informazioni sull'integrità dei servizi di Azure nella sottoscrizione su cui si basano l'applicazione e le risorse in uso.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log attività<br>Log di Monitoraggio di Azure | I record di integrità del servizio vengono archiviati nel log attività di Azure, pertanto è possibile visualizzarli nel portale di Azure o eseguire altre attività che è possibile eseguire con il log attività. | [Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure](service-notifications.md) |


## <a name="azure-resources"></a>Risorse di Azure
Le metriche e i log delle risorse forniscono informazioni sul funzionamento interno delle risorse di Azure.Metrics and resource logs provide information about the _internal_ operation of Azure resources. Sono disponibili per la maggior parte dei servizi di Azure e per il monitoraggio di soluzioni e informazioni dettagliate vengono raccolti dati aggiuntivi per servizi specifici.

![Raccolta di risorse di Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Metriche della piattaforma 
La maggior parte dei servizi di Azure invierà [le metriche](data-platform-metrics.md) della piattaforma che riflettono le prestazioni e il funzionamento direttamente al database delle metriche. Le [metriche specifiche variano in base al tipo di risorsa](metrics-supported.md). 

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Le metriche della piattaforma verranno scritte nel database delle metriche di Monitoraggio di Azure senza alcuna configurazione. Accedere alle metriche della piattaforma da Esplora metriche.  | [Introduzione a Esplora metriche di Azure](metrics-getting-started.md)<br>[Metriche supportate con il monitoraggio di Azure](metrics-supported.md) |
| Log di Monitoraggio di Azure | Copiare le metriche della piattaforma nei log per le tendenze e altre analisi usando Log Analytics.Copy platform metrics to Log for trending and other analysis using Log Analytics. | [Da Diagnostica di Azure direttamente a Log Analytics](resource-logs-collect-workspace.md) |
| Hub eventi | Eseguire lo streaming delle metriche in altre posizioni tramite hub eventi. |[Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Log risorse
[I log delle risorse](platform-logs-overview.md) forniscono informazioni dettagliate sul funzionamento interno di una risorsa di Azure.Resource logs provide insights into the _internal_ operation of an Azure resource.  I log delle risorse vengono creati automaticamente, ma è necessario creare un'impostazione di diagnostica per specificare una destinazione da raccogliere per ogni risorsa.

I requisiti di configurazione e il contenuto dei log delle risorse variano in base al tipo di risorsa e non tutti i servizi li creano ancora. Per informazioni dettagliate su ogni servizio e collegamenti a procedure di configurazione dettagliate, vedere Servizi, schemi e categorie supportati per i log delle risorse di [Azure.See Supported services, schemas, and categories for Azure resource logs](diagnostic-logs-schema.md) for details on each service and links to detailed configuration procedures. Se il servizio non è elencato in questo articolo, il servizio non crea attualmente i log delle risorse.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | Inviare i log delle risorse ai log di Monitoraggio di Azure per l'analisi con altri dati di log raccolti. | [Raccogliere i log delle risorse di Azure nell'area di lavoro di Log Analytics in Monitoraggio di AzureCollect Azure resource logs in Log Analytics workspace in Azure Monitor](resource-logs-collect-storage.md) |
| Archiviazione | Inviare log delle risorse ad Archiviazione di Azure per l'archiviazione. | [Archiviare i log delle risorse di AzureArchive Azure resource logs](resource-logs-collect-workspace.md) |
| Hub eventi | Eseguire il flusso dei log delle risorse in altre posizioni usando gli hub eventi. |[Trasmettere i log delle risorse di Azure a un hub eventi](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operativo (ospite)
Le risorse di calcolo in Azure, in altri cloud e in locale dispongono di un sistema operativo guest per il monitoraggio. Con l'installazione di uno o più agenti, è possibile raccogliere dati di telemetria dal guest in Monitoraggio di Azure per analizzarli con gli stessi strumenti di monitoraggio dei servizi di Azure stessi.

![Raccolta di risorse di calcolo di Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Estensione Diagnostica di Azure
L'abilitazione dell'estensione Diagnostica di Azure per le macchine virtuali di Azure consente di raccogliere log e metriche dal sistema operativo guest delle risorse di calcolo di Azure, inclusi i ruoli Web e di lavoro di Azure, le macchine virtuali, le macchine virtuali scale e Service Fabric.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Archiviazione | L'estensione di diagnostica di Azure scrive sempre in un account di archiviazione di Azure.Azure diagnostics extension always writes to an Azure Storage account. | [Installare e configurare l'estensione di diagnostica di Windows Azure (WAD)Install and configure Windows Azure diagnostics extension (WAD)](diagnostics-extension-windows-install.md)<br>[Usare l'estensione Diagnostica per Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Quando si configura l'estensione Diagnostica per raccogliere i contatori delle prestazioni, questi vengono scritti nel database delle metriche di Monitoraggio di Azure.When you configure the Diagnostics Extension to collect performance counters, they are written to the Azure Monitor metrics database. | [Inviare metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per una macchina virtuale WindowsSend Guest OS metrics to the Azure Monitor metric store using a Resource Manager template for a Windows virtual machine](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Hub eventi | Configurare l'estensione di diagnostica per trasmettere i dati ad altre posizioni usando gli hub eventi.  | [Streaming Azure Diagnostics data by using Event Hubs](diagnostics-extension-stream-event-hubs.md)<br>[Usare l'estensione Diagnostica per Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) |
| Registri di Application Insights | Raccogliere i log e i contatori delle prestazioni dalla risorsa di calcolo che supporta l'applicazione da analizzare con altri dati dell'applicazione. | [Inviare i dati di diagnostica del servizio Cloud, della macchina virtuale o di Service Fabric ad Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente di Log Analytics 
Installare l'agente log Analytics per il monitoraggio e la gestione completi delle macchine virtuali Windows o Linux.Install the Log Analytics agent for comprehensive monitoring and management of your Windows or Linux virtual machines. La macchina virtuale può essere eseguita in Azure, un altro cloud o in locale.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | L'agente di Log Analytics si connette ad Monitoraggio di Azure direttamente o tramite System Center Operations Manager e consente di raccogliere dati da origini dati configurate o da soluzioni di monitoraggio che forniscono informazioni aggiuntive sulle applicazioni in esecuzione sulla macchina virtuale. | [Origini dati degli agenti in Monitoraggio di Azure](agent-data-sources.md)<br>[Connettere Operations Manager ad Monitoraggio di AzureConnect Operations Manager to Azure Monitor](om-agents.md) |
| Archiviazione VM | Monitoraggio di Azure per le macchine virtuali usa l'agente log Analytics per archiviare le informazioni sullo stato di riscaldamento in una posizione personalizzata. Per altre informazioni, vedere la sezione seguente.  |


### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali 
[Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) offre un'esperienza di monitoraggio personalizzata per le macchine virtuali che forniscono funzionalità che vanno oltre la funzionalità di base di Monitoraggio di Azure.Azure Monitor for VMs provides a customized monitoring experience for virtual machines providing features beyond core Azure Monitor functionality. Richiede un agente di dipendenze nelle macchine virtuali Windows e Linux che si integra con l'agente di Log Analytics per raccogliere i dati individuati sui processi in esecuzione nella macchina virtuale e le dipendenze dei processi esterni.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | Archivia i dati relativi ai processi e alle dipendenze nell'agente. | [Uso di Monitoraggio di Azure per macchine virtuali (anteprima) Mappa per comprendere i componenti dell'applicazioneUsing Azure Monitor for VMs (preview) Map to understand application components](../insights/vminsights-maps.md) |



## <a name="application-code"></a>codice dell'applicazione
Il monitoraggio dettagliato delle applicazioni in Monitoraggio di Azure viene eseguito con [Application Insights](https://docs.microsoft.com/azure/application-insights/) che raccoglie dati dalle applicazioni in esecuzione su un'ampia gamma di piattaforme. L'applicazione può essere eseguita in Azure, un altro cloud o in locale.

![Raccolta di dati dell'applicazione](media/data-sources/applications.png)


### <a name="application-data"></a>Dati dell'applicazione
Quando si abilita Application Insights per un'applicazione mediante l'installazione di un pacchetto di strumentazione, questo servizio raccoglie le metriche e i log relativi alle prestazioni e al funzionamento dell'applicazione. Application Insights archivia i dati raccolti nella stessa piattaforma dati di Monitoraggio di Azure usata da altre origini dati. It includes extensive tools for analyzing this data, but you can also analyze it with data from other sources using tools such as Metrics Explorer and Log Analytics.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | Dati operativi relativi all'applicazione, incluse visualizzazioni di pagina, richieste di applicazioni, eccezioni e tracce. | [Analizzare i dati di log in Monitoraggio di Azure](../log-query/log-query-overview.md) |
|                    | Informazioni sulle dipendenze tra i componenti dell'applicazione per supportare la mappa dell'applicazione e la correlazione di telemetria. | [Correlazione di dati di telemetria in Application Insights](../app/correlation.md) <br> [Mappa dell'applicazione](../app/app-map.md) |
|            | Risultati dei test di disponibilità che testano la disponibilità e la velocità di risposta dell'applicazione da posizioni diverse su Internet pubblico. | [Monitorare la disponibilità e la velocità di risposta dei siti Web](../app/monitor-web-app-availability.md) |
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Application Insights raccoglie le metriche che descrivono le prestazioni e il funzionamento dell'applicazione, oltre alle metriche personalizzate definite nell'applicazione nel database delle metriche di Monitoraggio di Azure.Application Insights collects metrics describing the performance and operation of the application in addition to custom metrics that you define in your application into the Azure Monitor metrics database. | [Metriche basate su log e metriche preaggregate in Azure Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API di Application Insights per metriche ed eventi personalizzati](../app/api-custom-events-metrics.md) |
| Archiviazione di Azure | Inviare i dati dell'applicazione ad Archiviazione di Azure per l'archiviazione. | [Esportare i dati di telemetria da Application Insights](../app/export-telemetry.md) |
|            | I dettagli dei test di disponibilità vengono archiviati in Archiviazione di Azure.Details of availability tests are stored in Azure Storage. Usare Application Insights nel portale di Azure per scaricare l'analisi locale. I risultati dei test di disponibilità vengono archiviati nei log di Monitoraggio di Azure.Results of availability tests are stored in Azure Monitor Logs. | [Monitorare la disponibilità e la velocità di risposta dei siti Web](../app/monitor-web-app-availability.md) |
|            | Profiler trace data is stored in Azure Storage. Usare Application Insights nel portale di Azure per scaricare l'analisi locale.  | [Profilare le applicazioni di produzione in Azure con Application Insights](../app/profiler-overview.md) 
|            | Debug snapshot data that is captured for a subset of exceptions is stored in Azure Storage. Usare Application Insights nel portale di Azure per scaricare l'analisi locale.  | [Funzionamento degli snapshot](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluzioni di monitoraggio e approfondimenti
[Soluzioni di monitoraggio](../insights/solutions.md) e [Informazioni dettagliate](../insights/insights-overview.md) raccolgono dati per fornire informazioni aggiuntive sul funzionamento di un determinato servizio o applicazione. Possono indirizzare le risorse in diversi livelli applicazione e anche più livelli.

### <a name="monitoring-solutions"></a>Soluzioni di monitoraggio

| Destination | Descrizione | Riferimento
|:---|:---|:---|
| Log di Monitoraggio di Azure | Le soluzioni di monitoraggio raccolgono dati nei log di Monitoraggio di Azure in cui possono essere analizzati usando il linguaggio di query o [le viste](view-designer.md) in genere incluse nella soluzione. | [Dettagli di raccolta dati per il monitoraggio delle soluzioni in AzureData collection details for monitoring solutions in Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
[Monitoraggio di Azure per i contenitori](../insights/container-insights-overview.md) offre un'esperienza di monitoraggio personalizzata per il [servizio Azure Kubernetes (AKS).](/azure/aks/) Raccoglie dati aggiuntivi su queste risorse descritte nella tabella seguente.

| Destination | Descrizione | Riferimento |
|:---|:---|:---|
| Log di Monitoraggio di Azure | Archivia i dati di monitoraggio per AKS, inclusi inventario, log ed eventi. I dati delle metriche vengono archiviati anche nei log per sfruttare le funzionalità di analisi nel portale. | [Conoscere le prestazioni del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori](../insights/container-insights-analyze.md) |
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | I dati delle metriche vengono archiviati nel database delle metriche per aumentare la visualizzazione e gli avvisi. | [Visualizzare le metriche del contenitore in Esplora metricheView container metrics in metrics explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Servizio Azure Kubernetes | Fornisce accesso diretto ai log del contenitore del servizio Azure Kubernetes (AKS) (stdout/stderror), agli eventi e alle metriche pod nel portale. | [Come visualizzare i log, gli eventi e le metriche dei pod di Kubernetes in tempo reale](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) offre un'esperienza personalizzata per il monitoraggio delle macchine virtuali. Una descrizione dei dati raccolti da Monitoraggio di Azure per le macchine virtuali è inclusa nella sezione [Sistema operativo (ospite)](#operating-system-guest) precedente.

## <a name="custom-sources"></a>Origini personalizzate
Oltre ai livelli standard di un'applicazione, potrebbe essere necessario monitorare altre risorse che contengono dati di telemetria che non possono essere raccolti con le altre origini dati. Per queste risorse, scrivere questi dati in Metriche o Log usando un'API di monitoraggio di Azure.For these resources, write these data to either Metrics or Logs using an Azure Monitor API.

![Raccolta personalizzata](media/data-sources/custom.png)

| Destination | Metodo | Descrizione | Riferimento |
|:---|:---|:---|:---|
| Log di Monitoraggio di Azure | API dell'Agente di raccolta dati | Raccogliere i dati di log da qualsiasi client REST e archiviare nell'area di lavoro di Log Analytics.Collect log data from any REST client and store in Log Analytics workspace. | [Inviare dati di log a Monitoraggio di Azure con l'API di raccolta dati HTTP (anteprima pubblica)](data-collector-api.md) |
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | API metriche personalizzate | Raccogliere i dati delle metriche da qualsiasi client REST e archiviare nel database delle metriche di Monitoraggio di Azure.Collect metric data from any REST client and store in Azure Monitor metrics database. | [Inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di Monitoraggio di Azure usando un'API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Altri servizi
Altri servizi in Azure scrivono i dati nella piattaforma dati di Monitoraggio di Azure.Other services in Azure write data to the Azure Monitor data platform. In questo modo è possibile analizzare i dati raccolti da questi servizi con i dati raccolti da Monitoraggio di Azure e sfruttare gli stessi strumenti di analisi e visualizzazione.

| Service | Destination | Descrizione | Riferimento |
|:---|:---|:---|:---|
| [Centro sicurezza di AzureAzure Security Center](/azure/security-center/) | Log di Monitoraggio di Azure | Azure Security Center stores the security data it collects in a Log Analytics workspace which allows it to be analyzed with other log data collected by Azure Monitor.  | [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md) |
| [Sentinella di Azure](/azure/sentinel/) | Log di Monitoraggio di Azure | Azure Sentinel stores the data it collects from different data sources in a Log Analytics workspace which allows it to be analyzed with other log data collected by Azure Monitor.  | [Connettere le origini dati](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [tipi di dati di monitoraggio raccolti da Monitoraggio di Azure](data-platform.md) e su come visualizzare e analizzare i dati.
- Elencare le [diverse posizioni in cui le risorse](data-locations.md) di Azure archiviano i dati e come è possibile accedervi. 
