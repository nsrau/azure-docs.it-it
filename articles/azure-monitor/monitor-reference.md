---
title: Elementi monitorati da Monitoraggio di AzureWhat is monitored by Azure Monitor
description: Riferimento di tutti i servizi e altre risorse monitorate da Monitoraggio di Azure.Reference of all services and other resources monitored by Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 3cd330e9c4ceba2feeb7a74cafe9f094fd03d690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249165"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Che cosa viene monitorato da Monitoraggio di Azure?
Questo articolo descrive le diverse applicazioni e servizi monitorati da Monitoraggio di Azure.This article describes the different applications and services that are monitored by Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Approfondimenti e soluzioni di base
Le informazioni di base e le soluzioni sono considerate parte di Monitoraggio di Azure e seguono i contratti di supporto e livello di servizio per Azure.Core insights and solutions are considered part of Azure Monitor and follow the support and service level agreements for Azure. Sono supportati in tutte le aree di Azure in cui è disponibile Monitoraggio di Azure.They are supported in all Azure regions where Azure Monitor is available.

### <a name="insights"></a>Informazioni dettagliate

Insights offre un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici. Raccoglino e analizzano sia i log che le metriche.

| Informazioni dettagliate | Descrizione |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Il servizio Extensible Application Performance Management (APM) per monitorare l'applicazione Web live su qualsiasi piattaforma. |
| [Azure Monitor for Containers](insights/container-insights-overview.md) | Monitora le prestazioni dei carichi di lavoro del contenitore distribuiti in istanze del contenitore di Azure o nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS). |
| [Monitoraggio di Azure per cosmo DB (anteprima)Azure Monitor for Cosmos DB (preview)](insights/cosmosdb-insights-overview.md) | Fornisce una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse di Azure Cosmos DB in un'esperienza interattiva unificata. |
| [Monitoraggio di Azure per reti (anteprima)Azure Monitor for Networks (preview)](insights/network-insights-overview.md) | Fornisce una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, consentendo scenari come l'identificazione delle risorse che ospitano il sito Web, semplicemente cercando il nome del sito Web. |
[Monitoraggio di Azure per i gruppi di risorse (anteprima)Azure Monitor for Resource Groups (preview)](insights/resource-group-insights.md) |  Valutare e diagnosticare eventuali problemi riscontrati dalle singole risorse, offrendo al conto dell'integrità e delle prestazioni dell'intero gruppo di risorse. |
| [Monitoraggio di Azure per archiviazione (anteprima)Azure Monitor for Storage (preview)](insights/storage-insights-overview.md) | Fornisce un monitoraggio completo degli account di archiviazione di Azure offrendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di archiviazione di Azure.Provides comprehensive monitoring of your Azure Storage accounts by provides a unified view of your Azure Storage services performance, capacity, and availability. |
| [Monitoraggio di Azure per macchine virtuali (anteprima)Azure Monitor for VMs (preview)](insights/container-insights-overview.md) | Monitora le macchine virtuali di Azure (VM) e i set di scalabilità delle macchine virtuali su larga scala su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. |

### <a name="core-solutions"></a>Soluzioni di base

Le soluzioni si basano su query di log e visualizzazioni personalizzate per una determinata applicazione o servizio. Raccolgono e analizzano solo i log e sono deprecati nel tempo a favore delle informazioni dettagliate.

| Soluzione | Descrizione |
|:---|:---|
| [Integrità dell'agente](insights/solution-agenthealth.md) | Analizzare l'integrità e la configurazione degli agenti di Log Analytics. |
| [Gestione degli avvisi](platform/alert-management-solution.md) | Analizzare gli avvisi raccolti da System Center Operations Manager, Nagios o . |
| [Mappa del servizio](insights/service-map.md) | Individua automaticamente i componenti dell'applicazione nei sistemi Windows e Linux ed esegue automaticamente il mapping della comunicazione tra i servizi. |



## <a name="azure-services"></a>Servizi di Azure
The following table lists Azure services and the data they collect into Azure Monitor. 

- Metrics - The service automatically collects metrics into Azure Monitor Metrics. 
- Logs - The service supports diagnostic settings which can collect platform logs and metrics to Azure Monitor Logs.
- Insight: è disponibile un'analisi disponibile per il servizio che offre un'esperienza di monitoraggio personalizzata per il servizio.

| Service | Metriche | Log | Informazioni dettagliate | Note |
|:---|:---|:---|:---|:---|
|Active Directory | No | Sì | [Sì](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | No | No | No |  |
|Active Directory Domain Services | No | Sì | No |  |
|Log attività | No | Sì | No | |
|Advanced Threat Protection | No | No | No |  |
|Advisor | No | No | No |  |
|Generatore di intelligenza artificiale | No | No | No |  |
|Analysis Services | Sì | Sì | No |  |
|API per FHIR | No | No | No |  |
|Gestione API | Sì | Sì | No |  |
|Servizio app | Sì | Sì | No |  |
|AppConfig | No | No | No |  |
|Gateway applicazione | Sì | Sì | No |  |
|Servizio di attestazione | No | No | No |  |
|Automazione | Sì | Sì | No |  |
|Azure Service Manager (RDFE) | No | No | No |  |
|Backup | No | Sì | No |  |
|Bastion | No | No | No |  |
|Batch | Sì | Sì | No |  |
|Intelligenza artificiale per Batch | No | No | No |  |
|Servizio Blockchain | No | Sì | No |  |
|Progetti | No | No | No |  |
|Servizio bot | No | No | No |  |
|Servizi cloud | Sì | Sì | No | Agente necessario per monitorare il sistema operativo guest e i flussi di lavoro.  |
|Cloud Shell | No | No | No |  |
|Servizi cognitivi | Sì | Sì | No |  |
|Istanze di Container | Sì | No | No |  |
|Registro Container | Sì | Sì | No |  |
|Rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) | No | Sì | No |  |
|Cosmos DB | Sì | Sì | [Sì](insights/cosmosdb-insights-overview.md) |  |
|Gestione costi | No | No | No |  |
|Data Box | No | No | No |  |
|Catalogo dati Gen2 | No | No | No |  |
|Esplora dati | Sì | Sì | No |  |
|Data Factory | Sì | Sì | No |  |
|Data Factory v2 | No | Sì | No |  |
|Condivisione dati | No | No | No |  |
|Database per MariaDB | Sì | Sì | No |  |
|Database per MySQL | Sì | Sì | No |  |
|Database per PostgreSQL | Sì | Sì | No |  |
|Servizio Migrazione del database | No | No | No |  |
|Databricks | No | Sì | No |  |
|Protezione DDoS | Sì | Sì | No |  |
|DevOps | No | No | No |  |
|DNS | Sì | No | No |  |
|Nomi di dominio | No | No | No |  |
|DPS | No | No | No |  |
|Coinvolgimento dei clienti di Dynamics 365 | No | No | No |  |
|Dynamics 365 Finanza e operazioni | No | No | No |  |
|Griglia di eventi | Sì | No | No |  |
|Hub eventi | Sì | Sì | No |  |
|ExpressRoute | Sì | Sì | No |  |
|Firewall | Sì | Sì | No |  |
|Frontdoor | Sì | Sì | No |  |
|Funzioni | Sì | Sì | No |  |
|HDInsight | No | Sì | No |  |
|Cache HPC | No | No | No |  |
|Protezione delle informazioni | No | Sì | No |  |
|Intune | No | Sì | No |  |
|IoT Central | No | No | No |  |
|Hub IoT | Sì | Sì | No |  |
|Key Vault | Sì | Sì | No |  |
|Servizio Kubernetes | No | No | [Sì](insights/container-insights-overview.md)  |  |
|Load Balancer | Sì | Sì | No |  |
|App per la logica | Sì | Sì | No |  |
|Servizio Machine Learning | No | No | No |  |
|Applicazioni gestite  | No | No | No |  |
|Mappe  | No | No | No |  |
|Servizi multimediali | Sì | Sì | No |  |
|Microsoft Flow | No | No | No |  |
|Microsoft Managed Desktop | No | No | No |  |
|Microsoft PowerApps | No | No | No |  |
|Microsoft Social Engagement | No | No | No |  |
|Microsoft Stream | Sì | Sì | No |  |
|Migrazione | No | No | No |  |
|Multi-Factor Authentication | No | Sì | No |  |
|Network Watcher | Sì | Sì | No |  |
|Hub di notifica | Sì | No | No |  |
|Set di dati Open | No | No | No |  |
|Policy | No | No | No |  |
|Power BI | Sì | Sì | No |  |
|Power BI Embedded | No | No | No |  |
|Collegamento privato | No | No | No |  |
|Piattaforma di comunicazione dello spooler del progettoProject Spool Communication Platform | No | No | No |  |
|Red Hat OpenShift | No | No | No |  |
|Cache Redis | Sì | Sì | No |  |
|Resource Graph | No | No | No |  |
|Gestione risorse | No | No | No |  |
|Ricerca al dettaglio – di Bing | No | No | No |  |
|Ricerca | Sì | Sì | No |  |
|Bus di servizio | Sì | Sì | No |  |
|Service Fabric | No | Sì | No | Agente necessario per monitorare il sistema operativo guest e i flussi di lavoro.  |
|Portale di iscrizione | No | No | No |  |
|Site Recovery | No | Sì | No |  |
|Servizio Cloud Di Primavera | No | No | No |  |
|SQL Data Warehouse | Sì | Sì | No |  |
|Database SQL | Sì | Sì | No |  |
|SQL Server Stretch Database | Sì | Sì | No |  |
|Stack | No | No | No |  |
|Archiviazione | Sì | No | [Sì](insights/storage-insights-overview.md) |  |
|Cache di archiviazione | No | No | No |  |
|Servizi di sincronizzazione archiviazione | No | No | No |  |
|Analisi di flusso | Sì | Sì | No |  |
|Time Series Insights | Sì | Sì | No |  |
|TINA | No | No | No |  |
|Gestione traffico | Sì | Sì | No |  |
|Stampa universale | No | No | No |  |
|Set di scalabilità di macchine virtuali | No | Sì | [Sì](insights/vminsights-overview.md) | Agente necessario per monitorare il sistema operativo guest e i flussi di lavoro. |
|Macchine virtuali | Sì | Sì | [Sì](insights/vminsights-overview.md) | Agente necessario per monitorare il sistema operativo guest e i flussi di lavoro. |
|Rete virtuale | Sì | Sì | [Sì](insights/network-insights-overview.md) |  |
|Rete virtuale - Registri di flusso del gruppo di sicurezza di reteVirtual Network - NSG Flow Logs | No | Sì | No |  |
|Gateway VPN | Sì | Sì | No |  |
|Desktop virtuale Windows | No | No | No |  |


## <a name="product-integrations"></a>Integrazioni dei prodotti
The services and solutions in the following table store their data in a Log Analytics workspace so that it can be analyzed with other log data collected by Azure Monitor.

| Prodotto o servizio | Descrizione |
|:---|:---|
| [Automazione di AzureAzure Automation](/azure/automation/) | Gestire gli aggiornamenti del sistema operativo e tenere traccia delle modifiche nei computer Windows e Linux. Vedere [Rilevamento modifiche](../automation/change-tracking.md) e [gestione degli aggiornamenti](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | Classificare e facoltativamente proteggere documenti e messaggi di posta elettronica. Vedere [Creazione di report centralizzati per Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro sicurezza di AzureAzure Security Center](/azure/security-center/) | Raccogliere e analizzare gli eventi di sicurezza ed eseguire l'analisi delle minacce. Vedere Raccolta dei dati nel Centro sicurezza di [AzureSee Data collection in Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Sentinella di Azure](/azure/sentinel/) | Si connette a diverse fonti, tra cui Office 365 e Amazon Web Services Cloud Trail. Consultate [Connettere origini dati.](/azure/sentinel/connect-data-sources) |
| [Analisi dell'insieme di credenziali delle chiavi](insights/azure-key-vault.md) | Analizzare i log AuditEvent dell'insieme di credenziali delle chiavi di Azure.Analyze Azure Key Vault AuditEvent logs. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Creare un'impostazione di diagnostica per inviare log a Monitoraggio di Azure.Create a diagnostic setting to send logs to Azure Monitor. Vedere [Inviare dati di log a archivi, hub eventi o analisi dei log in Intune (anteprima).](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)  |
| Rete  | [Monitoraggio prestazioni rete:](insights/network-performance-monitor.md) consente di monitorare la connettività di rete e le prestazioni per gli endpoint di servizio e applicazione.Network Performance Monitor - Monitor network connectivity and performance to service and application endpoints.<br>[Gateway applicazione di Azure:](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) analizza i log e le metriche dal gateway applicazione di Azure.Azure Application Gateway - Analyze logs and metrics from Azure Application Gateway.<br>[Analisi del traffico:](/azure/network-watcher/traffic-analytics) analizza i log del gruppo di sicurezza di rete (NSG) di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure.Traffic Analytics - Analyzes Network Watcher network security group (NSG) flow logs to provide insights into traffic flow in your Azure cloud. |
| [Office 365](insights/solution-office-365.md) | Monitorare l'ambiente Office 365. Versione aggiornata con onboarding migliorato disponibile tramite Azure Sentinel.Updated version with improved onboarding available through Azure Sentinel. |
| [Analisi SQL](insights/azure-sql.md) | Monitorare le prestazioni dei database SQL di Azure, dei pool elastici e delle istanze gestite su larga scala e tra più sottoscrizioni. |
| [Surface Hub](insights/surface-hubs.md) | Tieni traccia dell'integrità e dell'utilizzo dei dispositivi Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Raccogliere dati dagli agenti di Operations Manager connettendo il gruppo di gestione ad Monitoraggio di Azure.Collect data from Operations Manager agents by connecting their management group to Azure Monitor. Vedere [Connettere Operations Manager ad Monitoraggio di AzureSee Connect Operations Manager to Azure Monitor](platform/om-agents.md)<br> Valutare il rischio e l'integrità del gruppo di gestione di System Center Operations Manager con la soluzione [di valutazione di Operations Manager.](insights/scom-assessment.md) |
| [Sale Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gestione integrata end-to-end dei dispositivi Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Compilare, testare e distribuire le applicazioni e quindi monitorarne lo stato e l'utilizzo. Vedere [Iniziare ad analizzare l'app per dispositivi mobili con App Center e Application Insights.](learn/mobile-center-quickstart.md) |
| WINDOWS | [Conformità di Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Valutare gli aggiornamenti desktop di Windows.<br>[Analisi desktop:](https://docs.microsoft.com/configmgr/desktop-analytics/overview) si integra con Configuration Manager per fornire informazioni dettagliate e informazioni per prendere decisioni più informate sulla preparazione degli aggiornamenti dei client Windows. |



## <a name="other-solutions"></a>Altre soluzioni
Sono disponibili altre soluzioni per il monitoraggio di applicazioni e servizi diversi, ma lo sviluppo attivo è stato interrotto e potrebbero non essere disponibili in tutte le aree. Sono coperti dal contratto di servizio per l'inserimento dei dati di Azure Log Analytics.They are covered by the Azure Log Analytics data ingestion service level agreement.

| Soluzione | Descrizione |
|:---|:---|
| [Controllo di integrità di Active Directory](insights/ad-assessment.md) | Valutare il rischio e l'integrità degli ambienti Active Directory. |
| [Stato della replica di Active Directory](insights/ad-replication-status.md) | Monitora regolarmente l'ambiente Active Directory per eventuali errori di replica. |
| [Analisi del log attività](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Analizzare le voci del log attività utilizzando query e viste di log predefinite. |
| [Analisi DNS (anteprima)D ANALYTICS (preview)](insights/dns-analytics.md) | Raccoglie, analizza e correla i log analitici e di controllo di Windows DNS e altri dati correlati dai server DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Raccogliere, visualizzare e analizzare le metriche di integrità e prestazioni del sistema Cloud Foundry in più distribuzioni. |
| [Contenitori](insights/containers.md) | Visualizzare e gestire gli host contenitore Docker e Windows. |
| [Valutazioni su richiesta](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Valuta e ottimizza la disponibilità, la sicurezza e le prestazioni degli ambienti tecnologici Microsoft locali, ibridi e cloud. |
| [Controllo di integrità SQL](insights/sql-assessment.md) | Valutare il rischio e l'integrità degli ambienti SQL Server.  |
| [Trasferimento dati](insights/wire-data.md) | Dati di rete e prestazioni consolidati raccolti da computer connessi a Windows e connessi a Linux con l'agente Log Analytics. |


## <a name="third-party-integration"></a>Integrazione con terze parti

| Soluzione | Descrizione |
|:---|:---|
| [Gestione dei servizi IT](platform/itsmc-overview.md) | Connettore di Gestione dei servizi IT consente di connettere Azure e un prodotto o servizio di Gestione dei servizi IT supportato.  |


## <a name="resources-outside-of-azure"></a>Risorse esterne ad AzureResources outside of Azure
Monitoraggio di Azure può raccogliere dati da risorse esterne ad Azure usando i metodi elencati nella tabella seguente.

| Risorsa | Metodo |
|:---|:---|
| APPLICAZIONI | Monitorare le applicazioni Web all'esterno di Azure usando Application Insights.Monitor web applications outside of Azure using Application Insights. Vedere [Che cos'è Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Macchine virtuali | Usare l'agente di Log Analytics per raccogliere dati dal sistema operativo guest delle macchine virtuali in altri ambienti cloud o in locale. Consultate [Raccogliere i dati del log con l'agente di Log Analytics.](platform/log-analytics-agent.md) |
| REST API Client | Sono disponibili API separate per scrivere dati in Log e metriche di Monitoraggio di Azure da qualsiasi client DELL'API REST. Vedere [Inviare dati di log ad Azure Monitor con l'API agente](platform/data-collector-api.md) di raccolta dati HTTP per Logs e Inviare metriche personalizzate per una risorsa di [Azure all'archivio delle metriche](platform/metrics-store-custom-rest-api.md) di Monitoraggio di Azure usando un'API REST per le metriche. |



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla piattaforma dati di [Monitoraggio di Azure in cui sono archiviati i log e le metriche raccolti da informazioni dettagliate e soluzioni](platform/data-platform.md).
- Completare [un'esercitazione sul monitoraggio](learn/tutorial-resource-logs.md)di una risorsa di Azure.Complete a tutorial on monitoring an Azure resource.
- Completare un'esercitazione sulla scrittura di [una query di log per analizzare i dati nei](learn/tutorial-resource-logs.md)log di Monitoraggio di Azure.Complete a tutorial on writing a log query to analyze data in Azure Monitor Logs .
- Completare [un'esercitazione sulla creazione di un grafico delle metriche per analizzare i dati in Metriche](learn/tutorial-metrics-explorer.md)di Monitoraggio di Azure.Complete a tutorial on creating a metrics chart to analyze data in Azure Monitor Metrics .

 
