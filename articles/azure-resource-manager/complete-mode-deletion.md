---
title: Eliminazione in modalità completa di Azure Resource Manager in base al tipo di risorsa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302852"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa
Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

I tipi di risorsa contrassegnati con `Yes` vengono eliminati quando il tipo non si trova in un modello distribuito in modalità completa. 

I tipi di risorsa contrassegnati con `No` non vengono automaticamente eliminati quando non si trovano nel modello, ma vengono eliminati se viene eliminata la risorsa padre. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DomainServices | Yes | 
> | DomainServices/oucontainer | No | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No | 
> | diagnosticSettingsCategories | No | 

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | supportProviders | No | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aadsupportcases | No | 
> | addsservices | No | 
> | agents | No | 
> | anonymousapiusers | No | 
> | configurazione | No | 
> | logs | No | 
> | reports | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurazioni | No | 
> | generateRecommendations | No | 
> | raccomandazioni di film | No | 
> | suppressions | No | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | actionRules | No | 
> | alerts | No | 
> | alertsList | No | 
> | alertsSummary | No | 
> | alertsSummaryList | No | 
> | smartDetectorAlertRules | No | 
> | smartDetectorRuntimeEnvironments | No | 
> | smartGroups | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | reportFeedback | No | 
> | servizio | Sì | 
> | validateServiceName | No | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | attestationProviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicAdministrators | No | 
> | denyAssignments | No | 
> | elevateAccess | No | 
> | locks | No | 
> | autorizzazioni | No | 
> | policyAssignments | No | 
> | policyDefinitions | No | 
> | policySetDefinitions | No | 
> | providerOperations | No | 
> | roleAssignments | No | 
> | roleDefinitions | No | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | automationAccounts | Sì | 
> | automationAccounts/configurations | Yes | 
> | automationAccounts/jobs | No | 
> | automationAccounts/runbooks | Sì | 
> | automationAccounts/softwareUpdateConfigurations | No | 
> | automationAccounts/webhooks | No | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | No | 
> | environments/accounts | No | 
> | environments/accounts/namespaces | No | 
> | environments/accounts/namespaces/configurations | No | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | b2cDirectories | Sì | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registrations | Sì | 
> | registrations/customerSubscriptions | No | 
> | registrations/products | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | batchAccounts | Yes | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | billingAccounts | No | 
> | billingAccounts/billingProfiles | No | 
> | billingAccounts/billingProfiles/billingSubscriptions | No | 
> | billingAccounts/billingProfiles/invoices | No | 
> | billingAccounts/billingProfiles/invoices/pricesheet | No | 
> | billingAccounts/billingProfiles/operationStatus | No | 
> | billingAccounts/billingProfiles/paymentMethods | No | 
> | billingAccounts/billingProfiles/policies | No | 
> | billingAccounts/billingProfiles/pricesheet | No | 
> | billingAccounts/billingProfiles/products | No | 
> | billingAccounts/billingProfiles/transactions | No | 
> | billingAccounts/billingSubscriptions | No | 
> | billingAccounts/departments | No | 
> | billingAccounts/eligibleOffers | No | 
> | billingAccounts/enrollmentAccounts | No | 
> | billingAccounts/invoices | No | 
> | billingAccounts/invoiceSections | No | 
> | billingAccounts/invoiceSections/billingSubscriptions | No | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No | 
> | billingAccounts/invoiceSections/importRequests | No | 
> | billingAccounts/invoiceSections/initiateImportRequest | No | 
> | billingAccounts/invoiceSections/initiateTransfer | No | 
> | billingAccounts/invoiceSections/operationStatus | No | 
> | billingAccounts/invoiceSections/products | No | 
> | billingAccounts/invoiceSections/transfers | No | 
> | billingAccounts/products | No | 
> | billingAccounts/projects | No | 
> | billingAccounts/projects/billingSubscriptions | No | 
> | billingAccounts/projects/importRequests | No | 
> | billingAccounts/projects/initiateImportRequest | No | 
> | billingAccounts/projects/operationStatus | No | 
> | billingAccounts/projects/products | No | 
> | billingAccounts/transactions | No | 
> | billingPeriods | No | 
> | BillingPermissions | No | 
> | billingProperty | No | 
> | BillingRoleAssignments | No | 
> | BillingRoleDefinitions | No | 
> | CreateBillingRoleAssignment | No | 
> | departments | No | 
> | enrollmentAccounts | No | 
> | importRequests | No | 
> | importRequests/acceptImportRequest | No | 
> | importRequests/declineImportRequest | No | 
> | invoices | No | 
> | transfers | No | 
> | transfers/acceptTransfer | No | 
> | transfers/declineTransfer | No | 
> | transfers/operationStatus | No | 
> | usagePlans | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mapApis | Sì | 
> | updateCommunicationPreference | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | BizTalk | Yes | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blueprintAssignments | No | 
> | blueprintAssignments/assignmentOperations | No | 
> | blueprintAssignments/operations | No | 
> | blueprints | No | 
> | blueprints/artifacts | No | 
> | blueprints/versions | No | 
> | blueprints/versions/artifacts | No | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | botServices | Sì | 
> | botServices/channels | No | 
> | botServices/connections | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Redis | Yes | 
> | RedisConfigDefinition | No | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appliedReservations | No | 
> | calculatePrice | No | 
> | catalogs | No | 
> | commercialReservationOrders | No | 
> | reservationOrders | No | 
> | reservationOrders/calculateRefund | No | 
> | reservationOrders/merge | No | 
> | reservationOrders/reservations | No | 
> | reservationOrders/reservations/revisions | No | 
> | reservationOrders/return | No | 
> | reservationOrders/split | No | 
> | reservationOrders/swap | No | 
> | reservations | No | 
> | resources | No | 
> | validateReservationOrder | No | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | edgenodes | No | 
> | profiles | Sì | 
> | profiles/endpoints | Sì | 
> | profiles/endpoints/customdomains | No | 
> | profiles/endpoints/origins | No | 
> | validateProbe | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | certificateOrders | Sì | 
> | certificateOrders/certificates | No | 
> | validateCertificateRegistrationInformation | No | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No | 
> | domainNames | No | 
> | domainNames/capabilities | No | 
> | domainNames/internalLoadBalancers | No | 
> | domainNames/serviceCertificates | No | 
> | domainNames/slots | No | 
> | domainNames/slots/roles | No | 
> | moveSubscriptionResources | No | 
> | operatingSystemFamilies | No | 
> | operatingSystems | No | 
> | quotas | No | 
> | resourceTypes | No | 
> | validateSubscriptionMoveAvailability | No | 
> | virtualMachines | No | 
> | virtualMachines/diagnosticSettings | No | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicInfrastructureResources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No | 
> | expressRouteCrossConnections | No | 
> | expressRouteCrossConnections/peerings | No | 
> | gatewaySupportedDevices | No | 
> | networkSecurityGroups | No | 
> | quotas | No | 
> | reservedIps | No | 
> | virtualNetworks | No | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No | 
> | virtualNetworks/virtualNetworkPeerings | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No | 
> | disks | No | 
> | images | No | 
> | osImages | No | 
> | osPlatformImages | No | 
> | publicImages | No | 
> | quotas | No | 
> | storageAccounts | No | 
> | storageAccounts/services | No | 
> | storageAccounts/services/diagnosticSettings | No | 
> | storageAccounts/vmImages | No | 
> | vmImages | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Yes | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | RateCard | No | 
> | UsageAggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | availabilitySets | Yes | 
> | disks | Yes | 
> | images | Yes | 
> | restorePointCollections | Yes | 
> | restorePointCollections/restorePoints | No | 
> | sharedVMImages | Sì | 
> | sharedVMImages/versions | Sì | 
> | snapshots | Yes | 
> | virtualMachines | Sì | 
> | virtualMachines/diagnosticSettings | No | 
> | virtualMachines/extensions | Sì | 
> | virtualMachineScaleSets | Sì | 
> | virtualMachineScaleSets/extensions | No | 
> | virtualMachineScaleSets/networkInterfaces | No | 
> | virtualMachineScaleSets/publicIPAddresses | No | 
> | virtualMachineScaleSets/virtualMachines | No | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | AggregatedCost | No | 
> | Saldi | No | 
> | Budget | No | 
> | Charges | No | 
> | CostTags | No | 
> | credits | No | 
> | eventi | No | 
> | Previsioni | No | 
> | lots | No | 
> | Marketplace | No | 
> | Pricesheets | No | 
> | products | No | 
> | ReservationDetails | No | 
> | ReservationRecommendations | No | 
> | ReservationSummaries | No | 
> | ReservationTransactions | No | 
> | Tag | No | 
> | Termini | No | 
> | UsageDetails | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerGroups | Sì | 
> | serviceAssociationLinks | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registries | Yes | 
> | registries/builds | No | 
> | registries/builds/cancel | No | 
> | registries/builds/getLogLink | No | 
> | registries/buildTasks | Sì | 
> | registries/buildTasks/steps | No | 
> | registries/eventGridFilters | No | 
> | registries/getBuildSourceUploadUrl | No | 
> | registries/GetCredentials | No | 
> | registries/importImage | No | 
> | registries/queueBuild | No | 
> | registries/regenerateCredential | No | 
> | registries/regenerateCredentials | No | 
> | registries/replications | Sì | 
> | registries/runs | No | 
> | registries/runs/cancel | No | 
> | registries/scheduleRun | No | 
> | registries/tasks | Sì | 
> | registries/updatePolicies | No | 
> | registries/webhooks | Sì | 
> | registries/webhooks/getCallbackConfig | No | 
> | registries/webhooks/ping | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerServices | Sì | 
> | managedClusters | Sì | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì | 
> | updateCommunicationPreference | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Avvisi | No | 
> | BillingAccounts | No | 
> | Connettori | Sì | 
> | Departments | No | 
> | Dimensioni | No | 
> | EnrollmentAccounts | No | 
> | Query | No | 
> | register | No | 
> | Reportconfigs | No | 
> | Report | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hubs | Sì | 
> | hubs/authorizationPolicies | No | 
> | hubs/connectors | No | 
> | hubs/connectors/mappings | No | 
> | hubs/interactions | No | 
> | hubs/kpi | No | 
> | hubs/links | No | 
> | hubs/profiles | No | 
> | hubs/roleAssignments | No | 
> | hubs/roles | No | 
> | hubs/suggestTypeSchema | No | 
> | hubs/views | No | 
> | hubs/widgetTypes | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Yes | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sì | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | Yes | 
> | workspaces/virtualNetworkPeerings | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | catalogs | Sì | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | connectionManagers | Sì | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataFactories | Sì | 
> | dataFactories/diagnosticSettings | No | 
> | dataFactorySchema | No | 
> | factories | Yes | 
> | factories/integrationRuntimes | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Yes | 
> | accounts/dataLakeStoreAccounts | No | 
> | accounts/storageAccounts | No | 
> | accounts/storageAccounts/containers | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 
> | accounts/eventGridFilters | No | 
> | accounts/firewallRules | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì | 
> | services/projects | Yes | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì | 
> | servers/recoverableServers | No | 
> | servers/virtualNetworkRules | No | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì | 
> | servers/recoverableServers | No | 
> | servers/virtualNetworkRules | No | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Yes | 
> | servers/advisors | No | 
> | servers/queryTexts | No | 
> | servers/recoverableServers | No | 
> | servers/topQueryStatistics | No | 
> | servers/virtualNetworkRules | No | 
> | servers/waitStatistics | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Hub IoT | Sì | 
> | IotHubs/eventGridFilters | No | 
> | ProvisioningServices | Sì | 
> | usages | No | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | controllers | Yes | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labs | Sì | 
> | labs/serviceRunners | Sì | 
> | labs/virtualMachines | Yes | 
> | schedules | Sì | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | databaseAccountNames | No | 
> | databaseAccounts | Yes | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | Sì | 
> | domains/domainOwnershipIdentifiers | No | 
> | generateSsoRequest | No | 
> | topLevelDomains | No | 
> | validateDomainRegistrationInformation | No | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | lcsprojects | No | 
> | lcsprojects/clouddeployments | No | 
> | lcsprojects/connectors | No | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | Yes | 
> | domains/topics | No | 
> | eventSubscriptions | No | 
> | extensionTopics | No | 
> | topics | Sì | 
> | topicTypes | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Yes | 
> | spazi dei nomi | Sì | 
> | namespaces/authorizationrules | No | 
> | namespaces/disasterrecoveryconfigs | No | 
> | namespaces/eventhubs | No | 
> | namespaces/eventhubs/authorizationrules | No | 
> | namespaces/eventhubs/consumergroups | No | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | funzionalità | No | 
> | provider | No | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | enroll | No | 
> | galleryitems | No | 
> | generateartifactaccessuri | No | 
> | myareas | No | 
> | myareas/areas | No | 
> | myareas/areas/areas | No | 
> | myareas/areas/areas/galleryitems | No | 
> | myareas/areas/galleryitems | No | 
> | myareas/galleryitems | No | 
> | register | No | 
> | resources | No | 
> | retrieveresourcesbyid | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | guestConfigurationAssignments | No | 
> | software | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hanaInstances | Sì | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì | 
> | clusters/applications | No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Sì | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labelGroups | No | 
> | labelGroups/labels | No | 
> | labelGroups/labels/conditions | No | 
> | labelGroups/labels/subLabels | No | 
> | labelGroups/labels/subLabels/conditions | No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | actiongroups | Sì | 
> | activityLogAlerts | Yes | 
> | alertrules | Sì | 
> | automatedExportSettings | No | 
> | autoscalesettings | Sì | 
> | baseline | No | 
> | calculatebaseline | No | 
> | components | Sì | 
> | components/events | No | 
> | components/pricingPlans | No | 
> | components/query | No | 
> | diagnosticSettings | No | 
> | diagnosticSettingsCategories | No | 
> | eventCategories | No | 
> | eventtypes | No | 
> | extendedDiagnosticSettings | No | 
> | logDefinitions | No | 
> | logprofiles | No | 
> | logs | No | 
> | metricAlerts | Sì |
> | migrateToNewPricingModel | No | 
> | myWorkbooks | No | 
> | query | No | 
> | rollbackToLegacyPricingModel | No | 
> | scheduledqueryrules | Sì | 
> | vmInsightsOnboardingStatuses | No | 
> | webtests | Sì | 
> | workbooks | Sì | 

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No | 
> | diagnosticSettingsCategories | No | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | IoTApps | Sì | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Grafico | Sì | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deletedVaults | No | 
> | insiemi di credenziali | Sì | 
> | vaults/accessPolicies | No | 
> | vaults/secrets | No | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì | 
> | clusters/databases | No | 
> | clusters/databases/dataconnections | No | 
> | clusters/databases/eventhubconnections | No | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labaccounts | Sì | 
> | users | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | logs | No | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | integrationAccounts | Sì | 
> | flussi di lavoro | Sì | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | commitmentPlans | Sì | 
> | webServices | Yes | 
> | Aree di lavoro | Sì | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 
> | accounts/workspaces | Yes | 
> | accounts/workspaces/projects | Sì | 
> | teamAccounts | Yes | 
> | teamAccounts/workspaces | Sì | 
> | teamAccounts/workspaces/projects | Sì | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | Sì | 
> | workspaces/computes | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Identities | No | 
> | userAssignedIdentities | Sì | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | getEntities | No | 
> | managementGroups | No | 
> | resources | No | 
> | startTenantBackfill | No | 
> | tenantBackfillStatus | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 
> | accounts/eventGridFilters | No | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | offers | No | 
> | offerTypes | No | 
> | offerTypes/publishers | No | 
> | offerTypes/publishers/offers | No | 
> | offerTypes/publishers/offers/plans | No | 
> | offerTypes/publishers/offers/plans/agreements | No | 
> | offerTypes/publishers/offers/plans/configs | No | 
> | offerTypes/publishers/offers/plans/configs/importImage | No | 
> | privategalleryitems | No | 
> | products | No | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicDevServices | Sì | 
> | updateCommunicationPreference | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | agreements | No | 
> | offertypes | No | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mediaservices | Sì | 
> | mediaservices/accountFilters | No | 
> | mediaservices/assets | No | 
> | mediaservices/assets/assetFilters | No | 
> | mediaservices/contentKeyPolicies | No | 
> | mediaservices/eventGridFilters | No | 
> | mediaservices/liveEventOperations | No | 
> | mediaservices/liveEvents | Yes | 
> | mediaservices/liveEvents/liveOutputs | No | 
> | mediaservices/liveOutputOperations | No | 
> | mediaservices/streamingEndpointOperations | No | 
> | mediaservices/streamingEndpoints | Sì | 
> | mediaservices/streamingLocators | No | 
> | mediaservices/streamingPolicies | No | 
> | mediaservices/transforms | No | 
> | mediaservices/transforms/jobs | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | projects | Yes | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationGateways | Sì | 
> | applicationSecurityGroups | Sì | 
> | azureFirewallFqdnTags | No | 
> | azureFirewalls | Sì | 
> | bgpServiceCommunities | No | 
> | connections | Sì | 
> | ddosCustomPolicies | Sì | 
> | ddosProtectionPlans | Yes | 
> | dnsOperationStatuses | No | 
> | dnszones | Sì | 
> | dnszones/A | No | 
> | dnszones/AAAA | No | 
> | dnszones/all | No | 
> | dnszones/CAA | No | 
> | dnszones/CNAME | No | 
> | dnszones/MX | No | 
> | dnszones/NS | No | 
> | dnszones/PTR | No | 
> | dnszones/recordsets | No | 
> | dnszones/SOA | No | 
> | dnszones/SRV | No | 
> | dnszones/TXT | No | 
> | expressRouteCircuits | Sì | 
> | expressRouteServiceProviders | No | 
> | frontdoors | Yes | 
> | frontdoorWebApplicationFirewallPolicies | Sì | 
> | getDnsResourceReference | No | 
> | interfaceEndpoints | Yes | 
> | internalNotify | No | 
> | loadBalancers | Sì | 
> | localNetworkGateways | Yes | 
> | natGateways | Yes | 
> | networkIntentPolicies | Yes | 
> | networkInterfaces | Yes | 
> | networkProfiles | Yes | 
> | networkSecurityGroups | Yes | 
> | networkWatchers | Sì | 
> | networkWatchers/connectionMonitors | Sì | 
> | networkWatchers/lenses | Yes | 
> | networkWatchers/pingMeshes | Yes | 
> | privateLinkServices | Yes | 
> | publicIPAddresses | Sì | 
> | publicIPPrefixes | Sì | 
> | routeFilters | Sì | 
> | routeTables | Sì | 
> | serviceEndpointPolicies | Yes | 
> | trafficManagerGeographicHierarchies | No | 
> | trafficmanagerprofiles | Yes | 
> | trafficmanagerprofiles/heatMaps | No | 
> | virtualHubs | Sì | 
> | virtualNetworkGateways | Sì | 
> | virtualNetworks | Sì | 
> | virtualNetworkTaps | Yes | 
> | virtualWans | Sì | 
> | vpnGateways | Sì | 
> | vpnSites | Sì | 
> | webApplicationFirewallPolicies | Sì | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì | 
> | namespaces/notificationHubs | Sì | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | devices | No | 
> | linkTargets | No | 
> | storageInsightConfigs | No | 
> | aree di lavoro | Sì | 
> | workspaces/dataSources | No | 
> | workspaces/linkedServices | No | 
> | workspaces/query | No | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managementassociations | No | 
> | managementconfigurations | Yes | 
> | solutions | Sì | 
> | Viste | Yes | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | policyEvents | No | 
> | policyStates | No | 
> | policyTrackedResources | No | 
> | remediations | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | consoles | No | 
> | dashboards | Sì | 
> | userSettings | No | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | workspaceCollections | Sì | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capacities | Sì | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | backupProtectedItems | No | 
> | insiemi di credenziali | Yes | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Yes | 
> | namespaces/authorizationrules | No | 
> | namespaces/hybridconnections | No | 
> | namespaces/hybridconnections/authorizationrules | No | 
> | namespaces/wcfrelays | No | 
> | namespaces/wcfrelays/authorizationrules | No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | resources | No | 
> | subscriptionsStatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | availabilityStatuses | No | 
> | childAvailabilityStatuses | No | 
> | childResources | No | 
> | eventi | No | 
> | impactedResources | No | 
> | Notifiche | No | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deployments | No | 
> | deployments/operations | No | 
> | links | No | 
> | notifyResourceJobs | No | 
> | provider | No | 
> | resourceGroups | No | 
> | resources | No | 
> | subscriptions | No | 
> | subscriptions/providers | No | 
> | subscriptions/resourceGroups | No | 
> | subscriptions/resourcegroups/resources | No | 
> | subscriptions/resources | No | 
> | subscriptions/tagnames | No | 
> | subscriptions/tagNames/tagValues | No | 
> | tenants | No | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì | 
> | saasresources | No | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | flows | Sì | 
> | jobcollections | Yes | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | resourceHealthMetadata | No | 
> | searchServices | Yes | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | No | 
> | alerts | No | 
> | allowedConnections | No | 
> | appliances | No | 
> | applicationWhitelistings | No | 
> | AutoProvisioningSettings | No | 
> | Compliances | No | 
> | dataCollectionAgents | No | 
> | discoveredSecuritySolutions | No | 
> | externalSecuritySolutions | No | 
> | InformationProtectionPolicies | No | 
> | jitNetworkAccessPolicies | No | 
> | monitoring | No | 
> | monitoring/antimalware | No | 
> | monitoring/baseline | No | 
> | monitoring/patch | No | 
> | criteri | No | 
> | pricings | No | 
> | securityContacts | No | 
> | securitySolutions | No | 
> | securitySolutionsReferenceData | No | 
> | securityStatus | No | 
> | securityStatus/endpoints | No | 
> | securityStatus/subnets | No | 
> | securityStatus/virtualMachines | No | 
> | securityStatuses | No | 
> | securityStatusesSummaries | No | 
> | Scheda Impostazioni | No | 
> | attività | No | 
> | topologies | No | 
> | workspaceSettings | No | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No | 
> | diagnosticSettingsCategories | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Yes | 
> | namespaces/authorizationrules | No | 
> | namespaces/disasterrecoveryconfigs | No | 
> | namespaces/eventgridfilters | No | 
> | namespaces/queues | No | 
> | namespaces/queues/authorizationrules | No | 
> | namespaces/topics | No | 
> | namespaces/topics/authorizationrules | No | 
> | namespaces/topics/subscriptions | No | 
> | namespaces/topics/subscriptions/rules | No | 
> | premiumMessagingRegions | No | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Yes | 
> | clusters/applications | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Yes | 
> | gateways | Sì | 
> | networks | Sì | 
> | chiavi private | Sì | 
> | volumes | Yes | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SignalR | Sì | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applianceDefinitions | Sì | 
> | appliances | Sì | 
> | applicationDefinitions | Yes | 
> | scala Web | Yes | 
> | jitRequests | Sì | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/databases | Yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | servers | Yes | 
> | servers/administrators | No | 
> | servers/communicationLinks | No | 
> | servers/databases | Sì | 
> | servers/encryptionProtector | No | 
> | servers/firewallRules | No | 
> | servers/keys | No | 
> | servers/restorableDroppedDatabases | No | 
> | servers/serviceobjectives | No | 
> | servers/tdeCertificates | No | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sì | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No | 
> | SqlVirtualMachines | Sì | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageAccounts | Sì | 
> | storageAccounts/blobServices | No | 
> | storageAccounts/fileServices | No | 
> | storageAccounts/queueServices | No | 
> | storageAccounts/services | No | 
> | storageAccounts/tableServices | No | 
> | usages | No | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì | 
> | storageSyncServices/registeredServers | No | 
> | storageSyncServices/syncGroups | No | 
> | storageSyncServices/syncGroups/cloudEndpoints | No | 
> | storageSyncServices/syncGroups/serverEndpoints | No | 
> | storageSyncServices/workflows | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managers | Yes | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | streamingjobs | Yes | 
> | streamingjobs/diagnosticSettings | No | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | CreateSubscription | No | 
> | SubscriptionDefinitions | No | 
> | SubscriptionOperations | No | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | supporttickets | No | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | Sì | 
> | resources | Yes | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | Yes | 
> | environments/accessPolicies | No | 
> | environments/eventsources | Yes | 
> | environments/referenceDataSets | Yes | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì | 
> | account/extension | Sì | 
> | account/project | Sì | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | apiManagementAccounts | No | 
> | apiManagementAccounts/apiAcls | No | 
> | apiManagementAccounts/apis | No | 
> | apiManagementAccounts/apis/apiAcls | No | 
> | apiManagementAccounts/apis/connectionAcls | No | 
> | apiManagementAccounts/apis/connections | No | 
> | apiManagementAccounts/apis/connections/connectionAcls | No | 
> | apiManagementAccounts/apis/localizedDefinitions | No | 
> | apiManagementAccounts/connectionAcls | No | 
> | apiManagementAccounts/connections | No | 
> | billingMeters | No | 
> | certificates | Sì | 
> | connectionGateways | Yes | 
> | connections | Sì | 
> | customApis | Sì | 
> | deletedSites | No | 
> | functions | No | 
> | hostingEnvironments | Sì | 
> | hostingEnvironments/multiRolePools | No | 
> | hostingEnvironments/multiRolePools/instances | No | 
> | hostingEnvironments/workerPools | No | 
> | hostingEnvironments/workerPools/instances | No | 
> | publishingUsers | No | 
> | raccomandazioni di film | No | 
> | resourceHealthMetadata | No | 
> | runtimes | No | 
> | serverFarms | Sì | 
> | serverFarms/workers | No | 
> | siti | Sì | 
> | sites/domainOwnershipIdentifiers | No | 
> | sites/hostNameBindings | No | 
> | sites/instances | No | 
> | sites/instances/extensions | No | 
> | sites/premieraddons | Sì | 
> | sites/recommendations | No | 
> | sites/resourceHealthMetadata | No | 
> | sites/slots | Sì | 
> | sites/slots/hostNameBindings | No | 
> | sites/slots/instances | No | 
> | sites/slots/instances/extensions | No | 
> | sourceControls | No | 
> | validate | No | 
> | verifyHostingEnvironmentVnet | No | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No | 
> | diagnosticSettingsCategories | No | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DeviceServices | Sì | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | components | No | 
> | componentsSummary | No | 
> | monitorInstances | No | 
> | monitorInstancesSummary | No | 
> | monitors | No | 
> | notificationSettings | No | 

## <a name="next-steps"></a>Passaggi successivi

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).