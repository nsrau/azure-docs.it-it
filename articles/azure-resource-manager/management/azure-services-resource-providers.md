---
title: Provider di risorse per servizi di Azure
description: Elenca tutti gli spazi dei nomi del provider di risorse per Azure Resource Manager e Mostra il servizio di Azure per lo spazio dei nomi.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: faea6badc24d0152e94fc507cafdb15932d1ea3e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452627"
---
# <a name="resource-providers-for-azure-services"></a>Provider di risorse per i servizi di Azure

Questo articolo illustra come eseguire il mapping degli spazi dei nomi del provider di risorse ai servizi di Azure.

## <a name="match-resource-provider-to-service"></a>Associare il provider di risorse al servizio

I provider di risorse contrassegnati con **-registered** sono registrati per impostazione predefinita per la sottoscrizione. Per ulteriori informazioni, vedere la pagina relativa alla [registrazione](#registration).

| Spazio dei nomi del provider di risorse | Servizio di Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Servizi di dominio Azure Active Directory](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft. ADHybridHealthService- [registrato](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [Gestione API](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Configurazione app di Azure](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Servizio di attestazione di Azure |
| Microsoft. Authorization- [registrato](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automazione](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Sistemi autonomi](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Soluzione Azure VMware](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | Registro SQL Server |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. Billing- [registrato](#registration) | [Gestione dei costi e fatturazione](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mappe](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Servizio Azure Blockchain](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Token di blockchain di Azure](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprint](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Servizio Azure Bot](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis di Azure](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rete per la distribuzione di contenuti](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificati del servizio app](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Macchina virtuale del modello di distribuzione classica |
| Microsoft.ClassicInfrastructureMigrate | Migrazione del modello di distribuzione classica |
| Microsoft.ClassicNetwork | Rete virtuale del modello di distribuzione classica |
| Microsoft.ClassicStorage | Archiviazione modello di distribuzione classica |
| Microsoft. ClassicSubscription- [registrato](#registration) | Modello di distribuzione classica |
| Microsoft.CognitiveServices | [Servizi cognitivi](../../cognitive-services/index.yml) |
| Microsoft. Commerce- [registrato](#registration) | core |
| Microsoft.Compute | [Macchine virtuali](../../virtual-machines/index.yml)<br />[Set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. consumo- [registrato](#registration) | [Gestione costi](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Istanze di Container](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Registro Container](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Servizio Azure Kubernetes](../../aks/index.yml) |
| Microsoft. CostManagement- [registrato](#registration) | [Gestione costi](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Gestione costi](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Customer Lockbox per Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Provider personalizzati di Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Analisi Data Lake](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Servizio Migrazione del database di Azure](../../dms/index.yml) |
| Microsoft.DataProtection | Protezione dei dati |
| Microsoft.DataShare | [Condivisione dati di Azure](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Database di Azure per MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Database di Azure per MySQL](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [Database di Azure per PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Deployment Manager di Azure](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Desktop virtuale Windows](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Hub IoT Azure](../../iot-hub/index.yml)<br />[Servizio Device Provisioning in hub IoT di Azure](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Gemelli digitali di Azure](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Servizio app](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [Servizi del ciclo di vita](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | Grafico Knowledge base Enterprise |
| Microsoft.EventGrid | [Griglia di eventi](../../event-grid/index.yml) |
| Microsoft.EventHub | [Hub eventi](../../event-hubs/index.yml) |
| Microsoft. Features- [registrato](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Criteri di Azure](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA in istanze Large di Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM dedicato di Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API di Azure per FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Zone perimetrali private](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Importazione/Esportazione di Azure](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Gemelli digitali di Azure](../../digital-twins/index.yml) |
| Microsoft.Intune | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Insieme di credenziali di chiave](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Servizio Azure Kubernetes](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Servizio Azure Kubernetes](../../aks/index.yml) |
| Microsoft.Kusto | [Esplora dati di Azure](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [App per la logica](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Manutenzione di Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | Reti virtuali gestite da servizi PaaS |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [Gruppi di gestione](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Mappe di Azure](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft. MarketplaceOrdering- [registrato](#registration) | core |
| Microsoft.Media | [Servizi multimediali](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Ancoraggi nello spazio di Azure](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway applicazione](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Protezione DDoS di Azure](../../ddos-protection/ddos-protection-overview.md)<br />[DNS di Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Firewall di Azure](../../firewall/index.yml)<br />[Servizio Frontdoor di Azure](../../frontdoor/index.yml)<br />[Collegamento privato di Azure](../../private-link/index.yml)<br />[Bilanciamento del carico](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Gestione traffico](../../traffic-manager/index.yml)<br />[Rete virtuale](../../virtual-network/index.yml)<br />[Rete WAN virtuale](../../virtual-wan/index.yml)<br />[Gateway VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft. Notebooks | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Hub di notifica di Azure](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | Archivio oggetti |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Monitoraggio di Azure](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Servizio di peering di Azure](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Criteri di Azure](../../governance/policy/index.yml) |
| Microsoft. Portal- [registrato](#registration) | [Portale di Azure](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Quantum di Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Inoltro di Azure](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph- [registrato](#registration) | [Diagramma delle risorse di Azure](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Integrità dei servizi di Azure](../../service-health/index.yml) |
| Microsoft. Resources- [registrato](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Utilità di pianificazione](../../scheduler/index.yml) |
| Microsoft.Search | [Ricerca cognitiva di Azure](../../search/index.yml) |
| Microsoft.Security | [Centro sicurezza](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole- [registrato](#registration) | [Console seriale di Azure per Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Bus di servizio](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Servizio Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Licenza |
| Microsoft.Solutions | [Applicazioni gestite di Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Database SQL di Azure](../../azure-sql/database/index.yml)<br /> [Istanza gestita di database SQL di Azure](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server in Macchine virtuali di Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageCache | [Cache HPC di Azure](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Analisi di flusso di Azure](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| Microsoft. support- [registrato](#registration) | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Soluzione Azure VMware](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Soluzione Azure VMware di CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [Servizio app](../../app-service/index.yml)<br />[Funzioni di Azure](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | Aggiornamenti della sicurezza estesi |
| Microsoft.WindowsIoT | [Servizi Windows 10 IoT Core](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Monitoraggio di Azure](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registrazione

I provider di risorse indicati in precedenza contrassegnati con **-registered** sono registrati per impostazione predefinita per la sottoscrizione. Per usare gli altri provider di risorse, è necessario [registrarli](resource-providers-and-types.md). Tuttavia, molti provider di risorse vengono registrati per l'utente quando si intraprendono determinate azioni. Se, ad esempio, si crea una risorsa tramite il portale, il portale registra automaticamente i provider di risorse non registrati necessari. Quando si distribuiscono risorse tramite un [modello di Azure Resource Manager](../templates/overview.md), vengono registrati anche tutti i provider di risorse richiesti.

> [!IMPORTANT]
> Registrare un provider di risorse solo quando si è pronti a usarlo. Il passaggio di registrazione consente di mantenere i privilegi minimi all'interno della sottoscrizione. Un utente malintenzionato non può usare i provider di risorse che non sono registrati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui provider di risorse, ad esempio su come registrare un provider di risorse, vedere [provider e tipi di risorse di Azure](resource-providers-and-types.md).
