---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000292"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta l'[assegnazione di tag](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>AAD Domain Services
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| domains | No  | 

## <a name="ad-hybrid-health-service"></a>Servizio ibrido per l'integrità di AD
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| addsservices | No  |
| aadsupportcases | No  | 
| agents | No  | 
| anonymousapiusers | No  | 
| configurazione | No  | 
| logs | No  | 
| reports | No  | 
| services | No  | 
| servicehealthmetrics | No  | 

## <a name="aks"></a>Servizio contenitore di Azure
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| managedClusters | Yes | 

## <a name="analysis-services"></a>Analysis Services
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 

## <a name="api-hubs"></a>Hub API
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| apiManagementAccounts | No  | 
| apiManagementAccounts/apis | No  | 
| apiManagementAccounts/connectionAcls | No  | 
| apiManagementAccounts/connectionProviders | No  | 
| apiManagementAccounts/connectionProviderAcls | No  | 
| apiManagementAccounts/connections | No  | 

## <a name="api-management"></a>Gestione API
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| service | Yes | 

## <a name="automation"></a>Automazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/jobs | No  | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/softwareUpdateConfigurations | No  | 
| automationAccounts/webhooks | No  | 

## <a name="azure-database-for-mariadb"></a>Database di Azure per MariaDB
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/configurations | No  |
| servers/databases | No  |
| servers/firewallRules | No  |
| servers/recoverableServers | No  | 
| servers/securityAlertPolicies | No  |
| servers/virtualNetworkRules | No  | 

## <a name="azure-database-for-mysql"></a>Database di Azure per MySQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/configurations | No  |
| servers/databases | No  |
| servers/firewallRules | No  |
| servers/recoverableServers | No  | 
| servers/securityAlertPolicies | No  |
| servers/virtualNetworkRules | No  | 

## <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/advisors | No  | 
| servers/configurations | No  |
| servers/databases | No  |
| servers/firewallRules | No  |
| servers/queryTexts | No  | 
| servers/recoverableServers | No  | 
| servers/securityAlertPolicies | No  |
| servers/topQueryStatistics | No  | 
| servers/virtualNetworkRules | No  | 
| servers/waitStatistics | No  | 

## <a name="batch"></a>Batch
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="bing-maps"></a>Bing Mappe
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| mapApis | Yes | 

## <a name="biztalk-services"></a>Servizi BizTalk
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| BizTalk | Yes | 

## <a name="cache"></a>Cache
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| Redis | Yes | 

## <a name="cdn"></a>RETE CDN
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| edgenodes | No  | 
| Profili | Yes | 
| profiles/endpoints | Yes | 
| profiles/endpoints/customdomains | No  | 
| profiles/endpoints/origins | No  | 
| validateProbe | No  | 

## <a name="classic-compute"></a>Calcolo classico
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| domainNames | No  | 
| domainNames/slots | No  | 
| domainNames/slots/roles | No  | 
| virtualMachines | No  | 
| virtualMachines/diagnosticSettings | No  | 
| virtualMachines/metricDefinitions | No  | 
| virtualMachines/metrics | No  | 

## <a name="classic-infrastructure-migrate"></a>Migrazione dell'infrastruttura classica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| classicInfrastructureResources | No  | 

## <a name="classic-network"></a>Rete classica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| virtualNetworks | No  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | No  | 
| virtualNetworks/virtualNetworkPeerings | No  | 

## <a name="classic-storage"></a>Archiviazione classica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageAccounts/services | No  | 
| storageAccounts/services/diagnosticSettings | No  | 

## <a name="compute"></a>Calcolo
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| availabilitySets | Yes | 
| disks | Yes | 
| images | Yes | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | No  | 
| sharedVMImages | Yes | 
| sharedVMImages/versions | Yes | 
| snapshots | Yes | 
| virtualMachines | Yes | 
| virtualMachines/diagnosticSettings | No  | 
| virtualMachines/extensions | Yes | 
| virtualMachines/metricDefinitions | No  | 
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/extensions | No  | 
| virtualMachineScaleSets/networkInterfaces | No  | 
| virtualMachineScaleSets/publicIPAddresses | No  | 
| virtualMachineScaleSets/virtualMachines | No  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | No  | 

## <a name="container"></a>Contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| containerGroups | Yes | 

## <a name="container-instance"></a>Istanza di contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | No  | 

## <a name="container-registry"></a>Registro di sistema del contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| registries | Yes | 
| registries/replications | Yes |
| registries/tasks | Yes |
| registries/webhooks | Yes |

## <a name="container-service"></a>Servizio contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| containerServices | Yes | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| account | Yes | 

## <a name="cosmos-db"></a>Cosmos DB
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| databaseAccounts | Yes | 
| databaseAccountNames | No  | 

## <a name="cost-management"></a>Gestione costi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| Connettori | Yes | 

## <a name="data-box"></a>Data Box
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="data-box-edge"></a>Data Box Edge
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="data-catalog"></a>Data Catalog
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| catalogs | Yes | 

## <a name="data-connect"></a>Connessione dati
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="data-factory"></a>Data Factory
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| dataFactories | Yes | 
| dataFactories/diagnosticSettings | No  | 
| dataFactories/metricDefinitions | No  | 
| dataFactorySchema | No  | 
| factories | Yes | 
| factories/integrationRuntimes | No  | 

## <a name="devices"></a>Dispositivi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| Hub IoT | Yes | 
| IotHubs/eventGridFilters | No  | 
| ProvisioningServices | Yes | 

## <a name="devspaces"></a>Devspaces
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| controllers | Yes | 

## <a name="devtest-lab"></a>Devtest Lab
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| labs | Yes | 
| labs/artifactsources | Yes |
| labs/costs | Yes |
| labs/customimages | Yes |
| labs/formulas | Yes |
| labs/notificationchannels | Yes |
| labs/policysets/policies | Yes |
| labs/schedules | Yes |
| labs/serviceRunners | Yes | 
| labs/users | Yes |
| labs/users/disks | Yes |
| labs/users/environments | Yes |
| labs/users/secrets | Yes |
| labs/users/servicefabrics | Yes |
| labs/users/servicefabrics/schedules | Yes |
| labs/virtualMachines | Yes | 
| labs/virtualmachines/schedules | Yes |
| labs/virtualnetworks | Yes |
| schedules | Yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| lcsprojects | No  | 
| lcsprojects/connectors | No  | 
| lcsprojects/clouddeployments | No  | 

## <a name="event-grid"></a>Griglia di eventi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| domains | Yes | 
| domains/topics | No  | 
| eventSubscriptions | No  | 
| extensionTopics | No  | 
| topics | Yes | 
| topicTypes | No  | 

## <a name="event-hub"></a>Hub eventi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| clusters | Yes | 
| spazi dei nomi | Yes | 
| namespaces/AuthorizationRules | No  |
| namespaces/disasterRecoveryConfigs | No  |
| namespaces/eventhubs | No  |
| namespaces/eventhubs/authorizationRules | No  |
| namespaces/eventhubs/consumergroups | No  |

## <a name="hana-on-azure"></a>HANA in Azure
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="hdinsight"></a>HDInsight
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/applications | No  | 

## <a name="import-export"></a>Importazione/esportazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| jobs | Yes | 

## <a name="insights"></a>Informazioni dettagliate
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| actionGroups | Yes |
| activityLogAlerts | Yes |
| alertrules | Yes |
| automatedExportSettings | No  | 
| components | Yes | 
| components/events | No  | 
| components/metrics | No  | 
| components/pricingPlans | No  | 
| components/query | No  | 
| logs | No  | 
| metricAlerts | Yes |
| migrateToNewPricingModel | No  | 
| myWorkbooks | No  | 
| query | No  | 
| rollbackToLegacyPricingModel | No  | 
| scheduledqueryrules | Yes | 
| webtests | Yes | 
| workbooks | Yes | 

## <a name="key-vault"></a>Key Vault
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| deletedVaults | No  | 
| insiemi di credenziali | Yes | 
| vaults/accessPolicies | No  | 
| vaults/secrets | No  | 

## <a name="log-analytics"></a>Log Analytics
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| logs | No  | 

## <a name="logic"></a>Logica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| integrationAccounts | Yes | 
| flussi di lavoro | Yes | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| aree di lavoro | Yes | 
| workspaces/computes | No  | 

## <a name="managed-identity"></a>Identità gestita
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| Identities | No  | 
| userAssignedIdentities | Yes | 

## <a name="marketplace-apps"></a>App del marketplace
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| classicDevServices | Yes | 

## <a name="marketplace-ordering"></a>Ordini marketplace
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| agreements | No  | 
| offertypes | No  | 

## <a name="media"></a>Contenuti multimediali
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| mediaservices | Yes | 
| mediaservices/accountFilters | No  | 
| mediaservices/assets | No  | 
| mediaservices/assets/assetFilters | No  | 
| mediaservices/contentKeyPolicies | No  | 
| mediaservices/eventGridFilters | No  | 
| mediaservices/liveEventOperations | No  | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | No  | 
| mediaservices/liveOutputOperations | No  | 
| mediaservices/streamingEndpoints | Yes | 
| mediaservices/streamingEndpointOperations | No  | 
| mediaservices/streamingLocators | No  | 
| mediaservices/streamingPolicies | No  | 
| mediaservices/transforms | No  | 
| mediaservices/transforms/jobs | No  | 

## <a name="network"></a>Rete
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| applicationGateways | Yes | 
| applicationSecurityGroups | Yes | 
| azureFirewalls | Yes | 
| connections | Yes | 
| ddosProtectionPlans | Yes | 
| expressRouteCircuits | Yes | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| interfaceEndpoints | Yes | 
| loadBalancers | Yes | 
| localNetworkGateways | Yes | 
| networkIntentPolicies | Yes | 
| networkInterfaces | Yes | 
| networkProfiles | Yes | 
| networkSecurityGroups | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Yes | 
| privateLinkServices | Yes | 
| publicIPAddresses | Yes | 
| publicIPPrefixes | Yes | 
| routeFilters | Yes | 
| routeTables | Yes | 
| serviceEndpointPolicies | Yes | 
| virtualHubs | Yes | 
| virtualNetworks | Yes | 
| virtualNetworkGateways | Yes | 
| virtualNetworkTaps | Yes | 
| virtualWans | Yes | 
| vpnGateways | Yes | 
| vpnSites | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="notification-hubs"></a>Hub di notifica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| spazi dei nomi | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="operational-insights"></a>Operational Insights
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| aree di lavoro | Yes |
| workspaces/dataSources | Yes |
| workspaces/linkedServices | Yes |
| workspaces/savedSearches | No  |
| workspaces/storageInsightConfigs | Yes |

## <a name="operations-management"></a>Operations Management
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| solutions | No  |

## <a name="portal"></a>Portale
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| dashboards | Yes | 

## <a name="portal-sdk"></a>SDK portale
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| rootResources | Yes | 

## <a name="power-bi"></a>Power BI
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="recovery-services"></a>Servizi di ripristino
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| backupProtectedItems | No  | 
| insiemi di credenziali | Yes | 

## <a name="relay"></a>Relay
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| spazi dei nomi | Yes | 

## <a name="resources"></a>Risorse
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| resourceGroups | Yes | 
| subscriptions/resourceGroups | Yes | 

## <a name="scheduler"></a>Utilità di pianificazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| jobcollections | Yes | 
| flows | Yes | 

## <a name="search"></a>Ricerca
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| resourceHealthMetadata | No  | 
| searchServices | Yes | 

## <a name="security"></a>Sicurezza
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| dataCollectionAgents | No  | 

## <a name="service-bus"></a>Bus di servizio
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| spazi dei nomi | Yes | 
| namespaces/eventgridfilters | No  | 

## <a name="service-fabric"></a>Service Fabric
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| clusters | Yes | 
| clusters/applications | No  | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| scala Web | Yes | 
| networks | Yes | 
| volumes | Yes | 

## <a name="signalr-service"></a>Servizio SignalR
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="site-recovery"></a>Site Recovery
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| SiteRecoveryVault | Yes | 

## <a name="solutions"></a>Soluzioni
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| scala Web | Yes | 
| applicationDefinitions | Yes | 
| jitRequests | Yes | 

## <a name="sql"></a>SQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| locations/instanceFailoverGroups | No  |
| managedInstances | Yes |
| managedInstances/databases | Yes |
| managedInstances/databases/backupShortTermRetentionPolicies | No  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | No  |
| managedInstances/databases/vulnerabilityAssessments | No  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | No  |
| managedInstances/encryptionProtector | No  |
| managedInstances/keys | No  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No  |
| managedInstances/vulnerabilityAssessments | No  |
| servers | Yes |
| servers/administrators | No  |
| servers/advisors | No  |
| servers/auditingSettings | No  |
| servers/backupLongTermRetentionVaults | No  |
| servers/communicationLinks | No  |
| servers/connectionPolicies | No  |
| servers/databases | Yes |
| servers/databases/advisors | No  |
| servers/databases/auditingSettings | No  |
| servers/databases/backupLongTermRetentionPolicies | No  |
| servers/databases/backupShortTermRetentionPolicies | No  |
| servers/databases/connectionPolicies | No  |
| servers/databases/dataMaskingPolicies | No  |
| servers/databases/dataMaskingPolicies/rules | No  |
| servers/databases/extendedAuditingSettings | No  |
| servers/databases/extensions | No  |
| servers/databases/geoBackupPolicies | No  |
| servers/databases/schemas/tables/columns/sensitivityLabels | No  |
| servers/databases/securityAlertPolicies | No  |
| servers/databases/syncGroups | No  |
| servers/databases/syncGroups/syncMembers | No  |
| servers/databases/transparentDataEncryption | No  |
| servers/databases/vulnerabilityAssessments | No  |
| servers/databases/vulnerabilityAssessments/rules/baselines | No  |
| servers/disasterRecoveryConfiguration | No  |
| servers/dnsAliases | No  |
| servers/elasticPools | Yes |
| servers/encryptionProtector | No  |
| servers/extendedAuditingSettings | No  |
| servers/failoverGroups | Yes |
| servers/firewallRules | No  |
| servers/jobAgents | Yes |
| servers/jobAgents/credentials | No  |
| servers/jobAgents/jobs | No  |
| servers/jobAgents/jobs/executions | No  |
| servers/jobAgents/jobs/steps | No  |
| servers/jobAgents/targetGroups | No  |
| servers/keys | No  |
| servers/securityAlertPolicies | No  |
| servers/syncAgents | No  |
| servers/virtualNetworkRules | No  |
| servers/vulnerabilityAssessments | No  |

## <a name="sql-virtual-machine"></a>Macchina virtuale SQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| DWVM | Yes | 

## <a name="storage"></a>Archiviazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | No  | 
| storageAccounts/fileServices | No  | 
| storageAccounts/queueServices | No  | 
| storageAccounts/services | No  | 
| storageAccounts/services/metricDefinitions | No  | 
| storageAccounts/tableServices | No  | 

## <a name="storage-sync"></a>Sincronizzazione archiviazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/registeredServers | No  | 
| storageSyncServices/syncGroups | No  | 
| storageSyncServices/syncGroups/cloudEndpoints | No  | 
| storageSyncServices/syncGroups/serverEndpoints | No  | 
| storageSyncServices/workflows | No  | 

## <a name="storsimple"></a>Storsimple
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| managers | Yes | 

## <a name="stream-analytics"></a>Analisi di flusso
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| streamingjobs | Yes | 
| streamingjobs/diagnosticSettings | No  | 
| streamingjobs/metricDefinitions | No  | 

## <a name="subscription"></a>Sottoscrizione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| SubscriptionDefinitions | No  | 
| SubscriptionOperations | No  | 

## <a name="support"></a>Supporto
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| supporttickets | No  | 

## <a name="visual-studio"></a>Visual Studio
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| account | Yes | 
| account/extension | Yes | 
| account/project | Yes | 

## <a name="web"></a>Web
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| apiManagementAccounts | No  | 
| apiManagementAccounts/apiAcls | No  | 
| apiManagementAccounts/apis | No  | 
| apiManagementAccounts/apis/apiAcls | No  | 
| apiManagementAccounts/apis/connectionAcls | No  | 
| apiManagementAccounts/apis/connections | No  | 
| apiManagementAccounts/apis/connections/connectionAcls | No  | 
| apiManagementAccounts/apis/localizedDefinitions | No  | 
| apiManagementAccounts/connectionAcls | No  | 
| apiManagementAccounts/connections | No  | 
| billingMeters | No  | 
| certificates | Yes | 
| connectionGateways | Yes | 
| connections | Yes | 
| customApis | Yes | 
| deletedSites | No  | 
| functions | No  | 
| hostingEnvironments | Yes | 
| hostingEnvironments/metrics | No  | 
| hostingEnvironments/multiRolePools | No  | 
| hostingEnvironments/workerPools | No  | 
| publishingUsers | No  | 
| serverFarms | Yes | 
| serverFarms/workers | No  | 
| siti | Yes | 
| sites/domainOwnershipIdentifiers | No  | 
| sites/hostNameBindings | No  | 
| sites/instances | No  | 
| sites/instances/extensions | No  | 
| sites/metrics | No  | 
| sites/premieraddons | Yes | 
| sites/slots | Yes | 
| sites/slots/hostNameBindings | No  | 
| sites/slots/instances | No  | 
| sites/slots/instances/extensions | No  | 
| sites/slots/metrics | No  | 
| sourceControls | No  | 
| validate | No  | 
| verifyHostingEnvironmentVnet | No  | 

## <a name="xrm"></a>XRM
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| organizations | No  | 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
