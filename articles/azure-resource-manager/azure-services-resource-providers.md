---
title: Provider di risorse di Azure Resource Manager dai servizi di Azure
description: Elenca tutti spazi dei nomi del provider di risorse di Azure Resource Manager e visualizza il servizio di Azure per tale spazio dei nomi.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/25/2019
ms.author: tomfitz
ms.openlocfilehash: 54493efdc0bffcbb4654b65676554f6707716968
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235567"
---
# <a name="resource-providers-for-azure-services"></a>Provider di risorse per i servizi di Azure

Questo articolo illustra come eseguire il mapping degli spazi dei nomi del provider di risorse ai servizi di Azure.

## <a name="match-resource-provider-to-service"></a>Provider di risorse di corrispondenza al servizio

| Spazio dei nomi del provider di risorse | Servizio di Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml) |
| microsoft.aadiam | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../advisor/index.yml) |
| Microsoft.AlertsManagement | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gestione API](../api-management/index.yml) |
| Microsoft.AppConfiguration | core |
| Microsoft.Authorization | [Azure Resource Manager](index.yml) |
| Microsoft.Automation | [Automazione](../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | [Azure Stack](/azure-stack/user/) |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft.Billing | [Fatturazione](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mappe](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.BizTalkServices | [Servizi BizTalk](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft.Blockchain | [Servizio Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Linee guida per Azure](/azure/governance/blueprints/) |
| Microsoft.BotService | [Servizio Azure Bot](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis di Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rete CDN](../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificati del servizio App](../app-service/web-sites-purchase-ssl-web-site.md) |
| Microsoft.ClassicCompute | Macchina virtuale del modello di distribuzione classica |
| Microsoft.ClassicInfrastructureMigrate | Migrazione del modello di distribuzione classica |
| Microsoft.ClassicNetwork | Rete virtuale del modello di distribuzione classica |
| Microsoft.ClassicStorage | Archiviazione del modello di distribuzione classica |
| Microsoft.ClassicSubscription | Modello di distribuzione classica |
| Microsoft.CognitiveServices | [Servizi cognitivi](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Macchine virtuali](/azure/virtual-machines/) |
| Microsoft.Consumption | [Gestione costi](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Istanze di contenitore](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registro contenitori](/azure/container-registry/) |
| Microsoft.ContainerService | [Servizio Azure Kubernetes](/azure/aks/) |
| Microsoft.ContentModerator | [Azure Content Moderator](../cognitive-services/content-moderator/index.yml) |
| Microsoft.CostManagement | [Gestione costi](/azure/cost-management/) |
| Microsoft.CustomerInsights | Customer Insights |
| Microsoft.CustomerLockbox | Customer Lockbox per Microsoft Azure |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analitica](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Servizio migrazione del Database di Azure](/azure/dms/) |
| Microsoft.DBforMariaDB | [Database di Azure per MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Database di Azure per MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Database di Azure per PostgreSQL](/azure/postgresql/) |
| Microsoft.DeploymentManager | [Azure Deployment Manager](deployment-manager-overview.md) |
| Microsoft.Devices | [Hub IoT](/azure/iot-hub/) |
| Microsoft.DevSpaces | [Spazi di sviluppo di Azure](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Servizio App](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Grafico della Knowledge base aziendale |
| Microsoft.EventGrid | [Griglia di eventi](/azure/event-grid/) |
| Microsoft.EventHub | [Hub eventi](../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](index.yml) |
| Microsoft.Genomics | [Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Criteri di Azure](../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA in Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Modulo di protezione hardware dedicato di Azure](../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API di Azure per FHIR](../healthcare-apis/index.yml) |
| Microsoft.ImportExport | [Importazione/esportazione di Azure](../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.Intune | [Intune](/intune/) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure gemelli digitali](../digital-twins/index.yml) |
| Microsoft.KeyVault | [Insieme di credenziali di chiave](../key-vault/index.yml) |
| Microsoft.Kusto | [Esplora dati di Azure](../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.LocationBasedServices | [Mappe di Azure](../azure-maps/index.yml) |
| Microsoft.LocationServices | core |
| Microsoft.LogAnalytics | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.Logic | [App per la logica](../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningCompute | [Servizio di Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningModelManagement | [Servizio di Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningServices | [Servizio di Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.ManagedIdentity | [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.Management | [Gruppi di gestione](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mappe di Azure](../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Servizi multimediali](../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Azure Anchor spaziali](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Rete virtuale](../virtual-network/index.yml)<br />[Bilanciamento del carico](../load-balancer/index.yml)<br />[Gateway applicazione](../application-gateway/index.yml)<br />[DNS di Azure](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[Gateway VPN](../vpn-gateway/index.yml)<br />[Gestione traffico](../traffic-manager/index.yml)<br />[Network Watcher](../network-watcher/index.yml)<br />[Firewall di Azure](../firewall/index.yml)<br />[Servizio di Azure di ingresso principale](../frontdoor/index.yml) |
| Microsoft.NotificationHubs | [Hub di notifica di Azure](../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.PolicyInsights | [Criteri di Azure](../governance/policy/index.yml) |
| Microsoft.Portal | [Portale di Azure](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Relay | [Inoltro di Azure](../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceHealth | core |
| Microsoft.Resources | [Azure Resource Manager](index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Utilità di pianificazione](/azure/scheduler/) |
| Microsoft.Search | [Ricerca di Azure](../search/index.yml) |
| Microsoft.Security | [Il Centro sicurezza](../security-center/index.yml) |
| Microsoft.ServiceBus | [Bus di servizio](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Servizio Azure SignalR](../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Solutions | [Applicazioni gestite di Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Database SQL di Azure](../sql-database/index.yml) |
| Microsoft.SqlVirtualMachine | [SQL Server in Macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Archiviazione](../storage/index.yml) |
| Microsoft.StorageSync | [Archiviazione](../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Analisi dei flussi](../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| microsoft.visualstudio | [DevOps di Azure](/azure/devops/?view=azure-devops) |
| Microsoft.Web | [Servizio app](../app-service/index.yml)<br />[Funzioni](../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft.WindowsIoT | [Servizi di Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Monitoraggio di Azure](../azure-monitor/index.yml) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui provider di risorse, vedere [provider di risorse di Azure e tipi](resource-manager-supported-services.md)
