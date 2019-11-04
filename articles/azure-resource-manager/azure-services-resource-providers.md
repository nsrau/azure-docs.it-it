---
title: Azure Resource Manager i provider di risorse in base ai servizi di Azure
description: Elenca tutti gli spazi dei nomi del provider di risorse per Azure Resource Manager e Mostra il servizio di Azure per lo spazio dei nomi.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: tomfitz
ms.openlocfilehash: 3bbd52615962c2cd187fb16282a373688175cf59
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476684"
---
# <a name="resource-providers-for-azure-services"></a>Provider di risorse per i servizi di Azure

Questo articolo illustra come eseguire il mapping degli spazi dei nomi del provider di risorse ai servizi di Azure.

## <a name="match-resource-provider-to-service"></a>Associare il provider di risorse al servizio

| Spazio dei nomi del provider di risorse | Servizio di Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml) |
| microsoft.aadiam | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../advisor/index.yml) |
| Microsoft.AlertsManagement | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gestione API](../api-management/index.yml) |
| Microsoft. AppConfiguration | core |
| Microsoft.Attestation | Servizio di attestazione di Azure |
| Microsoft.Authorization | [Azure Resource Manager](index.yml) |
| Microsoft.Automation | [Automazione](../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft.Billing | [Fatturazione](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mappe](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.BizTalkServices | [Servizi BizTalk](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft.Blockchain | [Servizio Azure blockchain](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Progetti di Azure](/azure/governance/blueprints/) |
| Microsoft.BotService | [Servizio Azure bot](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis di Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rete per la distribuzione di contenuti](../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificati del servizio app](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Macchina virtuale del modello di distribuzione classica |
| Microsoft.ClassicInfrastructureMigrate | Migrazione del modello di distribuzione classica |
| Microsoft.ClassicNetwork | Rete virtuale del modello di distribuzione classica |
| Microsoft.ClassicStorage | Archiviazione modello di distribuzione classica |
| Microsoft. ClassicSubscription | Modello di distribuzione classica |
| Microsoft.CognitiveServices | [Servizi cognitivi](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Macchine virtuali](/azure/virtual-machines/)<br />[Set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [Gestione dei costi](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Istanze di contenitore](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft.ContainerService | [Servizio Azure Kubernetes](/azure/aks/) |
| Microsoft.ContentModerator | [Content Moderator di Azure](../cognitive-services/content-moderator/index.yml) |
| Microsoft.CostManagement | [Gestione dei costi](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Gestione dei costi](/azure/cost-management/) |
| Microsoft.CustomerInsights | Customer Insights |
| Microsoft. CustomerLockbox | Customer Lockbox per Microsoft Azure |
| Microsoft. CustomProviders | [Provider personalizzati di Azure](../managed-applications/custom-providers-overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Servizio migrazione del database di Azure](/azure/dms/) |
| Microsoft. DataShare | [Condivisione dati di Azure](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Database di Azure per MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Database di Azure per MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Database di Azure per PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Desktop virtuale Windows](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Deployment Manager di Azure](deployment-manager-overview.md) |
| Microsoft.Devices | [Hub Internet delle cose](/azure/iot-hub/)<br />[Servizio Device Provisioning in hub IoT](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Servizio app](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Grafico Knowledge base Enterprise |
| Microsoft.EventGrid | [Griglia di eventi](/azure/event-grid/) |
| Microsoft.EventHub | [Hub eventi](../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](index.yml) |
| Microsoft.Genomics | [Genomica di Microsoft](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Criteri di Azure](../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA in Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Modulo di protezione hardware dedicato di Azure](../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [API di Azure per FHIR](../healthcare-apis/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Importazione/esportazione di Azure](../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.Intune | [Intune](/intune/) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Dispositivi gemelli digitali di Azure](../digital-twins/index.yml) |
| Microsoft.KeyVault | [Insieme di credenziali delle chiavi](../key-vault/index.yml) |
| Microsoft.Kusto | [Esplora dati di Azure](../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.LocationBasedServices | [Mappe di Azure](../azure-maps/index.yml) |
| Microsoft.LocationServices | core |
| Microsoft.LogAnalytics | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.Logic | [App per la logica](../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningCompute | [Servizio Machine Learning](../machine-learning/index.yml) |
| Microsoft.MachineLearningModelManagement | [Servizio Machine Learning](../machine-learning/index.yml) |
| Microsoft.MachineLearningServices | [Servizio Machine Learning](../machine-learning/index.yml) |
| Microsoft.ManagedIdentity | [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Gruppi di gestione](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mappe di Azure](../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Servizi multimediali](../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Spring in Azure](/azure/java/spring-framework/) |
| Microsoft.Migrate | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Ancoraggi nello spazio di Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Rete virtuale](../virtual-network/index.yml)<br />[Bilanciamento del carico](../load-balancer/index.yml)<br />[Gateway applicazione](../application-gateway/index.yml)<br />[DNS di Azure](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[Gateway VPN](../vpn-gateway/index.yml)<br />[Gestione traffico](../traffic-manager/index.yml)<br />[Network Watcher](../network-watcher/index.yml)<br />[Firewall di Azure](../firewall/index.yml)<br />[Servizio Frontdoor di Azure](../frontdoor/index.yml)<br />[Bastion di Azure](/azure/bastion/) |
| Microsoft.NotificationHubs | [Hub di notifica](../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Monitoraggio di Azure](../azure-monitor/index.yml) |
| Microsoft. peering | Servizio di peering Microsoft Azure |
| Microsoft.PolicyInsights | [Criteri di Azure](../governance/policy/index.yml) |
| Microsoft.Portal | [Azure portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Relay | [Inoltro di Azure](../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Grafico delle risorse di Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | core |
| Microsoft.Resources | [Azure Resource Manager](index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Utilità di pianificazione](/azure/scheduler/) |
| Microsoft.Search | [Ricerca di Azure](../search/index.yml) |
| Microsoft.Security | [Centro sicurezza](../security-center/index.yml) |
| Microsoft. SecurityInsights | [Sentinella di Azure](/azure/sentinel/) |
| Microsoft.ServiceBus | [Bus di servizio](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Servizio Azure SignalR](../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Solutions | [Applicazioni gestite di Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Database SQL di Azure](../sql-database/index.yml)<br />[SQL Data Warehouse](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server in Macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Archiviazione](../storage/index.yml) |
| Microsoft. StorageCache | [Cache HPC di Azure](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Archiviazione](../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Analisi dei flussi](../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMwareCloudSimple | [Soluzione VMware di Azure di CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [Servizio app](../app-service/index.yml)<br />[Funzioni](../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft.WindowsIoT | [Servizi principali di Windows 10](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Monitoraggio di Azure](../azure-monitor/index.yml) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](resource-manager-supported-services.md)
