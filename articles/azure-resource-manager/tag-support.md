---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 580955d3c6fd7a33c152e49e601d8078eb169a22
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409667"
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
| services | No  | 
| addsservices | No  | 
| configurazione | No  | 
| agents | No  | 
| aadsupportcases | No  | 
| reports | No  | 
| servicehealthmetrics | No  | 
| logs | No  | 
| anonymousapiusers | No  | 

## <a name="analysis-services"></a>Analysis Services
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 

## <a name="api-hubs"></a>Hub API
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| apiManagementAccounts | No  | 
| apiManagementAccounts/connectionProviders | No  | 
| apiManagementAccounts/connections | No  | 
| apiManagementAccounts/connectionAcls | No  | 
| apiManagementAccounts/connectionProviderAcls | No  | 
| apiManagementAccounts/apis | No  | 

## <a name="api-management"></a>Gestione API
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| service | Yes | 

## <a name="automation"></a>Automazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/webhooks | No  | 
| automationAccounts/softwareUpdateConfigurations | No  | 
| automationAccounts/jobs | No  | 

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
| Profili | Yes | 
| profiles/endpoints | Yes | 
| profiles/endpoints/origins | No  | 
| profiles/endpoints/customdomains | No  | 
| validateProbe | No  | 
| edgenodes | No  | 

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
| virtualNetworks/virtualNetworkPeerings | No  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | No  | 

## <a name="classic-storage"></a>Archiviazione classica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageAccounts/services | No  | 
| storageAccounts/services/diagnosticSettings | No  | 

## <a name="compute"></a>Calcolo
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| availabilitySets | Yes | 
| virtualMachines | Yes | 
| virtualMachines/extensions | Yes | 
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/extensions | No  | 
| virtualMachineScaleSets/virtualMachines | No  | 
| virtualMachineScaleSets/networkInterfaces | No  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | No  | 
| virtualMachineScaleSets/publicIPAddresses | No  | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | No  | 
| virtualMachines/diagnosticSettings | No  | 
| virtualMachines/metricDefinitions | No  | 
| sharedVMImages | Yes | 
| sharedVMImages/versions | Yes | 
| disks | Yes | 
| snapshots | Yes | 
| images | Yes | 

## <a name="container"></a>Contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| containerGroups | Yes | 

## <a name="container-instance"></a>Istanza di contenitore
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | No  | 

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
| factories | Yes | 
| factories/integrationRuntimes | No  | 
| dataFactories/diagnosticSettings | No  | 
| dataFactories/metricDefinitions | No  | 
| dataFactorySchema | No  | 

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
| schedules | Yes | 
| labs/virtualMachines | Yes | 
| labs/serviceRunners | Yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| lcsprojects | No  | 
| lcsprojects/connectors | No  | 
| lcsprojects/clouddeployments | No  | 

## <a name="event-grid"></a>Griglia di eventi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| eventSubscriptions | No  | 
| topics | Yes | 
| domains | Yes | 
| domains/topics | No  | 
| topicTypes | No  | 
| extensionTopics | No  | 

## <a name="event-hub"></a>Hub eventi
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| spazi dei nomi | Yes | 
| clusters | Yes | 

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
| components | Yes | 
| components/query | No  | 
| components/metrics | No  | 
| components/events | No  | 
| webtests | Yes | 
| query | No  | 
| scheduledqueryrules | Yes | 
| components/pricingPlans | No  | 
| migrateToNewPricingModel | No  | 
| rollbackToLegacyPricingModel | No  | 
| automatedExportSettings | No  | 
| workbooks | Yes | 
| myWorkbooks | No  | 
| logs | No  | 

## <a name="key-vault"></a>Key Vault
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| insiemi di credenziali | Yes | 
| vaults/secrets | No  | 
| vaults/accessPolicies | No  | 
| deletedVaults | No  | 

## <a name="log-analytics"></a>Log Analytics
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| logs | No  | 

## <a name="logic"></a>Logica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| flussi di lavoro | Yes | 
| integrationAccounts | Yes | 

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

## <a name="mariadb"></a>MariaDB
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | No  | 
| servers/virtualNetworkRules | No  | 

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
| mediaservices/assets | No  | 
| mediaservices/contentKeyPolicies | No  | 
| mediaservices/streamingLocators | No  | 
| mediaservices/streamingPolicies | No  | 
| mediaservices/eventGridFilters | No  | 
| mediaservices/transforms | No  | 
| mediaservices/transforms/jobs | No  | 
| mediaservices/streamingEndpoints | Yes | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | No  | 
| mediaservices/streamingEndpointOperations | No  | 
| mediaservices/liveEventOperations | No  | 
| mediaservices/liveOutputOperations | No  | 
| mediaservices/assets/assetFilters | No  | 
| mediaservices/accountFilters | No  | 

## <a name="mysql"></a>MySQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | No  | 
| servers/virtualNetworkRules | No  | 

## <a name="network"></a>Rete
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| virtualNetworks | Yes | 
| publicIPAddresses | Yes | 
| networkInterfaces | Yes | 
| interfaceEndpoints | Yes | 
| loadBalancers | Yes | 
| networkSecurityGroups | Yes | 
| applicationSecurityGroups | Yes | 
| serviceEndpointPolicies | Yes | 
| networkIntentPolicies | Yes | 
| routeTables | Yes | 
| publicIPPrefixes | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Yes | 
| virtualNetworkGateways | Yes | 
| localNetworkGateways | Yes | 
| connections | Yes | 
| applicationGateways | Yes | 
| expressRouteCircuits | Yes | 
| routeFilters | Yes | 
| virtualWans | Yes | 
| vpnSites | Yes | 
| virtualHubs | Yes | 
| vpnGateways | Yes | 
| azureFirewalls | Yes | 
| virtualNetworkTaps | Yes | 
| privateLinkServices | Yes | 
| ddosProtectionPlans | Yes | 
| networkProfiles | Yes | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="notification-hubs"></a>Hub di notifica
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| spazi dei nomi | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="portal"></a>Portale
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| dashboards | Yes | 

## <a name="portal-sdk"></a>SDK portale
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| rootResources | Yes | 

## <a name="postgresql"></a>PostgreSQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| servers | Yes | 
| servers/recoverableServers | No  | 
| servers/virtualNetworkRules | No  | 
| servers/topQueryStatistics | No  | 
| servers/queryTexts | No  | 
| servers/waitStatistics | No  | 
| servers/advisors | No  | 

## <a name="power-bi"></a>Power BI
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="recovery-services"></a>Servizi di ripristino
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| insiemi di credenziali | Yes | 
| backupProtectedItems | No  | 

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
| searchServices | Yes | 
| resourceHealthMetadata | No  | 

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

## <a name="sql-virtual-machine"></a>Macchina virtuale SQL
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| DWVM | Yes | 

## <a name="storage"></a>Archiviazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | No  | 
| storageAccounts/tableServices | No  | 
| storageAccounts/queueServices | No  | 
| storageAccounts/fileServices | No  | 
| storageAccounts/services | No  | 
| storageAccounts/services/metricDefinitions | No  | 

## <a name="storage-sync"></a>Sincronizzazione archiviazione
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/syncGroups | No  | 
| storageSyncServices/syncGroups/cloudEndpoints | No  | 
| storageSyncServices/syncGroups/serverEndpoints | No  | 
| storageSyncServices/registeredServers | No  | 
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
| account/project | Yes | 
| account/extension | Yes | 
| account | Yes | 
| account/project | Yes | 
| account/extension | Yes | 

## <a name="web"></a>Web
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| sites/instances | No  | 
| sites/slots/instances | No  | 
| sites/instances/extensions | No  | 
| sites/slots/instances/extensions | No  | 
| publishingUsers | No  | 
| validate | No  | 
| sourceControls | No  | 
| sites/hostNameBindings | No  | 
| sites/domainOwnershipIdentifiers | No  | 
| sites/slots/hostNameBindings | No  | 
| certificates | Yes | 
| serverFarms | Yes | 
| serverFarms/workers | No  | 
| siti | Yes | 
| sites/slots | Yes | 
| sites/metrics | No  | 
| sites/slots/metrics | No  | 
| sites/premieraddons | Yes | 
| hostingEnvironments | Yes | 
| hostingEnvironments/multiRolePools | No  | 
| hostingEnvironments/workerPools | No  | 
| hostingEnvironments/metrics | No  | 
| functions | No  | 
| deletedSites | No  | 
| apiManagementAccounts | No  | 
| apiManagementAccounts/connections | No  | 
| apiManagementAccounts/connectionAcls | No  | 
| apiManagementAccounts/apis/connections/connectionAcls | No  | 
| apiManagementAccounts/apis/connectionAcls | No  | 
| apiManagementAccounts/apiAcls | No  | 
| apiManagementAccounts/apis/apiAcls | No  | 
| apiManagementAccounts/apis | No  | 
| apiManagementAccounts/apis/localizedDefinitions | No  | 
| apiManagementAccounts/apis/connections | No  | 
| connections | Yes | 
| customApis | Yes | 
| connectionGateways | Yes | 
| billingMeters | No  | 
| verifyHostingEnvironmentVnet | No  | 

## <a name="xrm"></a>XRM
| Tipo di risorsa | Tag supportati |
| ------------- | ----------- |
| organizations | No  | 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
