---
title: Log delle risorse di Azure servizi e schemi supportatiAzure Resource Logs supported services and schemas
description: Comprendere i servizi supportati e lo schema di eventi per i log delle risorse di Azure.Understand the supported services and event schema for Azure resource logs.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 8abd8767d9bb7e3c4336f6600b94f6b3f4ea48f1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380511"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Servizi, schemi e categorie supportati per i log delle risorse di AzureSupported services, schemas, and categories for Azure Resource Logs

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica.

[I log delle risorse](../../azure-monitor/platform/platform-logs-overview.md) di Monitoraggio di Azure sono log generati dai servizi di Azure che descrivono il funzionamento di tali servizi o risorse. Tutti i log delle risorse disponibili tramite Monitoraggio di Azure condividono uno schema di primo livello comune, con flessibilità per ogni servizio di generare proprietà univoche per i propri eventi.

Una combinazione del tipo di risorsa (disponibile nella proprietà `resourceId`) e del `category` identifica in modo univoco uno schema. In questo articolo viene descritto lo schema di primo livello per i log delle risorse e collegamenti allo schemata per ogni servizio.

## <a name="top-level-resource-logs-schema"></a>Schema dei log delle risorse di primo livello

| Nome | Obbligatorio/Facoltativo | Descrizione |
|---|---|---|
| time | Obbligatoria | Il timestamp dell’evento (fuso UTC). |
| resourceId | Obbligatoria | ID della risorsa che ha emesso l’evento. Per i servizi di tenant, questo ha la forma /tenants/tenant-id/providers/provider-name. |
| TenantId | Obbligatorio per i log di tenant | L'ID tenant del tenant di Active Directory associato a questo evento. Questa proprietà viene utilizzata solo per i log a livello di tenant, non viene visualizzata nei log a livello di risorsa. |
| operationName | Obbligatoria | Il nome dell'operazione rappresentata da questo evento. Se l'evento rappresenta un'operazione RBAC, si tratta del nome di operazione RBAC (ad es. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Tipicamente modellate sotto forma di operazione di Resource Manager, anche se non sono effettivamente operazioni documentate di Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facoltativo | La versione api associata all'operazione, se operationName è stato eseguito utilizzando un'API (ad es. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| category | Obbligatoria | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. Le categorie di log tipiche sono "Audit" "Operational" "Execution" e "Request". |
| resultType | Facoltativo | Lo stato dell'evento. I valori tipici includono: Started, In Progress, Succeeded, Failed, Active e Resolved. |
| resultSignature | Facoltativo | Lo stato secondario dell'evento. Se questa operazione corrisponde a una chiamata API REST, questo è il codice di stato HTTP della chiamata REST corrispondente. |
| resultDescription | Facoltativo | Il testo statico che descrive questa operazione, ad es. "Ottieni file di archiviazione." |
| durationMs | Facoltativo | La durata dell'operazione in millisecondi. |
| callerIpAddress | Facoltativo | L'indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP accessibile al pubblico. |
| correlationId | Facoltativo | Un GUID utilizzato per raggruppare un set di eventi correlati. In genere, se due eventi hanno lo stesso operationName ma due diversi stati (ad es. "Started" e "Succeeded"), condividono lo stesso ID di correlazione. Ciò può anche rappresentare altre relazioni tra gli eventi. |
| identity | Facoltativo | Un blob JSON che descrive l'identità dell'utente o dell'applicazione che ha eseguito l'operazione. In genere includerà l'autorizzazione e le attestazioni / token JWT da Active Directory. |
| Level | Facoltativo | Il livello di gravità dell'evento. Deve essere di tipo Informativo, Avviso, Errore o Critico. |
| posizione | Facoltativo | L'area della risorsa che emette l'evento, ad es. "Stati Uniti orientali" o "Francia sud" |
| properties | Facoltativo | Eventuali proprietà estese relative a questa particolare categoria di eventi. Tutte le proprietà personalizzate/univoche devono essere inserite in questa "Parte B" dello schema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schemi specifici del servizio per i log delle risorseService-specific schemas for resource logs
Lo schema per i log di diagnostica di risorsa varia a seconda della risorsa e della categoria di log. Questo elenco mostra tutti i servizi che rendono disponibili i log delle risorse e i collegamenti allo schema specifico del servizio e della categoria, se disponibili.

| Service | Schema e documenti |
| --- | --- |
| Azure Active Directory | [Panoramica](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schema del log](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) di controllo e schema degli [invii](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestione API | [Registri delle risorse di Gestione API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateway applicazione |[Registrazione per il gateway applicazioneLogging for Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Log analytics for Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione batch di AzureAzure Batch logging](../../batch/batch-diagnostics.md) |
| Database di Azure per MySQL | [Log di diagnostica di database di Azure per MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Database di Azure per PostgreSQL | [Database di Azure per i log PostgreSQLAzure Database for PostgreSQL logs](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Esplora dati di Azure | [Log di Azure Data ExplorerAzure Data Explorer logs](/azure/data-explorer/using-diagnostic-logs) |
| Servizi cognitivi | [Registrazione per servizi cognitivi di AzureLogging for Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Registro Container | [Registrazione per il Registro di sistema del contenitore di AzureLogging for Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rete per la distribuzione di contenuti (CDN) | [Azure Logs for CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Registrazione di Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitor Data Factories using Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accesso ai log per Azure Data Lake AnalyticsAccessing logs for Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accesso ai log per Azure Data Lake StoreAccessing logs for Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hub eventi |[Registri di Hub di AzureAzure Event Hubs logs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Lo schema non è disponibile. |
| Firewall di Azure | Lo schema non è disponibile. |
| Hub IoT | [Operazioni dell'hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registrazione dell'insieme di credenziali delle chiavi di AzureAzure Key Vault Logging](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes Logging](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Analisi dei log per il servizio di bilanciamento del carico di Azure](../../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protezione DDoS | [Gestire la protezione DDoS di Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicato | [Registrazione per Power BI Embedded in AzureLogging for Power BI Embedded in Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Servizi di ripristino | [Modello di dati per il backup di AzureData Model for Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Ricerca |[Abilitazione e uso di Analisi del traffico di ricerca](../../search/search-traffic-analytics.md) |
| Bus di servizio |[Registri del bus di servizio di AzureAzure Service Bus logs](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Database SQL | [Registrazione del database SQL di AzureAzure SQL Database logging](../../sql-database/sql-database-metrics-diag-logging.md) |
| Analisi di flusso |[Log di processo](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestione traffico | [Schema del registro di Gestione traffico](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Reti virtuali | Lo schema non è disponibile. |
| Gateway di rete virtuale | Lo schema non è disponibile. |

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa

Alcune categorie possono essere supportate solo per tipi specifici di risorse. Questo è l'elenco di tutti coloro che sono disponibili in qualche forma.  Ad esempio, Le categorie Microsoft.Sql/servers/database non sono disponibili per tutti i tipi di database. Per ulteriori informazioni, vedere [informazioni sulla registrazione diagnostica del database SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Tipo di risorsa|Category|Nome visualizzato della categoria|
|---|---|---|
|Microsoft.AAD/domainServices|SystemSecurity (Protezione di sistema)|SystemSecurity (Protezione di sistema)|
|Microsoft.AAD/domainServices|Accountmanagement|Accountmanagement|
|Microsoft.AAD/domainServices|LogonLogoff (Informazioni in base alla finestra utente|LogonLogoff (Informazioni in base alla finestra utente|
|Microsoft.AAD/domainServices|ObjectAccess (Accesso a oggetti)|ObjectAccess (Accesso a oggetti)|
|Microsoft.AAD/domainServices|PolicyChange (Modifica dei criteri)|PolicyChange (Modifica dei criteri)|
|Microsoft.AAD/domainServices|PrivilegiOUso|PrivilegiOUso|
|Microsoft.AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft.AAD/domainServices|Accesso Al servizio directory|Accesso Al servizio directory|
|Microsoft.AAD/domainServices|AccountLogon (accesso account)|AccountLogon (accesso account)|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Motore|Motore|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Log correlati ad ApiManagement Gateway|
|Microsoft.AppPlatform/Primavera|Console Applicazione|Console applicazione|
|Microsoft.Automation/automationAccounts|JobLogs|Log del processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi del processo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stato del nodo Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Log del servizio|
|Microsoft.BatchAI/aree di lavoro|Evento BaiCluster|Evento BaiCluster|
|Microsoft.BatchAI/aree di lavoro|Evento BaiClusterNodeEvent|Evento BaiClusterNodeEvent|
|Microsoft.BatchAI/aree di lavoro|Evento BaiJob|Evento BaiJob|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|Applicazione Blockchain|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento del flusso di regole del gruppo di sicurezza di rete|Evento del flusso di regole del gruppo di sicurezza di rete|
|Microsoft.CognitiveServices/accounts|Audit|Log di controllo|
|Microsoft.CognitiveServices/accounts|RequestResponse|Log richieste e risposte|
|Microsoft.ContainerRegistry/registries|Contenitori RepositoryEventi|Registri di RepositoryEvent (anteprima)RepositoryEvent logs (Preview)|
|Microsoft.ContainerRegistry/registries|ContenitoreRegistryLoginEvents|Eventi di accesso (anteprima)Login Events (Preview)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Server API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Strumento di gestione del controller Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Utilità di pianificazione Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-audit|Controllo Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Ridimensionamento automatico del cluster Kubernetes|
|Microsoft.Databricks/spazi di lavoro|Dbfs|File system di Databricks|
|Microsoft.Databricks/spazi di lavoro|clusters|Cluster di Databrick|
|Microsoft.Databricks/spazi di lavoro|account|Conti Databricks|
|Microsoft.Databricks/spazi di lavoro|jobs|Lavori Databricks|
|Microsoft.Databricks/spazi di lavoro|notebook|Notebook di Databricks|
|Microsoft.Databricks/spazi di lavoro|ssh|Databricks SSH|
|Microsoft.Databricks/spazi di lavoro|area di lavoro|Area di lavoro Databricks|
|Microsoft.Databricks/spazi di lavoro|chiavi private|Segreti di Databricks|
|Microsoft.Databricks/spazi di lavoro|autorizzazioni sql|Databricks SQLPermissions|
|Microsoft.Databricks/spazi di lavoro|instancePools (reinsedi|Pool di istanze|
|Microsoft.DataCatalog/datacatalogs|Evento ScanStatusLog|Stato di scansione|
|Microsoft.DataFactory/factories|ActivityRuns|Log delle esecuzioni di attività pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Log delle esecuzioni di pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Log delle esecuzioni trigger|
|Microsoft.DataLakeAnalytics/accounts|Audit|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Requests|Log delle richieste|
|Microsoft.DataLakeStore/accounts|Audit|Log di controllo|
|Microsoft.DataLakeStore/accounts|Requests|Log delle richieste|
|Microsoft.DataShare/accounts|Condivisioni|Condivisioni|
|Microsoft.DataShare/accounts|ShareSubscriptions|Condividi abbonamenti|
|Microsoft.DataShare/accounts|SentShareSnapshots|Snapshot di condivisione inviati|
|Microsoft.DataShare/accounts|ReceivedShareSnapshots|Snapshot di condivisione ricevuti|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Log server MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Registri di controllo MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Log del server PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Statistiche di runtime dell'archivio query PostgreSQLPostgreSQL Query Store Runtime Statistics|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Statistiche di attesa dell'archivio query PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Log del server PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statistiche di runtime dell'archivio query PostgreSQLPostgreSQL Query Store Runtime Statistics|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Statistiche di attesa dell'archivio query PostgreSQL|
|Microsoft.DesktopVirtualization/aree di lavoro|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/aree di lavoro|Errore|Errore|
|Microsoft.DesktopVirtualization/aree di lavoro|Gestione|Gestione|
|Microsoft.DesktopVirtualization/aree di lavoro|Feed|Feed|
|Microsoft.DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/applicationGroups|Errore|Errore|
|Microsoft.DesktopVirtualization/applicationGroups|Gestione|Gestione|
|Microsoft.DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/hostPools|Errore|Errore|
|Microsoft.DesktopVirtualization/hostPools|Gestione|Gestione|
|Microsoft.DesktopVirtualization/hostPools|Connessione|Connessione|
|Microsoft.DesktopVirtualization/hostPools|Registrazione host|Registrazione host|
|Microsoft.Devices/IotHubs|Connessioni|Connessioni|
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
|Microsoft.Devices/IotHubs|DeviceStreams|Flussi di dispositivi (anteprima)Device Streams (Preview)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operazioni del dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operazioni di servizio|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/servizi|AuditEvent|Registro AuditEvent|
|Microsoft.EnterpriseKnowledgeGraph/servizi|DataIssue|Registro DataIssue|
|Microsoft.EnterpriseKnowledgeGraph/servizi|Requests|Registro di configurazione|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log di archiviazione|
|Microsoft.EventHub/namespaces|OperationalLogs|Log operativi|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Log di scalabilità automatica|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Registri del Coordinatore Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Registri degli errori utente Kafka|
|Microsoft.EventHub/namespaces|EventoHubVNetConnectionEvent|Registri di connessione filtro VNet/IP|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Registri delle chiavi gestite dal cliente|
|Microsoft.HealthcareApis/servizi|AuditLogs|Log di controllo|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Valutazioni sulla scalabilità automatica|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Azioni di ridimensionamento per la scalabilità automatica|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Operativo|Operativo|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Dati in ingresso|Dati in ingresso|
|Microsoft.IoTSpaces/Graph|Egress|Egress|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Kusto/Clusters|Ingestione riuscita|Operazioni di inserimento riuscite|
|Microsoft.Kusto/Clusters|Ingestione non riuscitaFailedIngestion|Operazioni di inserimento non riuscite|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent (evento AmlComputeClusterNodeEvent)|AmlComputeClusterNodeEvent (evento AmlComputeClusterNodeEvent)|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/Mediaservices (Servizi multimediali)|KeyDeliveryRequests (Richieste di recapito di keyDelivery|Richieste di recapito delle chiaviKey Delivery Requests|
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
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Log di diagnostica del tunnel|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Log di diagnostica della route|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log di accesso a Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log del Web Application Firewall di Frontdoor|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|Log di diagnostica P2S|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Registri di controllo del bastione|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|
|Microsoft.PowerBIDedicated/capacities|Motore|Motore|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dati dei report di Backup di Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Dati di base di Backup di Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobsAddonAzureBackupJobs|Dati del processo di backup di Azure Addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Addon Azure Backup Alert Data|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicyAddonAzureBackupPolicy|Dati dei criteri di backup di Azure Addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorageAddonAzureBackupStorage|Addon Azure Backup Storage Data|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstanceAddonAzureBackupProtectedInstance|Dati dell'istanza protetta di Backup di AzureAddon Azure Backup Protected Instance Data|
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
|Microsoft.Sql/servers/databases|Blocchi|Blocchi|
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
|Microsoft.Storage/storageAccounts/tableServices|StorageElimina|StorageElimina|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageElimina|StorageElimina|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageElimina|StorageElimina|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageElimina|StorageElimina|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione|Creazione|
|microsoft.web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Registri della piattaforma dell'ambiente del servizio app|
|microsoft.web/sites|FunzioniRegistri|Registri delle applicazioni delle funzioni|
|microsoft.web/sites|AppServiceHTTPLogs|Registri HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Registri della console del servizio app|
|microsoft.web/sites|AppServiceAppLogs|Registri dell'applicazione del servizio app|
|microsoft.web/sites|AppServiceFileAuditLogs|Registri di controllo delle modifiche del contenuto del sito|
|microsoft.web/sites|AppServiceAuditLogs|Accedere ai registri di controlloAccess Audit Logs|
|microsoft.web/sites/slots|FunzioniRegistri|Registri delle applicazioni delle funzioni|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Registri HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Registri della console|
|microsoft.web/sites/slots|AppServiceAppLogs|Log applicazioni|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Registri di controllo delle modifiche del contenuto del sito|
|microsoft.web/sites/slots|AppServiceAuditLogs|Accedere ai registri di controlloAccess Audit Logs|

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log delle risorseLearn more about resource logs](../../azure-monitor/platform/platform-logs-overview.md)
* [Trasmettere i log delle risorse agli **hub eventi**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Modificare le impostazioni di diagnostica del log delle risorse usando l'API REST di Monitoraggio di AzureChange resource log diagnostic settings using the Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
