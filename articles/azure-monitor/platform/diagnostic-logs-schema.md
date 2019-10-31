---
title: Servizi e schemi supportati nei log delle risorse di Azure
description: Informazioni sullo schema di eventi e i servizi per i log di Diagnostica di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: 0031a0c96ecadbb3c7d3a479384bee92ba4d102c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161977"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Servizi, schemi e categorie supportati per i log delle risorse di Azure

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica.

I [log delle risorse di monitoraggio di Azure](../../azure-monitor/platform/resource-logs-overview.md) sono log emessi da servizi di Azure che descrivono il funzionamento di tali servizi o risorse. Tutti i log delle risorse disponibili tramite monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità che consente a ogni servizio di emettere proprietà univoche per gli eventi.

Una combinazione del tipo di risorsa (disponibile nella proprietà `resourceId`) e del `category` identifica in modo univoco uno schema. Questo articolo descrive lo schema di primo livello per i log delle risorse e i collegamenti a schemi per ogni servizio.

## <a name="top-level-resource-logs-schema"></a>Schema dei log delle risorse di livello superiore

| name | Obbligatorio/Facoltativo | Description |
|---|---|---|
| time | Obbligatoria | Il timestamp dell’evento (fuso UTC). |
| ResourceId | Obbligatoria | ID della risorsa che ha emesso l’evento. Per i servizi di tenant, questo ha la forma /tenants/tenant-id/providers/provider-name. |
| TenantId | Obbligatorio per i log di tenant | L'ID tenant del tenant di Active Directory associato a questo evento. Questa proprietà viene utilizzata solo per i log a livello di tenant, non viene visualizzata nei log a livello di risorsa. |
| operationName | Obbligatoria | Il nome dell'operazione rappresentata da questo evento. Se l'evento rappresenta un'operazione RBAC, si tratta del nome di operazione RBAC (ad es. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Tipicamente modellate sotto forma di operazione di Resource Manager, anche se non sono effettivamente operazioni documentate di Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facoltativo | La versione api associata all'operazione, se operationName è stato eseguito utilizzando un'API (ad es. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| category | Obbligatoria | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. Tipiche categorie di log sono "Controllo" "Operativo" "Esecuzione" e "Richiesta". |
| resultType | Facoltativo | Lo stato dell'evento. I valori tipici includono: Started, In Progress, Succeeded, Failed, Active e Resolved. |
| resultSignature | Facoltativo | Lo stato secondario dell'evento. Se questa operazione corrisponde a una chiamata API REST, questo è il codice di stato HTTP della chiamata REST corrispondente. |
| resultDescription | Facoltativo | Il testo statico che descrive questa operazione, ad es. "Recupera file di archiviazione". |
| durationMs | Facoltativo | La durata dell'operazione in millisecondi. |
| callerIpAddress | Facoltativo | L'indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP accessibile al pubblico. |
| correlationId | Facoltativo | Un GUID utilizzato per raggruppare un set di eventi correlati. In genere, se due eventi hanno lo stesso operationName ma due diversi stati (ad es. "Started" e "Succeeded"), condividono lo stesso ID di correlazione. Ciò può anche rappresentare altre relazioni tra gli eventi. |
| identità | Facoltativo | Un blob JSON che descrive l'identità dell'utente o dell'applicazione che ha eseguito l'operazione. In genere includerà l'autorizzazione e le attestazioni / token JWT da Active Directory. |
| Level | Facoltativo | Il livello di gravità dell'evento. Deve essere di tipo Informativo, Avviso, Errore o Critico. |
| location | Facoltativo | L'area della risorsa che emette l'evento, ad es. "Stati Uniti orientali" o "Francia meridionale" |
| properties | Facoltativo | Eventuali proprietà estese relative a questa particolare categoria di eventi. Tutte le proprietà personali/uniche devono essere inserite all'interno di questa "Parte B" dello schema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schemi specifici del servizio per i log delle risorse
Lo schema per i log di diagnostica di risorsa varia a seconda della risorsa e della categoria di log. Questo elenco Mostra tutti i servizi che rendono disponibili i log delle risorse e i collegamenti al servizio e allo schema specifico di categoria, se disponibili.

| Servizio | Schema e documenti |
| --- | --- |
| Azure Active Directory | [Panoramica](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema del [Registro di controllo](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [schema degli accessi](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Servizi di analisi | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestione API | [Log delle risorse di gestione API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateway applicazione |[Registrazione per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Analisi dei log per Automazione di Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione Azure Batch](../../batch/batch-diagnostics.md) |
| Database di Azure per MySQL | [Log di diagnostica di database di Azure per MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Database di Azure per PostgreSQL | [Log di database di Azure per PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Esplora dati di Azure | [Log di Azure Esplora dati](../../data-explorer/using-diagnostic-logs.md) |
| Servizi cognitivi | [Registrazione per servizi cognitivi di Azure](../../cognitive-services/diagnostic-logging.md) |
| Rete per la distribuzione di contenuti | [Log di Azure per la rete CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registrazione di Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorare le data factory con Monitoraggio di Azure](../../data-factory/monitor-using-azure-monitor.md) |
| Analisi Data Lake |[Accesso ai log per Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Archivio Data Lake |[Accesso ai log per Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hub eventi |[Log di hub eventi di Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Lo schema non è disponibile. |
| Firewall di Azure | Lo schema non è disponibile. |
| Hub IoT | [Operazioni dell'hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Registrazione di Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Bilanciamento del carico |[Analisi dei log per Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protezione DDoS | [Gestire la protezione DDoS di Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicato | [Registrazione per Power BI Embedded in Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Servizi di ripristino | [Modello di dati per Backup di Microsoft Azure](../../backup/backup-azure-reports-data-model.md)|
| Ricerca |[Abilitazione e uso di Analisi del traffico di ricerca](../../search/search-traffic-analytics.md) |
| Bus di servizio |[Log del bus di servizio di Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Database SQL | [Registrazione del database SQL di Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Analisi dei flussi |[Log del processo](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestione traffico | [Schema dei log di Gestione traffico](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Reti virtuali | Lo schema non è disponibile. |
| Gateway di rete virtuale | Lo schema non è disponibile. |

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa
|Tipo di risorsa|Categoria|Nome visualizzato della categoria|
|---|---|---|
|Microsoft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Engine (Motore)|Engine (Motore)|
|Microsoft.AnalysisServices/servers|Servizio|Servizio|
|Microsoft.ApiManagement/service|GatewayLogs|Log correlati ad ApiManagement Gateway|
|Microsoft. AppPlatform/Spring|ApplicationConsole|Console applicazione|
|Microsoft.Automation/automationAccounts|JobLogs|Log del processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi del processo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stato del nodo Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Log del servizio|
|Microsoft. BatchAI/Workspaces|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/Workspaces|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/Workspaces|BaiJobEvent|BaiJobEvent|
|Microsoft. blockchain/blockchainMembers|BlockchainApplication|Applicazione blockchain|
|Microsoft. blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento del flusso di regole del gruppo di sicurezza di rete|Evento del flusso di regole del gruppo di sicurezza di rete|
|Microsoft.CognitiveServices/accounts|Audit|Log di controllo|
|Microsoft.CognitiveServices/accounts|RequestResponse|Log richieste e risposte|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|Log RepositoryEvent|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|Eventi di accesso|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Server API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Strumento di gestione del controller Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Utilità di pianificazione Kubernetes|
|Microsoft.ContainerService/managedClusters|Kube-audit|Controllo Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Ridimensionamento automatico del cluster Kubernetes|
|Microsoft. databricks/area di lavoro|dBFS|File system di Databricks|
|Microsoft. databricks/area di lavoro|clusters|Cluster databricks|
|Microsoft. databricks/area di lavoro|account|Account databricks|
|Microsoft. databricks/area di lavoro|jobs|Processi Databricks|
|Microsoft. databricks/area di lavoro|Notebook|Notebook di Databricks|
|Microsoft. databricks/area di lavoro|ssh|SSH di databricks|
|Microsoft. databricks/area di lavoro|area di lavoro|Area di lavoro di Databricks|
|Microsoft. databricks/area di lavoro|chiavi private|Segreti di databricks|
|Microsoft. databricks/area di lavoro|sqlPermissions|Sqlpermissions di databricks|
|Microsoft. databricks/area di lavoro|instancePools|Pool di istanze|
|Microsoft. datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Log delle esecuzioni di attività pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Log delle esecuzioni di pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Log delle esecuzioni trigger|
|Microsoft.DataLakeAnalytics/accounts|Audit|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Richieste|Log delle richieste|
|Microsoft.DataLakeStore/accounts|Audit|Log di controllo|
|Microsoft.DataLakeStore/accounts|Richieste|Log delle richieste|
|Microsoft. DataShare/accounts|Condivisioni|Condivisioni|
|Microsoft. DataShare/accounts|ShareSubscriptions|Condividi sottoscrizioni|
|Microsoft. DataShare/accounts|SentShareSnapshots|Snapshot di condivisione inviati|
|Microsoft. DataShare/accounts|ReceivedShareSnapshots|Snapshot di condivisione ricevuti|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Log server MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Log di controllo di MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Log del server PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Statistiche di runtime Query Store PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Statistiche attesa Query Store PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|PostgreSQLLogs|Log del server PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statistiche di runtime Query Store PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Statistiche attesa Query Store PostgreSQL|
|Microsoft. DesktopVirtualization/Workspaces|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/Workspaces|Errore|Errore|
|Microsoft. DesktopVirtualization/Workspaces|Gestione|Gestione|
|Microsoft. DesktopVirtualization/Workspaces|Feed|Feed|
|Microsoft. DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/applicationGroups|Errore|Errore|
|Microsoft. DesktopVirtualization/applicationGroups|Gestione|Gestione|
|Microsoft. DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/hostPools|Errore|Errore|
|Microsoft. DesktopVirtualization/hostPools|Gestione|Gestione|
|Microsoft. DesktopVirtualization/hostPools|Connessione|Connessione|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|connessioni|connessioni|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria dei dispositivi|
|Microsoft.Devices/IotHubs|C2DCommands|Comandi da cloud a dispositivo|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operazioni relative alle identità dei dispositivi|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operazioni di caricamento file|
|Microsoft.Devices/IotHubs|Route|Route|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operazioni da cloud a dispositivi gemelli|
|Microsoft.Devices/IotHubs|TwinQueries|Query dei dispositivi gemelli|
|Microsoft.Devices/IotHubs|JobsOperations|Operazioni dei processi|
|Microsoft.Devices/IotHubs|DirectMethods|Metodi diretti|
|Microsoft.Devices/IotHubs|DistributedTracing|Traccia distribuita (anteprima)|
|Microsoft.Devices/IotHubs|Configurazioni|Configurazioni|
|Microsoft.Devices/IotHubs|DeviceStreams|Flussi del dispositivo (anteprima)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operazioni del dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operazioni di servizio|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/Services|AuditEvent|Log AuditEvent|
|Microsoft. EnterpriseKnowledgeGraph/Services|Problema di dataissue|Log del problema di dataissue|
|Microsoft. EnterpriseKnowledgeGraph/Services|Richieste|Log di configurazione|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log di archiviazione|
|Microsoft.EventHub/namespaces|OperationalLogs|Log operativi|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Log di scalabilità automatica|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Log coordinatore Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Log degli errori utente Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|VNet/IP filtraggio dei log di connessione|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Log delle chiavi gestite dal cliente|
|Microsoft. HealthcareApis/Services|AuditLogs|Log di controllo|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Valutazioni sulla scalabilità automatica|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Azioni di ridimensionamento per la scalabilità automatica|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Operativo|Operativo|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Ingresso|Ingresso|
|Microsoft.IoTSpaces/Graph|Egress|Egress|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Kusto/Clusters|SucceededIngestion|Operazioni di inserimento riuscite|
|Microsoft.Kusto/Clusters|FailedIngestion|Operazioni di inserimento non riuscite|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/MediaServices|KeyDeliveryRequests|Richieste di distribuzione delle chiavi|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento del flusso di regole del gruppo di sicurezza di rete|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notifiche di protezione DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Flusso di log di decisioni di mitigazione DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Report soluzioni di prevenzione DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Avvisi di protezione VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regola di applicazione di Firewall di Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regola di rete di Firewall di Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Log di diagnostica del tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Log di diagnostica della route|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Log di diagnostica P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento dei risultati di integrità dei probe di Traffic Manager|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Log delle tabelle di routing di peering|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Log di diagnostica del tunnel|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Log di diagnostica della route|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log di accesso a Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log del Web Application Firewall di Frontdoor|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|Log di diagnostica P2S|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Log di controllo Bastion|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|
|Microsoft.PowerBIDedicated/capacities|Engine (Motore)|Engine (Motore)|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dati dei report di Backup di Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Dati di backup di Azure di base|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Dati del processo di backup di Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Dati di avviso di backup di Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Dati dei criteri di backup di Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Dati di archiviazione di backup di Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Dati dell'istanza protetta del backup di Azure addon|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Processi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementi replicati di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiche di replica di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punti di ripristino di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Velocità di caricamento dei dati di replica di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Varianza dei dati del disco protetti di Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Log delle operazioni|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Log operativi|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ottimizzazione automatica|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiche relative alle attese del database|
|Microsoft.Sql/servers/databases|Timeout|Timeout|
|Microsoft.Sql/servers/databases|Blocks|Blocks|
|Microsoft.Sql/servers/databases|Deadlock|Deadlock|
|Microsoft.Sql/servers/databases|Audit|Log di controllo|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Ruoli di lavoro del servizio di Migrazione del database|
|Microsoft.Sql/servers/databases|ExecRequests|Richieste di esecuzione|
|Microsoft.Sql/servers/databases|RequestSteps|Procedura per la richiesta|
|Microsoft.Sql/servers/databases|SqlRequests|Richieste SQL|
|Microsoft.Sql/servers/databases|In attesa|In attesa|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistiche di utilizzo delle risorse|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione|Creazione|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Log della piattaforma ambiente del servizio app|
|microsoft.web/sites|FunctionAppLogs|Log dell'applicazione per le funzioni|
|microsoft.web/sites|AppServiceHTTPLogs|Log HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Log della console del servizio app|
|microsoft.web/sites|AppServiceAppLogs|Log applicazioni del servizio app|
|microsoft.web/sites|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|
|microsoft.web/sites|AppServiceAuditLogs|Accedi ai log di controllo|
|microsoft.web/sites/slots|FunctionAppLogs|Log dell'applicazione per le funzioni|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Log HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Log della console|
|microsoft.web/sites/slots|AppServiceAppLogs|Log applicazioni|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|
|microsoft.web/sites/slots|AppServiceAuditLogs|Accedi ai log di controllo|

## <a name="next-steps"></a>Fasi successive

* [Altre informazioni sui log delle risorse](../../azure-monitor/platform/resource-logs-overview.md)
* [Trasmettere i log delle risorse delle risorse a **Hub eventi**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Modificare le impostazioni di diagnostica del log delle risorse usando l'API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
