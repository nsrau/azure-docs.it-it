---
title: Che cosa viene monitorato da Monitoraggio di Azure
description: Informazioni di riferimento su tutti i servizi e le altre risorse monitorati da Monitoraggio di Azure.
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: d8c4eea10b0c2230e50b5ded710b3455539f6493
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206034"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Che cosa viene monitorato da Monitoraggio di Azure?
Questo articolo descrive le applicazioni e i servizi monitorati da Monitoraggio di Azure. 

## <a name="insights-and-core-solutions"></a>Informazioni dettagliate e soluzioni di base
Le informazioni dettagliate e le soluzioni di base sono considerate parte integrante di Monitoraggio di Azure e seguono i contratti di servizio e di supporto relativi ad Azure. Sono supportati in tutte le aree di Azure in cui è disponibile Monitoraggio di Azure.

### <a name="insights"></a>Informazioni dettagliate

Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata per determinati servizi e applicazioni. Raccolgono e analizzano sia i log che le metriche.

| Informazioni dettagliate | Descrizione |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Servizio estendibile di gestione delle prestazioni delle applicazioni per monitorare l'applicazione Web live su qualsiasi piattaforma. |
| [Monitoraggio di Azure per contenitori](insights/container-insights-overview.md) | Monitorizza le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. |
| [Monitoraggio di Azure per Cosmos DB](insights/cosmosdb-insights-overview.md) | Offre una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse Azure Cosmos DB in un'esperienza interattiva unificata. |
| [Monitoraggio di Azure per le reti (anteprima)](insights/network-insights-overview.md) | Offre una panoramica completa dell'integrità e delle metriche di tutte le risorse di rete. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, in modo da poter, ad esempio, identificare le risorse che ospitano il sito Web semplicemente cercando il nome del sito Web. |
[Monitoraggio di Azure per gruppi di risorse (anteprima)](insights/resource-group-insights.md) |  Consente di analizzare e diagnosticare i problemi riscontrati dalle singole risorse, offrendo al contempo un contesto dell'integrità e delle prestazioni del gruppo di risorse nel suo complesso. |
| [Monitoraggio di Azure per l'archiviazione](insights/storage-insights-overview.md) | Offre un monitoraggio completo degli account di Archiviazione di Azure offrendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di Archiviazione di Azure. |
| [Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md) | Consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni. |
| [Monitoraggio di Azure per Key Vault (anteprima)](./insights/key-vault-insights-overview.md) | Fornisce un monitoraggio completo degli insiemi di credenziali delle chiavi grazie a una visualizzazione unificata delle richieste di Key Vault, delle prestazioni, degli errori e della latenza. |
| [Monitoraggio di Azure per la cache di Azure per Redis (anteprima)](insights/redis-cache-insights-overview.md) |  Offre una vista unificata e interattiva delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa. |


### <a name="core-solutions"></a>Soluzioni di base

Le soluzioni sono basate su query di log e viste personalizzate per un'applicazione o un servizio specifico. Raccolgono e analizzano solo i log e, nel tempo, verranno deprecati a favore delle informazioni dettagliate.

| Soluzione | Descrizione |
|:---|:---|
| [Integrità agente](insights/solution-agenthealth.md) | Analizzare lo stato e la configurazione degli agenti di Log Analytics. |
| [Gestione degli avvisi](platform/alert-management-solution.md) | Analizza gli avvisi raccolti da System Center Operations Manager, Nagios o Zabbix. |
| [Elenco dei servizi](insights/service-map.md) | Individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. |



## <a name="azure-services"></a>Servizi di Azure
La tabella seguente elenca i servizi di Azure e i dati che raccolgono in Monitoraggio di Azure. 

- Metriche: questo servizio raccoglie automaticamente le metriche in Metriche di Monitoraggio di Azure. 
- Log: questo servizio supporta le impostazioni di diagnostica che consentono di raccogliere i log e le metriche della piattaforma in Log di Monitoraggio di Azure.
- Informazioni dettagliate: sono disponibili informazioni dettagliate relative al servizio, che offrono un'esperienza di monitoraggio personalizzata per il servizio.

| Service | Metriche | Log | Informazioni dettagliate | Note |
|:---|:---|:---|:---|:---|
|Active Directory | No | Sì | [Sì](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | No | No | No |  |
|Active Directory Domain Services | No | Sì | No |  |
|Log attività | No | Sì | No | |
|Advanced Threat Protection | No | No | No |  |
|Advisor | No | No | No |  |
|AI Builder | No | No | No |  |
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
|Servizi cloud | Sì | Sì | No | Agente necessario per monitorare i flussi di lavoro e il sistema operativo guest.  |
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
|Dynamics 365 for Customer Engagement | No | No | No |  |
|Dynamics 365 for Finance and Operations | No | No | No |  |
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
|Key Vault | Sì | Sì | [Sì](./insights/key-vault-insights-overview.md) |  |
|Servizio Kubernetes | No | No | [Sì](insights/container-insights-overview.md)  |  |
|Load Balancer | Sì | No | No |  |
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
|Power BI Embedded | Sì | Sì | No |  |
|Collegamento privato | No | No | No |  |
|Piattaforma di comunicazione spooling del progetto | No | No | No |  |
|Red Hat OpenShift | No | No | No |  |
|Cache Redis | Sì | Sì | [Sì](insights/redis-cache-insights-overview.md) | |
|Resource Graph | No | No | No |  |
|Gestione risorse | No | No | No |  |
|Ricerca al dettaglio: Bing | No | No | No |  |
|Ricerca | Sì | Sì | No |  |
|Bus di servizio | Sì | Sì | No |  |
|Service Fabric | No | Sì | No | Agente necessario per monitorare i flussi di lavoro e il sistema operativo guest.  |
|Portale di iscrizione | No | No | No |  |
|Site Recovery | No | Sì | No |  |
|Spring Cloud Service | No | No | No |  |
|Azure Synapse Analytics | Sì | Sì | No |  |
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
|Set di scalabilità di macchine virtuali | No | Sì | [Sì](insights/vminsights-overview.md) | Agente necessario per monitorare i flussi di lavoro e il sistema operativo guest. |
|Macchine virtuali | Sì | Sì | [Sì](insights/vminsights-overview.md) | Agente necessario per monitorare i flussi di lavoro e il sistema operativo guest. |
|Rete virtuale | Sì | Sì | [Sì](insights/network-insights-overview.md) |  |
|Rete virtuale - Log del flusso del gruppo di sicurezza di rete | No | Sì | No |  |
|Gateway VPN | Sì | Sì | No |  |
|Desktop virtuale Windows | No | No | No |  |


## <a name="product-integrations"></a>Integrazioni dei prodotti
Le soluzioni e i servizi elencati nella tabella seguente archiviano i dati in un'area di lavoro di Log Analytics in modo che possano essere analizzati con altri dati di log raccolti da Monitoraggio di Azure.

| Prodotto o servizio | Descrizione |
|:---|:---|
| [Automazione di Azure](../automation/index.yml) | Gestisce gli aggiornamenti del sistema operativo e tiene traccia delle modifiche in computer Windows e Linux. Vedere [Rilevamento modifiche](../automation/change-tracking/overview.md) e [Gestione aggiornamenti](../automation/update-management/update-mgmt-overview.md). |
| [Azure Information Protection](/azure/information-protection/) | Classifica ed eventualmente protegge documenti e messaggi di posta elettronica. Vedere [Reporting centralizzato per Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Centro sicurezza di Azure](../security-center/index.yml) | Raccoglie e analizza gli eventi di sicurezza ed esegue l'analisi delle minacce. Vedere [Raccolta dati nel Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Si connette a varie origini, tra cui Office 365 e Amazon Web Services Cloud Trail. Vedere [Origini dati Connect](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Crea un'impostazione di diagnostica per inviare i log a Monitoraggio di Azure. Vedere [Inviare i dati dei log alla risorsa di archiviazione, agli hub eventi o a Log Analytics in Intune (anteprima)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rete  | [Monitoraggio prestazioni rete](insights/network-performance-monitor.md): monitorizza le prestazioni e la connettività di rete in corrispondenza degli endpoint del servizio e dell'applicazione.<br>[Gateway applicazione di Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor): analizza i log e le metriche da Gateway applicazione di Azure.<br>[Analisi del traffico](../network-watcher/traffic-analytics.md): analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorizza l'ambiente Office 365. Versione aggiornata con onboarding migliorato disponibile tramite Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Monitora le prestazioni dei database SQL di Azure e delle istanze gestite di SQL su larga scala e su più sottoscrizioni. |
| [Surface Hub](insights/surface-hubs.md) | Tiene traccia dello stato d'integrità e dell'utilizzo dei dispositivi Surface Hub. |
| [System Center Operations Manager](/system-center/scom) | Raccoglie i dati dagli agenti di Operations Manager connettendo il gruppo di gestione a Monitoraggio di Azure. Vedere [Connettere Operations Manager a Monitoraggio di Azure](platform/om-agents.md).<br> Valuta il rischio e l'integrità del gruppo di gestione di System Center Operations Manager con la soluzione [Operations Manager Assessment](insights/scom-assessment.md). |
| [Microsoft Teams Rooms](/microsoftteams/room-systems/azure-monitor-deploy) | Consente una gestione end-to-end integrata dei dispositivi Microsoft Teams Rooms. |
| [Visual Studio App Center](/appcenter/) | Consente di creare, testare e distribuire applicazioni e di monitorarne lo stato e l'utilizzo. Vedere [Avviare l'analisi dell'app per dispositivi mobili con App Center e Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformità agli aggiornamenti per Windows](/windows/deployment/update/update-compliance-get-started): valuta gli aggiornamenti desktop per Windows.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview) - Si integra con Configuration Manager per fornire informazioni dettagliate e intelligence e consentire agli utenti di prendere decisioni più informate sull'adozione aggiornamenti di Windows. |



## <a name="other-solutions"></a>Altre soluzioni
Sono disponibili anche altre soluzioni per il monitoraggio di applicazioni e servizi, ma lo sviluppo attivo è stato interrotto e potrebbero non essere disponibili in tutte le aree geografiche. Sono coperte dal contratto di servizio per l'inserimento dati di Azure Log Analytics.

| Soluzione | Descrizione |
|:---|:---|
| [Controllo integrità Active Directory](insights/ad-assessment.md) | Consente di valutare il livello di rischio e l'integrità di ambienti Active Directory. |
| [Stato replica di Active Directory](insights/ad-replication-status.md) | Controlla periodicamente l'ambiente Active Directory per rilevare eventuali errori di replica. |
| [Analisi log attività](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Visualizzare le voci del log attività. |
| [Analisi DNS (anteprima)](insights/dns-analytics.md) | Raccoglie, analizza e mette in relazione i log di controllo e analisi DNS di Windows e altri dati correlati dei server DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Consente di raccogliere, visualizzare e analizzare le metriche relative a prestazioni e integrità del sistema Cloud Foundry tra più distribuzioni. |
| [Contenitori](insights/containers.md) | Consente di visualizzare e gestire gli host dei contenitori Docker e Windows. |
| [Valutazioni su richiesta](/services-hub/health/getting_started_with_on_demand_assessments) | Consente di valutare e ottimizzare la disponibilità, la sicurezza e le prestazioni degli ambienti IT Microsoft locali, ibridi e cloud. |
| [Controllo integrità SQL](insights/sql-assessment.md) | Consente di valutare il livello di rischio e l'integrità di ambienti SQL Server.  |
| [Wire Data](insights/wire-data.md) | Dati di prestazioni e di rete consolidati che vengono raccolti da computer connessi tramite Windows o Linux all'agente di Log Analytics. |

## <a name="third-party-integration"></a>Integrazione con soluzioni di terze parti

| Soluzione | Descrizione |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Connettore di Gestione dei servizi IT consente di connettere Azure e un prodotto o servizio di Gestione dei servizi IT supportato.  |


## <a name="resources-outside-of-azure"></a>Risorse esterne ad Azure
Monitoraggio di Azure può raccogliere dati da risorse esterne ad Azure usando i metodi elencati nella tabella seguente.

| Risorsa | Metodo |
|:---|:---|
| APPLICAZIONI | Consente di monitorare le applicazioni Web all'esterno di Azure usando Application Insights. Vedere [Informazioni su Application Insights](./app/app-insights-overview.md). |
| Macchine virtuali | Usare gli agenti per raccogliere dati dal sistema operativo guest di macchine virtuali in altri ambienti cloud o in locale. Vedere [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md). |
| Client dell'API REST | Sono disponibili API separate per la scrittura di dati nei log e nelle metriche di Monitoraggio di Azure da qualsiasi client API REST. Vedere [Inviare dati di log a Monitoraggio di Azure con l'API di raccolta dati HTTP](platform/data-collector-api.md) per i log e [Inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di Monitoraggio di Azure usando un'API REST](platform/metrics-store-custom-rest-api.md) per le metriche. |



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Piattaforma dati di monitoraggio di Azure, che archivia i log e le metriche raccolti dalle informazioni dettagliate e dalle soluzioni](platform/data-platform.md).
- Completare un'[esercitazione sul monitoraggio di una risorsa di Azure](learn/tutorial-resource-logs.md).
- Completare un'[esercitazione sulla scrittura di una query di log per analizzare i dati nei log di Monitoraggio di Azure](learn/tutorial-resource-logs.md).
- Completare un'[esercitazione sulla creazione di un grafico delle metriche per analizzare i dati nelle metriche di Monitoraggio di Azure](learn/tutorial-metrics-explorer.md).

 
