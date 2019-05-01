---
title: Eliminazione in modalità completa di Azure Resource Manager in base al tipo di risorsa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 21b3972a96c1601b15c403275474d58873753b08
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712985"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa
Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

I tipi di risorsa contrassegnati con `Yes` vengono eliminati quando il tipo non si trova in un modello distribuito in modalità completa. 

I tipi di risorsa contrassegnati con `No` non vengono automaticamente eliminati quando non si trovano nel modello, ma vengono eliminati se viene eliminata la risorsa padre. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| DomainServices | Sì | 
| DomainServices/oucontainer | No  | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| diagnosticSettings | No  | 
| diagnosticSettingsCategories | No  | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| supportProviders | No  | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| aadsupportcases | No  | 
| addsservices | No  | 
| agents | No  | 
| anonymousapiusers | No  | 
| configurazione | No  | 
| log | No  | 
| reports | No  | 
| services | No  | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| configurazioni | No  | 
| generateRecommendations | No  | 
| raccomandazioni di film | No  | 
| suppressions | No  | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| actionRules | No  | 
| alerts | No  | 
| alertsList | No  | 
| alertsSummary | No  | 
| alertsSummaryList | No  | 
| smartDetectorAlertRules | No  | 
| smartDetectorRuntimeEnvironments | No  | 
| smartGroups | No  | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| servers | Sì | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| reportFeedback | No  | 
| service | Sì | 
| validateServiceName | No  | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| attestationProviders | No  | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| classicAdministrators | No  | 
| denyAssignments | No  | 
| elevateAccess | No  | 
| locks | No  | 
| autorizzazioni | No  | 
| policyAssignments | No  | 
| policyDefinitions | No  | 
| policySetDefinitions | No  | 
| providerOperations | No  | 
| roleAssignments | No  | 
| roleDefinitions | No  | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| automationAccounts | Sì | 
| automationAccounts/configurations | Sì | 
| automationAccounts/jobs | No  | 
| automationAccounts/runbooks | Sì | 
| automationAccounts/softwareUpdateConfigurations | No  | 
| automationAccounts/webhooks | No  | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| environments | No  | 
| environments/accounts | No  | 
| environments/accounts/namespaces | No  | 
| environments/accounts/namespaces/configurations | No  | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| b2cDirectories | Sì | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| registrations | Sì | 
| registrations/customerSubscriptions | No  | 
| registrations/products | No  | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| batchAccounts | Sì | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| billingAccounts | No  | 
| billingAccounts/billingProfiles | No  | 
| billingAccounts/billingProfiles/billingSubscriptions | No  | 
| billingAccounts/billingProfiles/invoices | No  | 
| billingAccounts/billingProfiles/invoices/pricesheet | No  | 
| billingAccounts/billingProfiles/operationStatus | No  | 
| billingAccounts/billingProfiles/paymentMethods | No  | 
| billingAccounts/billingProfiles/policies | No  | 
| billingAccounts/billingProfiles/pricesheet | No  | 
| billingAccounts/billingProfiles/products | No  | 
| billingAccounts/billingProfiles/transactions | No  | 
| billingAccounts/billingSubscriptions | No  | 
| billingAccounts/departments | No  | 
| billingAccounts/eligibleOffers | No  | 
| billingAccounts/enrollmentAccounts | No  | 
| billingAccounts/invoices | No  | 
| billingAccounts/invoiceSections | No  | 
| billingAccounts/invoiceSections/billingSubscriptions | No  | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | No  | 
| billingAccounts/invoiceSections/importRequests | No  | 
| billingAccounts/invoiceSections/initiateImportRequest | No  | 
| billingAccounts/invoiceSections/initiateTransfer | No  | 
| billingAccounts/invoiceSections/operationStatus | No  | 
| billingAccounts/invoiceSections/products | No  | 
| billingAccounts/invoiceSections/transfers | No  | 
| billingAccounts/products | No  | 
| billingAccounts/projects | No  | 
| billingAccounts/projects/billingSubscriptions | No  | 
| billingAccounts/projects/importRequests | No  | 
| billingAccounts/projects/initiateImportRequest | No  | 
| billingAccounts/projects/operationStatus | No  | 
| billingAccounts/projects/products | No  | 
| billingAccounts/transactions | No  | 
| billingPeriods | No  | 
| BillingPermissions | No  | 
| billingProperty | No  | 
| BillingRoleAssignments | No  | 
| BillingRoleDefinitions | No  | 
| CreateBillingRoleAssignment | No  | 
| departments | No  | 
| enrollmentAccounts | No  | 
| importRequests | No  | 
| importRequests/acceptImportRequest | No  | 
| importRequests/declineImportRequest | No  | 
| invoices | No  | 
| transfers | No  | 
| transfers/acceptTransfer | No  | 
| transfers/declineTransfer | No  | 
| transfers/operationStatus | No  | 
| usagePlans | No  | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| mapApis | Sì | 
| updateCommunicationPreference | No  | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| BizTalk | Sì | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| blueprintAssignments | No  | 
| blueprintAssignments/assignmentOperations | No  | 
| blueprintAssignments/operations | No  | 
| blueprints | No  | 
| blueprints/artifacts | No  | 
| blueprints/versions | No  | 
| blueprints/versions/artifacts | No  | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| botServices | Sì | 
| botServices/channels | No  | 
| botServices/connections | No  | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| Redis | Sì | 
| RedisConfigDefinition | No  | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| appliedReservations | No  | 
| calculatePrice | No  | 
| catalogs | No  | 
| commercialReservationOrders | No  | 
| reservationOrders | No  | 
| reservationOrders/calculateRefund | No  | 
| reservationOrders/merge | No  | 
| reservationOrders/reservations | No  | 
| reservationOrders/reservations/revisions | No  | 
| reservationOrders/return | No  | 
| reservationOrders/split | No  | 
| reservationOrders/swap | No  | 
| reservations | No  | 
| resources | No  | 
| validateReservationOrder | No  | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| edgenodes | No  | 
| Profili | Sì | 
| profiles/endpoints | Sì | 
| profiles/endpoints/customdomains | No  | 
| profiles/endpoints/origins | No  | 
| validateProbe | No  | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| certificateOrders | Sì | 
| certificateOrders/certificates | No  | 
| validateCertificateRegistrationInformation | No  | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| capabilities | No  | 
| domainNames | No  | 
| domainNames/capabilities | No  | 
| domainNames/internalLoadBalancers | No  | 
| domainNames/serviceCertificates | No  | 
| domainNames/slots | No  | 
| domainNames/slots/roles | No  | 
| moveSubscriptionResources | No  | 
| operatingSystemFamilies | No  | 
| operatingSystems | No  | 
| quotas | No  | 
| resourceTypes | No  | 
| validateSubscriptionMoveAvailability | No  | 
| virtualMachines | No  | 
| virtualMachines/diagnosticSettings | No  | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| classicInfrastructureResources | No  | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| capabilities | No  | 
| expressRouteCrossConnections | No  | 
| expressRouteCrossConnections/peerings | No  | 
| gatewaySupportedDevices | No  | 
| networkSecurityGroups | No  | 
| quotas | No  | 
| reservedIps | No  | 
| virtualNetworks | No  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | No  | 
| virtualNetworks/virtualNetworkPeerings | No  | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| capabilities | No  | 
| disks | No  | 
| images | No  | 
| osImages | No  | 
| osPlatformImages | No  | 
| publicImages | No  | 
| quotas | No  | 
| storageAccounts | No  | 
| storageAccounts/services | No  | 
| storageAccounts/services/diagnosticSettings | No  | 
| storageAccounts/vmImages | No  | 
| vmImages | No  | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| RateCard | No  | 
| UsageAggregates | No  | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| availabilitySets | Sì | 
| disks | Sì | 
| images | Sì | 
| restorePointCollections | Sì | 
| restorePointCollections/restorePoints | No  | 
| sharedVMImages | Sì | 
| sharedVMImages/versions | Sì | 
| snapshots | Sì | 
| virtualMachines | Sì | 
| virtualMachines/diagnosticSettings | No  | 
| virtualMachines/extensions | Sì | 
| virtualMachineScaleSets | Sì | 
| virtualMachineScaleSets/extensions | No  | 
| virtualMachineScaleSets/networkInterfaces | No  | 
| virtualMachineScaleSets/publicIPAddresses | No  | 
| virtualMachineScaleSets/virtualMachines | No  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | No  | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| AggregatedCost | No  | 
| Saldi | No  | 
| Budget | No  | 
| Charges | No  | 
| CostTags | No  | 
| credits | No  | 
| eventi | No  | 
| Previsioni | No  | 
| lots | No  | 
| Marketplace | No  | 
| Pricesheets | No  | 
| products | No  | 
| ReservationDetails | No  | 
| ReservationRecommendations | No  | 
| ReservationSummaries | No  | 
| ReservationTransactions | No  | 
| Tag | No  | 
| Termini | No  | 
| UsageDetails | No  | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| containerGroups | Sì | 
| serviceAssociationLinks | No  | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| registries | Sì | 
| registries/builds | No  | 
| registries/builds/cancel | No  | 
| registries/builds/getLogLink | No  | 
| registries/buildTasks | Sì | 
| registries/buildTasks/steps | No  | 
| registries/eventGridFilters | No  | 
| registries/getBuildSourceUploadUrl | No  | 
| registries/GetCredentials | No  | 
| registries/importImage | No  | 
| registries/queueBuild | No  | 
| registries/regenerateCredential | No  | 
| registries/regenerateCredentials | No  | 
| registries/replications | Sì | 
| registries/runs | No  | 
| registries/runs/cancel | No  | 
| registries/scheduleRun | No  | 
| registries/tasks | Sì | 
| registries/updatePolicies | No  | 
| registries/webhooks | Sì | 
| registries/webhooks/getCallbackConfig | No  | 
| registries/webhooks/ping | No  | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| containerServices | Sì | 
| managedClusters | Sì | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| scala Web | Sì | 
| updateCommunicationPreference | No  | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| Avvisi | No  | 
| BillingAccounts | No  | 
| Connettori | Sì | 
| Departments | No  | 
| Dimensioni | No  | 
| EnrollmentAccounts | No  | 
| Query | No  | 
| register | No  | 
| Reportconfigs | No  | 
| Report | No  | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| hubs | Sì | 
| hubs/authorizationPolicies | No  | 
| hubs/connectors | No  | 
| hubs/connectors/mappings | No  | 
| hubs/interactions | No  | 
| hubs/kpi | No  | 
| hubs/links | No  | 
| hubs/profiles | No  | 
| hubs/roleAssignments | No  | 
| hubs/roles | No  | 
| hubs/suggestTypeSchema | No  | 
| hubs/views | No  | 
| hubs/widgetTypes | No  | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| jobs | Sì | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| DataBoxEdgeDevices | Sì | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| aree di lavoro | Sì | 
| workspaces/virtualNetworkPeerings | No  | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| catalogs | Sì | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| connectionManagers | Sì | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| dataFactories | Sì | 
| dataFactories/diagnosticSettings | No  | 
| dataFactorySchema | No  | 
| factories | Sì | 
| factories/integrationRuntimes | No  | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 
| accounts/dataLakeStoreAccounts | No  | 
| accounts/storageAccounts | No  | 
| accounts/storageAccounts/containers | No  | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 
| accounts/eventGridFilters | No  | 
| accounts/firewallRules | No  | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| services | Sì | 
| services/projects | Sì | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| servers | Sì | 
| servers/recoverableServers | No  | 
| servers/virtualNetworkRules | No  | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| servers | Sì | 
| servers/recoverableServers | No  | 
| servers/virtualNetworkRules | No  | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| servers | Sì | 
| servers/advisors | No  | 
| servers/queryTexts | No  | 
| servers/recoverableServers | No  | 
| servers/topQueryStatistics | No  | 
| servers/virtualNetworkRules | No  | 
| servers/waitStatistics | No  | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| Hub IoT | Sì | 
| IotHubs/eventGridFilters | No  | 
| ProvisioningServices | Sì | 
| usages | No  | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| controllers | Sì | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| labs | Sì | 
| labs/serviceRunners | Sì | 
| labs/virtualMachines | Sì | 
| schedules | Sì | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| databaseAccountNames | No  | 
| databaseAccounts | Sì | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| domains | Sì | 
| domains/domainOwnershipIdentifiers | No  | 
| generateSsoRequest | No  | 
| topLevelDomains | No  | 
| validateDomainRegistrationInformation | No  | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| lcsprojects | No  | 
| lcsprojects/clouddeployments | No  | 
| lcsprojects/connectors | No  | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| domains | Sì | 
| domains/topics | No  | 
| eventSubscriptions | No  | 
| extensionTopics | No  | 
| topics | Sì | 
| topicTypes | No  | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| clusters | Sì | 
| spazi dei nomi | Sì | 
| namespaces/authorizationrules | No  | 
| namespaces/disasterrecoveryconfigs | No  | 
| namespaces/eventhubs | No  | 
| namespaces/eventhubs/authorizationrules | No  | 
| namespaces/eventhubs/consumergroups | No  | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| funzionalità | No  | 
| provider | No  | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| enroll | No  | 
| galleryitems | No  | 
| generateartifactaccessuri | No  | 
| myareas | No  | 
| myareas/areas | No  | 
| myareas/areas/areas | No  | 
| myareas/areas/areas/galleryitems | No  | 
| myareas/areas/galleryitems | No  | 
| myareas/galleryitems | No  | 
| register | No  | 
| resources | No  | 
| retrieveresourcesbyid | No  | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| guestConfigurationAssignments | No  | 
| software | No  | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| hanaInstances | Sì | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| clusters | Sì | 
| clusters/applications | No  | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| jobs | Sì | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| labelGroups | No  | 
| labelGroups/labels | No  | 
| labelGroups/labels/conditions | No  | 
| labelGroups/labels/subLabels | No  | 
| labelGroups/labels/subLabels/conditions | No  | 

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| actiongroups | Sì | 
| activityLogAlerts | Sì | 
| alertrules | Sì | 
| automatedExportSettings | No  | 
| autoscalesettings | Sì | 
| baseline | No  | 
| calculatebaseline | No  | 
| components | Sì | 
| components/events | No  | 
| components/pricingPlans | No  | 
| components/query | No  | 
| diagnosticSettings | No  | 
| diagnosticSettingsCategories | No  | 
| eventCategories | No  | 
| eventtypes | No  | 
| extendedDiagnosticSettings | No  | 
| logDefinitions | No  | 
| logprofiles | No  | 
| log | No  | 
| metricAlerts | Sì |
| migrateToNewPricingModel | No  | 
| myWorkbooks | No  | 
| query | No  | 
| rollbackToLegacyPricingModel | No  | 
| scheduledqueryrules | Sì | 
| vmInsightsOnboardingStatuses | No  | 
| webtests | Sì | 
| workbooks | Sì | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| diagnosticSettings | No  | 
| diagnosticSettingsCategories | No  | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| IoTApps | Sì | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| Grafico | Sì | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| deletedVaults | No  | 
| insiemi di credenziali | Sì | 
| vaults/accessPolicies | No  | 
| vaults/secrets | No  | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| clusters | Sì | 
| clusters/databases | No  | 
| clusters/databases/dataconnections | No  | 
| clusters/databases/eventhubconnections | No  | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| labaccounts | Sì | 
| users | No  | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| log | No  | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| integrationAccounts | Sì | 
| flussi di lavoro | Sì | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| commitmentPlans | Sì | 
| webServices | Sì | 
| Aree di lavoro | Sì | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 
| accounts/workspaces | Sì | 
| accounts/workspaces/projects | Sì | 
| teamAccounts | Sì | 
| teamAccounts/workspaces | Sì | 
| teamAccounts/workspaces/projects | Sì | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| aree di lavoro | Sì | 
| workspaces/computes | No  | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| Identities | No  | 
| userAssignedIdentities | Sì | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| getEntities | No  | 
| managementGroups | No  | 
| resources | No  | 
| startTenantBackfill | No  | 
| tenantBackfillStatus | No  | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 
| accounts/eventGridFilters | No  | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| offers | No  | 
| offerTypes | No  | 
| offerTypes/publishers | No  | 
| offerTypes/publishers/offers | No  | 
| offerTypes/publishers/offers/plans | No  | 
| offerTypes/publishers/offers/plans/agreements | No  | 
| offerTypes/publishers/offers/plans/configs | No  | 
| offerTypes/publishers/offers/plans/configs/importImage | No  | 
| privategalleryitems | No  | 
| products | No  | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| classicDevServices | Sì | 
| updateCommunicationPreference | No  | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| agreements | No  | 
| offertypes | No  | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| mediaservices | Sì | 
| mediaservices/accountFilters | No  | 
| mediaservices/assets | No  | 
| mediaservices/assets/assetFilters | No  | 
| mediaservices/contentKeyPolicies | No  | 
| mediaservices/eventGridFilters | No  | 
| mediaservices/liveEventOperations | No  | 
| mediaservices/liveEvents | Sì | 
| mediaservices/liveEvents/liveOutputs | No  | 
| mediaservices/liveOutputOperations | No  | 
| mediaservices/streamingEndpointOperations | No  | 
| mediaservices/streamingEndpoints | Sì | 
| mediaservices/streamingLocators | No  | 
| mediaservices/streamingPolicies | No  | 
| mediaservices/transforms | No  | 
| mediaservices/transforms/jobs | No  | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| projects | Sì | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| applicationGateways | Sì | 
| applicationSecurityGroups | Sì | 
| azureFirewallFqdnTags | No  | 
| azureFirewalls | Sì | 
| bgpServiceCommunities | No  | 
| connections | Sì | 
| ddosCustomPolicies | Sì | 
| ddosProtectionPlans | Sì | 
| dnsOperationStatuses | No  | 
| dnszones | Sì | 
| dnszones/A | No  | 
| dnszones/AAAA | No  | 
| dnszones/all | No  | 
| dnszones/CAA | No  | 
| dnszones/CNAME | No  | 
| dnszones/MX | No  | 
| dnszones/NS | No  | 
| dnszones/PTR | No  | 
| dnszones/recordsets | No  | 
| dnszones/SOA | No  | 
| dnszones/SRV | No  | 
| dnszones/TXT | No  | 
| expressRouteCircuits | Sì | 
| expressRouteServiceProviders | No  | 
| frontdoors | Sì | 
| frontdoorWebApplicationFirewallPolicies | Sì | 
| getDnsResourceReference | No  | 
| interfaceEndpoints | Sì | 
| internalNotify | No  | 
| loadBalancers | Sì | 
| localNetworkGateways | Sì | 
| natGateways | Sì | 
| networkIntentPolicies | Sì | 
| networkInterfaces | Sì | 
| networkProfiles | Sì | 
| networkSecurityGroups | Sì | 
| networkWatchers | Sì | 
| networkWatchers/connectionMonitors | Sì | 
| networkWatchers/lenses | Sì | 
| networkWatchers/pingMeshes | Sì | 
| privateLinkServices | Sì | 
| publicIPAddresses | Sì | 
| publicIPPrefixes | Sì | 
| routeFilters | Sì | 
| routeTables | Sì | 
| serviceEndpointPolicies | Sì | 
| trafficManagerGeographicHierarchies | No  | 
| trafficmanagerprofiles | Sì | 
| trafficmanagerprofiles/heatMaps | No  | 
| virtualHubs | Sì | 
| virtualNetworkGateways | Sì | 
| virtualNetworks | Sì | 
| virtualNetworkTaps | Sì | 
| virtualWans | Sì | 
| vpnGateways | Sì | 
| vpnSites | Sì | 
| webApplicationFirewallPolicies | Sì | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| spazi dei nomi | Sì | 
| namespaces/notificationHubs | Sì | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| devices | No  | 
| linkTargets | No  | 
| storageInsightConfigs | No  | 
| aree di lavoro | Sì | 
| workspaces/dataSources | No  | 
| workspaces/linkedServices | No  | 
| workspaces/query | No  | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| managementassociations | No  | 
| managementconfigurations | Sì | 
| solutions | Sì | 
| Viste | Sì | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| policyEvents | No  | 
| policyStates | No  | 
| policyTrackedResources | No  | 
| remediations | No  | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| consoles | No  | 
| dashboards | Sì | 
| userSettings | No  | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| workspaceCollections | Sì | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| capacities | Sì | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| backupProtectedItems | No  | 
| insiemi di credenziali | Sì | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| spazi dei nomi | Sì | 
| namespaces/authorizationrules | No  | 
| namespaces/hybridconnections | No  | 
| namespaces/hybridconnections/authorizationrules | No  | 
| namespaces/wcfrelays | No  | 
| namespaces/wcfrelays/authorizationrules | No  | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| resources | No  | 
| subscriptionsStatus | No  | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| availabilityStatuses | No  | 
| childAvailabilityStatuses | No  | 
| childResources | No  | 
| eventi | No  | 
| impactedResources | No  | 
| Notifiche | No  | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| deployments | No  | 
| deployments/operations | No  | 
| links | No  | 
| notifyResourceJobs | No  | 
| provider | No  | 
| resourceGroups | No  | 
| resources | No  | 
| subscriptions | No  | 
| subscriptions/providers | No  | 
| subscriptions/resourceGroups | No  | 
| subscriptions/resourcegroups/resources | No  | 
| subscriptions/resources | No  | 
| subscriptions/tagnames | No  | 
| subscriptions/tagNames/tagValues | No  | 
| tenants | No  | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| scala Web | Sì | 
| saasresources | No  | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| flows | Sì | 
| jobcollections | Sì | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| resourceHealthMetadata | No  | 
| searchServices | Sì | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| advancedThreatProtectionSettings | No  | 
| alerts | No  | 
| allowedConnections | No  | 
| appliances | No  | 
| applicationWhitelistings | No  | 
| AutoProvisioningSettings | No  | 
| Compliances | No  | 
| dataCollectionAgents | No  | 
| discoveredSecuritySolutions | No  | 
| externalSecuritySolutions | No  | 
| InformationProtectionPolicies | No  | 
| jitNetworkAccessPolicies | No  | 
| monitoring | No  | 
| monitoring/antimalware | No  | 
| monitoring/baseline | No  | 
| monitoring/patch | No  | 
| criteri | No  | 
| pricings | No  | 
| securityContacts | No  | 
| securitySolutions | No  | 
| securitySolutionsReferenceData | No  | 
| securityStatus | No  | 
| securityStatus/endpoints | No  | 
| securityStatus/subnets | No  | 
| securityStatus/virtualMachines | No  | 
| securityStatuses | No  | 
| securityStatusesSummaries | No  | 
| Scheda Impostazioni | No  | 
| attività | No  | 
| topologies | No  | 
| workspaceSettings | No  | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| diagnosticSettings | No  | 
| diagnosticSettingsCategories | No  | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| spazi dei nomi | Sì | 
| namespaces/authorizationrules | No  | 
| namespaces/disasterrecoveryconfigs | No  | 
| namespaces/eventgridfilters | No  | 
| namespaces/queues | No  | 
| namespaces/queues/authorizationrules | No  | 
| namespaces/topics | No  | 
| namespaces/topics/authorizationrules | No  | 
| namespaces/topics/subscriptions | No  | 
| namespaces/topics/subscriptions/rules | No  | 
| premiumMessagingRegions | No  | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| clusters | Sì | 
| clusters/applications | No  | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| scala Web | Sì | 
| gateways | Sì | 
| networks | Sì | 
| chiavi private | Sì | 
| volumes | Sì | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| SignalR | Sì | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| applianceDefinitions | Sì | 
| appliances | Sì | 
| applicationDefinitions | Sì | 
| scala Web | Sì | 
| jitRequests | Sì | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| managedInstances | Sì |
| managedInstances/databases | Sì (vedere la nota seguente) |
| managedInstances/databases/backupShortTermRetentionPolicies | No  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | No  |
| managedInstances/databases/vulnerabilityAssessments | No  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | No  |
| managedInstances/encryptionProtector | No  |
| managedInstances/keys | No  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No  |
| managedInstances/vulnerabilityAssessments | No  |
| servers | Sì | 
| servers/administrators | No  | 
| servers/communicationLinks | No  | 
| servers/databases | Sì (vedere la nota seguente) | 
| servers/encryptionProtector | No  | 
| servers/firewallRules | No  | 
| servers/keys | No  | 
| servers/restorableDroppedDatabases | No  | 
| servers/serviceobjectives | No  | 
| servers/tdeCertificates | No  | 

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database del data warehouse, supportano i tag.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Sì | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | No  | 
| SqlVirtualMachines | Sì | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| storageAccounts | Sì | 
| storageAccounts/blobServices | No  | 
| storageAccounts/fileServices | No  | 
| storageAccounts/queueServices | No  | 
| storageAccounts/services | No  | 
| storageAccounts/tableServices | No  | 
| usages | No  | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| storageSyncServices | Sì | 
| storageSyncServices/registeredServers | No  | 
| storageSyncServices/syncGroups | No  | 
| storageSyncServices/syncGroups/cloudEndpoints | No  | 
| storageSyncServices/syncGroups/serverEndpoints | No  | 
| storageSyncServices/workflows | No  | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| managers | Sì | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| streamingjobs | Sì (vedere la nota seguente) | 
| streamingjobs/diagnosticSettings | No  | 

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| CreateSubscription | No  | 
| SubscriptionDefinitions | No  | 
| SubscriptionOperations | No  | 

## <a name="microsoftsupport"></a>microsoft.support
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| supporttickets | No  | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| providerRegistrations | Sì | 
| resources | Sì | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| environments | Sì | 
| environments/accessPolicies | No  | 
| environments/eventsources | Sì | 
| environments/referenceDataSets | Sì | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| account | Sì | 
| account/extension | Sì | 
| account/project | Sì | 

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Eliminazione in modalità completa |
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
| certificates | Sì | 
| connectionGateways | Sì | 
| connections | Sì | 
| customApis | Sì | 
| deletedSites | No  | 
| functions | No  | 
| hostingEnvironments | Sì | 
| hostingEnvironments/multiRolePools | No  | 
| hostingEnvironments/multiRolePools/instances | No  | 
| hostingEnvironments/workerPools | No  | 
| hostingEnvironments/workerPools/instances | No  | 
| publishingUsers | No  | 
| raccomandazioni di film | No  | 
| resourceHealthMetadata | No  | 
| runtimes | No  | 
| serverFarms | Sì | 
| serverFarms/workers | No  | 
| siti | Sì | 
| sites/domainOwnershipIdentifiers | No  | 
| sites/hostNameBindings | No  | 
| sites/instances | No  | 
| sites/instances/extensions | No  | 
| sites/premieraddons | Sì | 
| sites/recommendations | No  | 
| sites/resourceHealthMetadata | No  | 
| sites/slots | Sì | 
| sites/slots/hostNameBindings | No  | 
| sites/slots/instances | No  | 
| sites/slots/instances/extensions | No  | 
| sourceControls | No  | 
| validate | No  | 
| verifyHostingEnvironmentVnet | No  | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| diagnosticSettings | No  | 
| diagnosticSettingsCategories | No  | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| DeviceServices | Sì | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo di risorsa | Eliminazione in modalità completa |
| ------------- | ----------- |
| components | No  | 
| componentsSummary | No  | 
| monitorInstances | No  | 
| monitorInstancesSummary | No  | 
| monitors | No  | 
| notificationSettings | No  | 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
