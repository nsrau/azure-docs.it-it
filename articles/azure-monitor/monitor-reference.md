---
title: Cosa viene monitorato da monitoraggio di Azure
description: Informazioni di riferimento su tutti i servizi e altre risorse monitorate da monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 055ba8b5050aef639bbe6527292a18c8b405065a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620265"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Che cosa viene monitorato da monitoraggio di Azure?
Questo articolo descrive i diversi servizi e applicazioni monitorati da monitoraggio di Azure. 

## <a name="insights-and-core-solutions"></a>Informazioni dettagliate e soluzioni di base
Le soluzioni e le informazioni di base sono considerate parte di monitoraggio di Azure e seguono i contratti di servizio e di supporto per Azure. Sono supportati in tutte le aree di Azure in cui è disponibile monitoraggio di Azure.

### <a name="insights"></a>Informazioni dettagliate

Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici. Raccolgono e analizzano i log e le metriche.

| Informazioni dettagliate | Descrizione |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Servizio estendibile di gestione delle prestazioni delle applicazioni (APM) per monitorare l'applicazione Web Live su qualsiasi piattaforma. |
| [Monitoraggio di Azure per i contenitori](insights/container-insights-overview.md) | Consente di monitorare le prestazioni dei carichi di lavoro del contenitore distribuiti in istanze di contenitore di Azure o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS). |
| [Monitoraggio di Azure per Cosmos DB (anteprima)](insights/cosmosdb-insights-overview.md) | Fornisce una visualizzazione delle prestazioni, degli errori, della capacità e dello stato operativo complessivi di tutte le risorse di Azure Cosmos DB in un'esperienza interattiva unificata. |
| [Monitoraggio di Azure per le reti (anteprima)](insights/network-insights-overview.md) | Fornisce una panoramica completa dell'integrità e delle metriche per tutte le risorse di rete. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, abilitando scenari come l'identificazione di risorse che ospitano il sito Web, semplicemente cercando il nome del sito Web. |
[Monitoraggio di Azure per i gruppi di risorse (anteprima)](insights/resource-group-insights.md) |  Valutazione e diagnosi di eventuali problemi riscontrati dalle singole risorse, offrendo al contempo un contesto per l'integrità e le prestazioni dell'intero gruppo di risorse. |
| [Monitoraggio di Azure per l'archiviazione (anteprima)](insights/storage-insights-overview.md) | Fornisce un monitoraggio completo degli account di archiviazione di Azure, fornendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di archiviazione di Azure. |
| [Monitoraggio di Azure per le macchine virtuali (anteprima)](insights/container-insights-overview.md) | Monitora le macchine virtuali (VM) e i set di scalabilità di macchine virtuali di Azure su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. |

### <a name="core-solutions"></a>Soluzioni principali

Le soluzioni sono basate su query di log e viste personalizzate per un'applicazione o un servizio specifico. Raccolgono e analizzano solo i log e sono deprecati nel tempo a favore delle informazioni dettagliate.

| Soluzione | Descrizione |
|:---|:---|
| [Integrità agente](insights/solution-agenthealth.md) | Analizzare lo stato e la configurazione degli agenti Log Analytics. |
| [Gestione degli avvisi](platform/alert-management-solution.md) | Analizza gli avvisi raccolti da System Center Operations Manager, Nagios o Zabbix. |
| [Elenco dei servizi](insights/service-map.md) | Individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue il mapping delle comunicazioni tra i servizi. |



## <a name="azure-services"></a>Servizi di Azure
La tabella seguente elenca i servizi di Azure e i dati raccolti in monitoraggio di Azure. 

- Metriche: il servizio raccoglie automaticamente le metriche nelle metriche di monitoraggio di Azure. 
- Log: il servizio supporta le impostazioni di diagnostica che consentono di raccogliere i log e le metriche della piattaforma nei log di monitoraggio di Azure.
- Informazioni dettagliate: sono disponibili informazioni dettagliate per il servizio, che offre un'esperienza di monitoraggio personalizzata per il servizio.

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
|Servizi cloud | Sì | Sì | No | Agent necessario per monitorare i flussi di lavoro e il sistema operativo guest.  |
|Cloud Shell | No | No | No |  |
|Servizi cognitivi | Sì | Sì | No |  |
|Istanze di Container | Sì | No | No |  |
|Registro Container | Sì | Sì | No |  |
|Rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) | No | Sì | No |  |
|Cosmos DB | Sì | Sì | [Sì](insights/cosmosdb-insights-overview.md) |  |
|Gestione costi | No | No | No |  |
|Data Box | No | No | No |  |
|Data Catalog Gen2 | No | No | No |  |
|Esplora dati | Sì | Sì | No |  |
|Data Factory | Sì | Sì | No |  |
|Data Factory V2 | No | Sì | No |  |
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
|Coinvolgimento dei clienti in Dynamics 365 | No | No | No |  |
|Finanza e operazioni di Dynamics 365 | No | No | No |  |
|Griglia di eventi | Sì | No | No |  |
|Hub eventi | Sì | Sì | No |  |
|ExpressRoute | Sì | Sì | No |  |
|Firewall | Sì | Sì | No |  |
|Frontdoor | Sì | Sì | No |  |
|Funzioni | Sì | Sì | No |  |
|HDInsight | No | Sì | No |  |
|Cache HPC | No | No | No |  |
|Information Protection | No | Sì | No |  |
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
|Desktop gestito Microsoft | No | No | No |  |
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
|Piattaforma di comunicazione spooling del progetto | No | No | No |  |
|Red Hat OpenShift | No | No | No |  |
|Cache Redis | Sì | Sì | No |  |
|Resource Graph | No | No | No |  |
|Gestione risorse | No | No | No |  |
|Ricerca al dettaglio: Bing | No | No | No |  |
|Ricerca | Sì | Sì | No |  |
|Bus di servizio | Sì | Sì | No |  |
|Service Fabric | No | Sì | No | Agent necessario per monitorare i flussi di lavoro e il sistema operativo guest.  |
|Portale di iscrizione | No | No | No |  |
|Site Recovery | No | Sì | No |  |
|Servizio cloud Spring | No | No | No |  |
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
|Set di scalabilità di macchine virtuali | No | Sì | [Sì](insights/vminsights-overview.md) | Agent necessario per monitorare i flussi di lavoro e il sistema operativo guest. |
|Macchine virtuali | Sì | Sì | [Sì](insights/vminsights-overview.md) | Agent necessario per monitorare i flussi di lavoro e il sistema operativo guest. |
|Rete virtuale | Sì | Sì | [Sì](insights/network-insights-overview.md) |  |
|Rete virtuale-log di flusso NSG | No | Sì | No |  |
|Gateway VPN | Sì | Sì | No |  |
|Desktop virtuale Windows | No | No | No |  |


## <a name="product-integrations"></a>Integrazioni del prodotto
I servizi e le soluzioni nella tabella seguente archiviano i dati in un'area di lavoro di Log Analytics in modo che possano essere analizzati con altri dati di log raccolti da monitoraggio di Azure.

| Prodotto o servizio | Descrizione |
|:---|:---|
| [Automazione di Azure](/azure/automation/) | Gestire gli aggiornamenti del sistema operativo e tenere traccia delle modifiche nei computer Windows e Linux. Vedere [rilevamento modifiche](../automation/change-tracking.md) e [Gestione aggiornamenti](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | Classificare ed eventualmente proteggere documenti e messaggi di posta elettronica. [Per Azure Information Protection, vedere Central Reporting](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro sicurezza di Azure](/azure/security-center/) | Raccogliere e analizzare gli eventi di sicurezza ed eseguire l'analisi delle minacce. Vedere [raccolta dati nel centro sicurezza di Azure](/azure/security-center/security-center-enable-data-collection) |
| [Sentinella di Azure](/azure/sentinel/) | Si connette a diverse origini, tra cui Office 365 e Amazon Web Services cloud Trail. Vedere [Connect Data Sources](/azure/sentinel/connect-data-sources). |
| [Analisi dell'insieme di credenziali delle chiavi](insights/azure-key-vault.md) | Analizzare Azure Key Vault registri AuditEvent. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Creare un'impostazione di diagnostica per inviare i log a monitoraggio di Azure. Vedere [inviare dati di log ad archiviazione, Hub eventi o log Analytics in Intune (anteprima)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rete  | [Monitoraggio prestazioni rete](insights/network-performance-monitor.md) : monitorare la connettività di rete e le prestazioni per gli endpoint di servizio e di applicazione.<br>[Applicazione Azure gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) : analizzare i log e le metriche da applicazione Azure gateway.<br>[Analisi del traffico](/azure/network-watcher/traffic-analytics) -Network Watcher analizza i log dei flussi del gruppo di sicurezza di rete (NSG) per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorare l'ambiente Office 365. Versione aggiornata con l'onboarding migliorato disponibile tramite Sentinel di Azure. |
| [Analisi SQL](insights/azure-sql.md) | Monitora le prestazioni di database SQL di Azure, pool elastici e istanze gestite su larga scala e in più sottoscrizioni. |
| [Surface Hub](insights/surface-hubs.md) | Tenere traccia dello stato e dell'utilizzo dei dispositivi Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Raccogliere i dati dagli agenti Operations Manager connettendo il gruppo di gestione a monitoraggio di Azure. Vedere [connettere Operations Manager a monitoraggio di Azure](platform/om-agents.md)<br> Valutare il rischio e l'integrità del gruppo di gestione di System Center Operations Manager con la soluzione [Operations Manager Assessment](insights/scom-assessment.md) . |
| [Chat di Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gestione end-to-end integrata dei dispositivi Microsoft teams room. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Creare, testare e distribuire applicazioni e monitorarne lo stato e l'utilizzo. Vedere [avviare l'analisi dell'app per dispositivi mobili con App Center e Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformità Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) : valutare gli aggiornamenti del desktop di Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) -si integra con Configuration Manager per fornire informazioni e informazioni dettagliate per prendere decisioni più informate sulla preparazione degli aggiornamenti dei client Windows. |



## <a name="other-solutions"></a>Altre soluzioni
Sono disponibili altre soluzioni per il monitoraggio di applicazioni e servizi diversi, ma lo sviluppo attivo è stato interrotto e potrebbero non essere disponibili in tutte le aree. Sono coperte dal contratto di servizio di inserimento dati di Azure Log Analytics.

| Soluzione | Descrizione |
|:---|:---|
| [Controllo integrità Active Directory](insights/ad-assessment.md) | Valutare il rischio e l'integrità degli ambienti di Active Directory. |
| [Stato della replica Active Directory](insights/ad-replication-status.md) | Monitora regolarmente l'ambiente di Active Directory per eventuali errori di replica. |
| [Analisi log attività](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Analizzare le voci del log attività usando le query e le visualizzazioni di log predefinite. |
| [Analisi DNS (anteprima)](insights/dns-analytics.md) | Raccoglie, analizza e mette in correlazione i registri di analisi e di controllo DNS di Windows e altri dati correlati dai server DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Raccogli, Visualizza e analizza le metriche delle prestazioni e dell'integrità del sistema Cloud Foundry in più distribuzioni. |
| [Contenitori](insights/containers.md) | Visualizzare e gestire gli host contenitore Docker e Windows. |
| [Valutazioni su richiesta](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Valuta e ottimizza la disponibilità, la sicurezza e le prestazioni degli ambienti di tecnologia Microsoft locali, ibridi e cloud. |
| [Controllo integrità SQL](insights/sql-assessment.md) | Valutare il rischio e l'integrità degli ambienti di SQL Server.  |
| [Wire Data](insights/wire-data.md) | Dati consolidati di rete e sulle prestazioni raccolti da computer connessi a Windows e Linux con l'agente di Log Analytics. |


## <a name="third-party-integration"></a>Integrazione di terze parti

| Soluzione | Descrizione |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Connettore di Gestione dei servizi IT consente di connettere Azure e un prodotto o servizio di Gestione dei servizi IT supportato.  |


## <a name="resources-outside-of-azure"></a>Risorse esterne ad Azure
Monitoraggio di Azure può raccogliere dati da risorse esterne ad Azure usando i metodi elencati nella tabella seguente.

| Risorsa | Metodo |
|:---|:---|
| APPLICAZIONI | Monitorare le applicazioni Web all'esterno di Azure usando Application Insights. Vedere informazioni su [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Macchine virtuali | Usare l'agente di Log Analytics per raccogliere dati dal sistema operativo guest di macchine virtuali in altri ambienti cloud o in locale. Vedere [raccogliere dati di log con l'agente di log Analytics](platform/log-analytics-agent.md). |
| Client API REST | Sono disponibili API separate per la scrittura di dati in log e metriche di monitoraggio di Azure da qualsiasi client API REST. Vedere [inviare i dati di log a monitoraggio di Azure con l'API dell'agente di raccolta dati http](platform/data-collector-api.md) per i log e [inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di monitoraggio di Azure usando un'API REST per le](platform/metrics-store-custom-rest-api.md) metriche. |



## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [piattaforma dati di monitoraggio di Azure che archivia i log e le metriche raccolti da informazioni dettagliate e soluzioni](platform/data-platform.md).
- Completare un' [esercitazione sul monitoraggio di una risorsa di Azure](learn/tutorial-resource-logs.md).
- Completare un' [esercitazione sulla scrittura di una query di log per analizzare i dati nei log di monitoraggio di Azure](learn/tutorial-resource-logs.md).
- Completare un' [esercitazione sulla creazione di un grafico delle metriche per analizzare i dati nelle metriche di monitoraggio di Azure](learn/tutorial-metrics-explorer.md).

 
