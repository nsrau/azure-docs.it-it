---
title: Provider di risorse per servizi di Azure
description: Elenca tutti gli spazi dei nomi del provider di risorse per Azure Resource Manager e Mostra il servizio di Azure per lo spazio dei nomi.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 55fbe4ae383e5275d185e2a03224e77660a01ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382503"
---
# <a name="resource-providers-for-azure-services"></a>Provider di risorse per i servizi di Azure

Questo articolo illustra come eseguire il mapping degli spazi dei nomi del provider di risorse ai servizi di Azure.

## <a name="match-resource-provider-to-service"></a>Associare il provider di risorse al servizio

| Spazio dei nomi del provider di risorse | Servizio di Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gestione API](../../api-management/index.yml) |
| Microsoft. AppConfiguration | core |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Servizio di attestazione di Azure |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automazione](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureData | Registro SQL Server |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing | [Gestione dei costi e fatturazione](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mappe](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Servizio Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure Blueprint](/azure/governance/blueprints/) |
| Microsoft.BotService | [Servizio Azure Bot](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis di Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rete per la distribuzione di contenuti (CDN)](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificati del servizio app](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Macchina virtuale del modello di distribuzione classica |
| Microsoft.ClassicInfrastructureMigrate | Migrazione del modello di distribuzione classica |
| Microsoft.ClassicNetwork | Rete virtuale del modello di distribuzione classica |
| Microsoft.ClassicStorage | Archiviazione modello di distribuzione classica |
| Microsoft. ClassicSubscription | Modello di distribuzione classica |
| Microsoft.CognitiveServices | [Servizi cognitivi](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Macchine virtuali](/azure/virtual-machines/)<br />[Set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [Gestione dei costi](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Istanze di Container](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registro Container](/azure/container-registry/) |
| Microsoft.ContainerService | [Servizio Azure Kubernetes](/azure/aks/) |
| Microsoft.CostManagement | [Gestione dei costi](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Gestione dei costi](/azure/cost-management/) |
| Microsoft. CustomerLockbox | Customer Lockbox per Microsoft Azure |
| Microsoft. CustomProviders | [Provider personalizzati di Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Servizio Migrazione del database di Azure](/azure/dms/) |
| Microsoft. DataShare | [Condivisione dati di Azure](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Database di Azure per MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Database di Azure per MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Database di Azure per PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Desktop virtuale Windows](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Deployment Manager di Azure](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Hub IoT Azure](/azure/iot-hub/)<br />[Servizio Device Provisioning in hub IoT di Azure](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Gemelli digitali di Azure](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Servizio app](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Grafico Knowledge base Enterprise |
| Microsoft.EventGrid | [Griglia di eventi](/azure/event-grid/) |
| Microsoft.EventHub | [Hub eventi](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Criteri di Azure](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA in istanze Large di Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Modulo di protezione hardware dedicato di Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [API di Azure per FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Importazione/Esportazione di Azure](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Gemelli digitali di Azure](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Servizio Azure Kubernetes](/azure/aks/) |
| Microsoft.Kusto | [Esplora dati di Azure](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [App per la logica](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Manutenzione di Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Gruppi di gestione](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mappe di Azure](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Servizi multimediali](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Ancoraggi nello spazio di Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway applicazione](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Protezione DDoS di Azure](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Firewall di Azure](../../firewall/index.yml)<br />[Servizio Frontdoor di Azure](../../frontdoor/index.yml)<br />[Collegamento privato di Azure](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Gestione traffico](../../traffic-manager/index.yml)<br />[Rete virtuale](../../virtual-network/index.yml)<br />[Rete WAN virtuale](../../virtual-wan/index.yml)<br />[Gateway VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [Hub di notifica](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft. peering | [Servizio peering di Azure](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Criteri di Azure](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Servizio di inoltro di Azure](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Diagramma delle risorse di Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Integrità dei servizi di Azure](../../service-health/index.yml)|
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Utilità di pianificazione](/azure/scheduler/) |
| Microsoft.Search | [Ricerca cognitiva di Azure](../../search/index.yml) |
| Microsoft.Security | [Centro sicurezza](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole | [Console seriale di Azure per Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Bus di servizio](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Servizio Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licenza |
| Microsoft.Solutions | [Applicazioni gestite di Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Database SQL di Azure](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server in Macchine virtuali di Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Archiviazione](../../storage/index.yml) |
| Microsoft. StorageCache | [Cache HPC di Azure](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Archiviazione](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Analisi di flusso di Azure](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft. sinapsi | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMwareCloudSimple | [Soluzione Azure VMware di CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [Servizio app](../../app-service/index.yml)<br />[Funzioni di Azure](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Servizi Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Monitoraggio di Azure](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](resource-providers-and-types.md)
