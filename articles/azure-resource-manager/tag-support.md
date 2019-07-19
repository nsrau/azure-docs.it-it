---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304860"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](resource-group-using-tags.md). La colonna con etichetta **supporta i tag** indica se il tipo di risorsa ha una proprietà per il tag. La colonna etichetta **nel report dei costi** indica se il tipo di risorsa passa il tag al report dei costi.

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| DomainServices | Sì | Yes |
| DomainServices/oucontainer | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | No |  No |
| diagnosticSettingsCategories | No |  No |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| supportProviders | No |  No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| aadsupportcases | No |  No |
| addsservices | No |  No |
| agents | No |  No |
| anonymousapiusers | No |  No |
| configurazione | No |  No |
| logs | No |  No |
| reports | No |  No |
| services | No |  No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| configurazioni | No |  No |
| generateRecommendations | No |  No |
| raccomandazioni di film | No |  No |
| suppressions | No |  No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| actionRules | No |  No |
| alerts | No |  No |
| alertsList | No |  No |
| alertsSummary | No |  No |
| alertsSummaryList | No |  No |
| smartDetectorAlertRules | No |  No |
| smartDetectorRuntimeEnvironments | No |  No |
| smartGroups | No |  No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| reportFeedback | No |  No |
| servizio | Sì | Sì |
| validateServiceName | No |  No |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| attestationProviders | No |  No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| classicAdministrators | No |  No |
| denyAssignments | No |  No |
| elevateAccess | No |  No |
| locks | No |  No |
| autorizzazioni | No |  No |
| policyAssignments | No |  No |
| policyDefinitions | No |  No |
| policySetDefinitions | No |  No |
| providerOperations | No |  No |
| roleAssignments | No |  No |
| roleDefinitions | No |  No |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| automationAccounts | Yes | Yes |
| automationAccounts/configurations | Yes | Sì |
| automationAccounts/jobs | No |  No |
| automationAccounts/runbooks | Sì | Yes |
| automationAccounts/softwareUpdateConfigurations | No | No |
| automationAccounts/webhooks | No |  No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| environments | No |  No |
| environments/accounts | No |  No |
| environments/accounts/namespaces | No |  No |
| environments/accounts/namespaces/configurations | No |  No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| b2cDirectories | Sì | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| registrations | Yes | Yes |
| registrations/customerSubscriptions | No |  No |
| registrations/products | No |  No |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| batchAccounts | Sì | Sì |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| billingAccounts | No |  No |
| billingAccounts/billingProfiles | No |  No |
| billingAccounts/billingProfiles/billingSubscriptions | No |  No |
| billingAccounts/billingProfiles/invoices | No |  No |
| billingAccounts/billingProfiles/invoices/pricesheet | No |  No |
| billingAccounts/billingProfiles/operationStatus | No |  No |
| billingAccounts/billingProfiles/paymentMethods | No |  No |
| billingAccounts/billingProfiles/policies | No |  No |
| billingAccounts/billingProfiles/pricesheet | No |  No |
| billingAccounts/billingProfiles/products | No |  No |
| billingAccounts/billingProfiles/transactions | No |  No |
| billingAccounts/billingSubscriptions | No |  No |
| billingAccounts/departments | No |  No |
| billingAccounts/eligibleOffers | No |  No |
| billingAccounts/enrollmentAccounts | No |  No |
| billingAccounts/invoices | No |  No |
| billingAccounts/invoiceSections | No |  No |
| billingAccounts/invoiceSections/billingSubscriptions | No |  No |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | No |  No |
| billingAccounts/invoiceSections/importRequests | No |  No |
| billingAccounts/invoiceSections/initiateImportRequest | No |  No |
| billingAccounts/invoiceSections/initiateTransfer | No |  No |
| billingAccounts/invoiceSections/operationStatus | No |  No |
| billingAccounts/invoiceSections/products | No |  No |
| billingAccounts/invoiceSections/transfers | No |  No |
| billingAccounts/products | No |  No |
| billingAccounts/projects | No |  No |
| billingAccounts/projects/billingSubscriptions | No |  No |
| billingAccounts/projects/importRequests | No |  No |
| billingAccounts/projects/initiateImportRequest | No |  No |
| billingAccounts/projects/operationStatus | No |  No |
| billingAccounts/projects/products | No |  No |
| billingAccounts/transactions | No |  No |
| billingPeriods | No |  No |
| BillingPermissions | No |  No |
| billingProperty | No |  No |
| BillingRoleAssignments | No |  No |
| BillingRoleDefinitions | No |  No |
| CreateBillingRoleAssignment | No |  No |
| departments | No |  No |
| enrollmentAccounts | No |  No |
| importRequests | No |  No |
| importRequests/acceptImportRequest | No |  No |
| importRequests/declineImportRequest | No |  No |
| invoices | No |  No |
| transfers | No |  No |
| transfers/acceptTransfer | No |  No |
| transfers/declineTransfer | No |  No |
| transfers/operationStatus | No |  No |
| usagePlans | No |  No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| mapApis | Sì | Sì |
| updateCommunicationPreference | No |  No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| BizTalk | Yes | Sì |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| blueprintAssignments | No |  No |
| blueprintAssignments/assignmentOperations | No |  No |
| blueprintAssignments/operations | No |  No |
| blueprints | No |  No |
| blueprints/artifacts | No |  No |
| blueprints/versions | No |  No |
| blueprints/versions/artifacts | No |  No |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| botServices | Yes | Sì |
| botServices/channels | No |  No |
| botServices/connections | No |  No |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| Redis | Sì | Yes |
| RedisConfigDefinition | No |  No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| appliedReservations | No |  No |
| calculatePrice | No |  No |
| catalogs | No |  No |
| commercialReservationOrders | No |  No |
| reservationOrders | No |  No |
| reservationOrders/calculateRefund | No |  No |
| reservationOrders/merge | No |  No |
| reservationOrders/reservations | No |  No |
| reservationOrders/reservations/revisions | No |  No |
| reservationOrders/return | No |  No |
| reservationOrders/split | No |  No |
| reservationOrders/swap | No |  No |
| reservations | No |  No |
| resources | No |  No |
| validateReservationOrder | No |  No |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| edgenodes | No |  No |
| profiles | Yes | Sì |
| profiles/endpoints | Sì | Yes |
| profiles/endpoints/customdomains | No |  No |
| profiles/endpoints/origins | No |  No |
| validateProbe | No |  No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| certificateOrders | Yes | Yes |
| certificateOrders/certificates | No |  No |
| validateCertificateRegistrationInformation | No |  No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| capabilities | No |  No |
| domainNames | No |  No |
| domainNames/capabilities | No |  No |
| domainNames/internalLoadBalancers | No |  No |
| domainNames/serviceCertificates | No |  No |
| domainNames/slots | No |  No |
| domainNames/slots/roles | No |  No |
| moveSubscriptionResources | No |  No |
| operatingSystemFamilies | No |  No |
| operatingSystems | No |  No |
| quotas | No |  No |
| resourceTypes | No |  No |
| validateSubscriptionMoveAvailability | No |  No |
| virtualMachines | No |  No |
| virtualMachines/diagnosticSettings | No |  No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | No |  No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| capabilities | No |  No |
| expressRouteCrossConnections | No |  No |
| expressRouteCrossConnections/peerings | No |  No |
| gatewaySupportedDevices | No |  No |
| networkSecurityGroups | No |  No |
| quotas | No |  No |
| reservedIps | No |  No |
| virtualNetworks | No |  No |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | No |  No |
| virtualNetworks/virtualNetworkPeerings | No |  No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| capabilities | No |  No |
| disks | No |  No |
| images | No |  No |
| osImages | No |  No |
| osPlatformImages | No |  No |
| publicImages | No |  No |
| quotas | No |  No |
| storageAccounts | No |  No |
| storageAccounts/services | No |  No |
| storageAccounts/services/diagnosticSettings | No |  No |
| storageAccounts/vmImages | No |  No |
| vmImages | No |  No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Yes | Sì |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| RateCard | No |  No |
| UsageAggregates | No |  No |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| availabilitySets | Yes | Yes |
| disks | Sì | Sì |
| images | Yes | Sì |
| restorePointCollections | Sì | Sì |
| restorePointCollections/restorePoints | No |  No |
| sharedVMImages | Sì | Sì |
| sharedVMImages/versions | Sì | Sì |
| snapshots | Sì | Yes |
| virtualMachines | Yes | Sì |
| virtualMachines/diagnosticSettings | No |  No |
| virtualMachines/extensions | Yes | Yes |
| virtualMachineScaleSets | Yes | Yes |
| virtualMachineScaleSets/extensions | No |  No |
| virtualMachineScaleSets/networkInterfaces | No |  No |
| virtualMachineScaleSets/publicIPAddresses | No |  No |
| virtualMachineScaleSets/virtualMachines | No |  No |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | No |  No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| AggregatedCost | No |  No |
| Saldi | No |  No |
| Budget | No |  No |
| Charges | No |  No |
| CostTags | No |  No |
| credits | No |  No |
| eventi | No |  No |
| Previsioni | No |  No |
| lots | No |  No |
| Marketplace | No |  No |
| Pricesheets | No |  No |
| products | No |  No |
| ReservationDetails | No |  No |
| ReservationRecommendations | No |  No |
| ReservationSummaries | No |  No |
| ReservationTransactions | No |  No |
| Tag | No |  No |
| Termini | No |  No |
| UsageDetails | No |  No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| containerGroups | Yes | Sì |
| serviceAssociationLinks | No |  No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| registries | Sì | Sì |
| registries/builds | No |  No |
| registries/builds/cancel | No |  No |
| registries/builds/getLogLink | No |  No |
| registries/buildTasks | Sì | Sì |
| registries/buildTasks/steps | No |  No |
| registries/eventGridFilters | No |  No |
| registries/getBuildSourceUploadUrl | No |  No |
| registries/GetCredentials | No |  No |
| registries/importImage | No |  No |
| registries/queueBuild | No |  No |
| registries/regenerateCredential | No |  No |
| registries/regenerateCredentials | No |  No |
| registries/replications | Yes | Sì |
| registries/runs | No |  No |
| registries/runs/cancel | No |  No |
| registries/scheduleRun | No |  No |
| registries/tasks | Sì | Sì |
| registries/updatePolicies | No |  No |
| registries/webhooks | Sì | Sì |
| registries/webhooks/getCallbackConfig | No |  No |
| registries/webhooks/ping | No |  No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| containerServices | Yes | Sì |
| managedClusters | Sì | Yes |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| scala Web | Sì | Sì |
| updateCommunicationPreference | No |  No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| Avvisi | No |  No |
| BillingAccounts | No |  No |
| Connettori | Sì | Yes |
| Departments | No |  No |
| Dimensioni | No |  No |
| EnrollmentAccounts | No |  No |
| Query | No |  No |
| register | No |  No |
| Reportconfigs | No |  No |
| Report | No |  No |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| hubs | Sì | Sì |
| hubs/authorizationPolicies | No |  No |
| hubs/connectors | No |  No |
| hubs/connectors/mappings | No |  No |
| hubs/interactions | No |  No |
| hubs/kpi | No |  No |
| hubs/links | No |  No |
| hubs/profiles | No |  No |
| hubs/roleAssignments | No |  No |
| hubs/roles | No |  No |
| hubs/suggestTypeSchema | No |  No |
| hubs/views | No |  No |
| hubs/widgetTypes | No |  No |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| jobs | Yes | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Yes | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| aree di lavoro | Sì | No |
| workspaces/virtualNetworkPeerings | No |  No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| catalogs | Sì | Sì |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| connectionManagers | Sì | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| dataFactories | Sì | No |
| dataFactories/diagnosticSettings | No |  No |
| dataFactorySchema | No |  No |
| factories | Sì | No |
| factories/integrationRuntimes | No |  No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Yes | Sì |
| accounts/dataLakeStoreAccounts | No |  No |
| accounts/storageAccounts | No |  No |
| accounts/storageAccounts/containers | No |  No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| accounts/eventGridFilters | No |  No |
| accounts/firewallRules | No |  No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| services | Yes | Sì |
| services/projects | Sì | Yes |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| servers | Sì | Yes |
| servers/recoverableServers | No |  No |
| servers/virtualNetworkRules | No |  No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| servers | Sì | Yes |
| servers/recoverableServers | No |  No |
| servers/virtualNetworkRules | No |  No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| servers | Sì | Sì |
| servers/advisors | No |  No |
| servers/queryTexts | No |  No |
| servers/recoverableServers | No |  No |
| servers/topQueryStatistics | No |  No |
| servers/virtualNetworkRules | No |  No |
| servers/waitStatistics | No |  No |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| Hub IoT | Sì | Yes |
| IotHubs/eventGridFilters | No |  No |
| ProvisioningServices | Sì | Sì |
| usages | No |  No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| controllers | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| labs | Yes | Yes |
| labs/serviceRunners | Sì | Sì |
| labs/virtualMachines | Sì | Yes |
| schedules | Yes | Sì |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| databaseAccountNames | No |  No |
| databaseAccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| domains | Sì | Yes |
| domains/domainOwnershipIdentifiers | No |  No |
| generateSsoRequest | No |  No |
| topLevelDomains | No |  No |
| validateDomainRegistrationInformation | No |  No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| lcsprojects | No |  No |
| lcsprojects/clouddeployments | No |  No |
| lcsprojects/connectors | No |  No |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| domains | Sì | No |
| domains/topics | No |  No |
| eventSubscriptions | No |  No |
| extensionTopics | No |  No |
| topics | Yes | No |
| topicTypes | No |  No |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| clusters | Yes | No |
| spazi dei nomi | Sì | No |
| namespaces/authorizationrules | No |  No |
| namespaces/disasterrecoveryconfigs | No |  No |
| namespaces/eventhubs | No |  No |
| namespaces/eventhubs/authorizationrules | No |  No |
| namespaces/eventhubs/consumergroups | No |  No |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| funzionalità | No |  No |
| provider | No |  No |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| enroll | No |  No |
| galleryitems | No |  No |
| generateartifactaccessuri | No |  No |
| myareas | No |  No |
| myareas/areas | No |  No |
| myareas/areas/areas | No |  No |
| myareas/areas/areas/galleryitems | No |  No |
| myareas/areas/galleryitems | No |  No |
| myareas/galleryitems | No |  No |
| register | No |  No |
| resources | No |  No |
| retrieveresourcesbyid | No |  No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | No |  No |
| software | No |  No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| hanaInstances | Yes |  Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| clusters | Yes | Sì |
| clusters/applications | No |  No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| jobs | Yes | Yes |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| labelGroups | No |  No |
| labelGroups/labels | No |  No |
| labelGroups/labels/conditions | No |  No |
| labelGroups/labels/subLabels | No |  No |
| labelGroups/labels/subLabels/conditions | No |  No |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| actiongroups | Sì | Sì |
| activityLogAlerts | Sì | Yes |
| alertrules | Yes | Yes |
| automatedExportSettings | No |  No |
| autoscalesettings | Sì | Yes |
| baseline | No |  No |
| calculatebaseline | No |  No |
| components | Sì | Yes |
| components/events | No |  No |
| components/pricingPlans | No |  No |
| components/query | No |  No |
| diagnosticSettings | No |  No |
| diagnosticSettingsCategories | No |  No |
| eventCategories | No |  No |
| eventtypes | No |  No |
| extendedDiagnosticSettings | No |  No |
| logDefinitions | No |  No |
| logprofiles | No |  No |
| logs | No |  No |
| metricAlerts | Yes | Sì |
| migrateToNewPricingModel | No |  No |
| myWorkbooks | No |  No |
| query | No |  No |
| rollbackToLegacyPricingModel | No |  No |
| scheduledqueryrules | Sì | Sì |
| vmInsightsOnboardingStatuses | No |  No |
| webtests | Sì | Sì |
| workbooks | Sì | Sì |

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | No |  No |
| diagnosticSettingsCategories | No |  No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| IoTApps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| Grafico | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| deletedVaults | No |  No |
| insiemi di credenziali | Sì | Sì |
| vaults/accessPolicies | No |  No |
| vaults/secrets | No |  No |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | Sì |
| clusters/databases | No |  No |
| clusters/databases/dataconnections | No |  No |
| clusters/databases/eventhubconnections | No |  No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| labaccounts | Yes | Sì |
| users | No |  No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| logs | No |  No |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| integrationAccounts | Sì | Yes |
| flussi di lavoro | Sì | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| commitmentPlans | Sì | Sì |
| webServices | Sì | Yes |
| Aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Yes |
| accounts/workspaces | Sì | Sì |
| accounts/workspaces/projects | Sì | Sì |
| teamAccounts | Yes | Yes |
| teamAccounts/workspaces | Sì | Sì |
| teamAccounts/workspaces/projects | Sì | Sì |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| aree di lavoro | Sì | Sì |
| workspaces/computes | No |  No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| Identities | No |  No |
| userAssignedIdentities | Sì | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| getEntities | No |  No |
| managementGroups | No |  No |
| resources | No |  No |
| startTenantBackfill | No |  No |
| tenantBackfillStatus | No |  No |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Yes |
| accounts/eventGridFilters | No |  No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| offers | No |  No |
| offerTypes | No |  No |
| offerTypes/publishers | No |  No |
| offerTypes/publishers/offers | No |  No |
| offerTypes/publishers/offers/plans | No |  No |
| offerTypes/publishers/offers/plans/agreements | No |  No |
| offerTypes/publishers/offers/plans/configs | No |  No |
| offerTypes/publishers/offers/plans/configs/importImage | No |  No |
| privategalleryitems | No |  No |
| products | No |  No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| classicDevServices | Sì | Sì |
| updateCommunicationPreference | No |  No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| agreements | No |  No |
| offertypes | No |  No |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| mediaservices | Yes | Yes |
| mediaservices/accountFilters | No |  No |
| mediaservices/assets | No |  No |
| mediaservices/assets/assetFilters | No |  No |
| mediaservices/contentKeyPolicies | No |  No |
| mediaservices/eventGridFilters | No |  No |
| mediaservices/liveEventOperations | No |  No |
| mediaservices/liveEvents | Sì | Sì |
| mediaservices/liveEvents/liveOutputs | No |  No |
| mediaservices/liveOutputOperations | No |  No |
| mediaservices/streamingEndpointOperations | No |  No |
| mediaservices/streamingEndpoints | Sì | Sì |
| mediaservices/streamingLocators | No |  No |
| mediaservices/streamingPolicies | No |  No |
| mediaservices/transforms | No |  No |
| mediaservices/transforms/jobs | No |  No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| projects | Yes | Sì |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| applicationGateways | Sì | No |
| applicationSecurityGroups | Sì | Sì |
| azureFirewallFqdnTags | No |  No |
| azureFirewalls | Sì | No |
| bgpServiceCommunities | No |  No |
| connections | Yes | Sì |
| ddosCustomPolicies | Sì | Sì |
| ddosProtectionPlans | Sì | Sì |
| dnsOperationStatuses | No |  No |
| dnszones | Sì | Sì |
| dnszones/A | No |  No |
| dnszones/AAAA | No |  No |
| dnszones/all | No |  No |
| dnszones/CAA | No |  No |
| dnszones/CNAME | No |  No |
| dnszones/MX | No |  No |
| dnszones/NS | No |  No |
| dnszones/PTR | No |  No |
| dnszones/recordsets | No |  No |
| dnszones/SOA | No |  No |
| dnszones/SRV | No |  No |
| dnszones/TXT | No |  No |
| expressRouteCircuits | Yes  | No |
| expressRouteServiceProviders | No |  No |
| frontdoors | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) | Sì |
| frontdoorWebApplicationFirewallPolicies | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) | Sì |
| getDnsResourceReference | No |  No |
| interfaceEndpoints | Sì | Sì |
| internalNotify | No |  No |
| loadBalancers | Yes | No |
| localNetworkGateways | Sì | Sì |
| natGateways | Yes | Sì |
| networkIntentPolicies | Yes | Yes |
| networkInterfaces | Yes | Yes |
| networkProfiles | Sì | Sì |
| networkSecurityGroups | Sì | Sì |
| networkWatchers | Sì | No |
| networkWatchers/connectionMonitors | Sì | No |
| networkWatchers/lenses | Sì | No |
| networkWatchers/pingMeshes | Sì | No |
| privateLinkServices | Yes | Sì |
| publicIPAddresses | Sì | Sì |
| publicIPPrefixes | Sì | Sì |
| routeFilters | Yes | Sì |
| routeTables | Sì | Sì |
| serviceEndpointPolicies | Yes | Sì |
| trafficManagerGeographicHierarchies | No |  No |
| trafficmanagerprofiles | Sì | Sì |
| trafficmanagerprofiles/heatMaps | No |  No |
| virtualHubs | Sì | Yes |
| virtualNetworkGateways | Sì | Sì |
| virtualNetworks | Sì | Sì |
| virtualNetworks/subnets | No |  No |
| virtualNetworkTaps | Yes | Yes |
| virtualWans | Sì | Sì |
| vpnGateways | Sì | No |
| vpnSites | Yes | Sì |
| webApplicationFirewallPolicies | Sì | Sì |

<a id="frontdoor" />

Per il servizio front-end di Azure, è possibile applicare tag quando si crea la risorsa, ma non è attualmente supportata l'aggiornamento o l'aggiunta di tag.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Sì | No |
| namespaces/notificationHubs | Sì | No |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| devices | No |  No |
| linkTargets | No |  No |
| storageInsightConfigs | No |  No |
| aree di lavoro | Yes | Yes |
| workspaces/dataSources | No |  No |
| workspaces/linkedServices | No |  No |
| workspaces/query | No |  No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| managementassociations | No |  No |
| managementconfigurations | Sì | Yes |
| solutions | Sì | Sì |
| Viste | Sì | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| policyEvents | No |  No |
| policyStates | No |  No |
| policyTrackedResources | No |  No |
| remediations | No |  No |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| consoles | No |  No |
| dashboards | Sì | Sì |
| userSettings | No |  No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| workspaceCollections | Sì | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| capacities | Sì | Sì |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| backupProtectedItems | No |  No |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Yes | Yes |
| namespaces/authorizationrules | No |  No |
| namespaces/hybridconnections | No |  No |
| namespaces/hybridconnections/authorizationrules | No |  No |
| namespaces/wcfrelays | No |  No |
| namespaces/wcfrelays/authorizationrules | No |  No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| resources | No |  No |
| subscriptionsStatus | No |  No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| availabilityStatuses | No |  No |
| childAvailabilityStatuses | No |  No |
| childResources | No |  No |
| eventi | No |  No |
| impactedResources | No |  No |
| Notifiche | No |  No |

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| deployments | No |  No |
| deployments/operations | No |  No |
| links | No |  No |
| notifyResourceJobs | No |  No |
| provider | No |  No |
| resourceGroups | No |  No |
| resources | No |  No |
| subscriptions | No |  No |
| subscriptions/providers | No |  No |
| subscriptions/resourceGroups | No |  No |
| subscriptions/resourcegroups/resources | No |  No |
| subscriptions/resources | No |  No |
| subscriptions/tagnames | No |  No |
| subscriptions/tagNames/tagValues | No |  No |
| tenants | No |  No |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| scala Web | Yes | Yes |
| saasresources | No |  No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| flows | Sì | Yes |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | No |  No |
| searchServices | Sì | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | No |  No |
| alerts | No |  No |
| allowedConnections | No |  No |
| appliances | No |  No |
| applicationWhitelistings | No |  No |
| AutoProvisioningSettings | No |  No |
| Compliances | No |  No |
| dataCollectionAgents | No |  No |
| discoveredSecuritySolutions | No |  No |
| externalSecuritySolutions | No |  No |
| InformationProtectionPolicies | No |  No |
| jitNetworkAccessPolicies | No |  No |
| monitoring | No |  No |
| monitoring/antimalware | No |  No |
| monitoring/baseline | No |  No |
| monitoring/patch | No |  No |
| criteri | No |  No |
| pricings | No |  No |
| securityContacts | No |  No |
| securitySolutions | No |  No |
| securitySolutionsReferenceData | No |  No |
| securityStatus | No |  No |
| securityStatus/endpoints | No |  No |
| securityStatus/subnets | No |  No |
| securityStatus/virtualMachines | No |  No |
| securityStatuses | No |  No |
| securityStatusesSummaries | No |  No |
| Scheda Impostazioni | No |  No |
| attività | No |  No |
| topologies | No |  No |
| workspaceSettings | No |  No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | No |  No |
| diagnosticSettingsCategories | No |  No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Sì | No |
| namespaces/authorizationrules | No |  No |
| namespaces/disasterrecoveryconfigs | No |  No |
| namespaces/eventgridfilters | No |  No |
| namespaces/queues | No |  No |
| namespaces/queues/authorizationrules | No |  No |
| namespaces/topics | No |  No |
| namespaces/topics/authorizationrules | No |  No |
| namespaces/topics/subscriptions | No |  No |
| namespaces/topics/subscriptions/rules | No |  No |
| premiumMessagingRegions | No |  No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | Sì |
| clusters/applications | No |  No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| scala Web | Sì | Sì |
| gateways | Yes | Yes |
| networks | Sì | Sì |
| chiavi private | Sì | Yes |
| volumes | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| SignalR | Yes | Yes |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Sì | Yes |
| appliances | Sì | Yes |
| applicationDefinitions | Yes | Yes |
| scala Web | Sì | Yes |
| jitRequests | Yes | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| managedInstances | Yes | Yes |
| managedInstances/databases | Sì (vedere la nota seguente) | Sì |
| managedInstances/databases/backupShortTermRetentionPolicies | No | No |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | No | No |
| managedInstances/databases/vulnerabilityAssessments | No | No |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | No | No |
| managedInstances/encryptionProtector | No | No |
| managedInstances/keys | No | No |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No | No |
| managedInstances/vulnerabilityAssessments | No | No |
| servers | Sì | Sì |
| servers/administrators | No |  No |
| servers/communicationLinks | No |  No |
| servers/databases | Sì (vedere la nota seguente) | Sì |
| servers/encryptionProtector | No |  No |
| servers/firewallRules | No |  No |
| servers/keys | No |  No |
| servers/restorableDroppedDatabases | No |  No |
| servers/serviceobjectives | No |  No |
| servers/tdeCertificates | No |  No |

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Yes | Yes |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | No |  No |
| SqlVirtualMachines | Sì | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| storageAccounts | Yes | Yes |
| storageAccounts/blobServices | No |  No |
| storageAccounts/fileServices | No |  No |
| storageAccounts/queueServices | No |  No |
| storageAccounts/services | No |  No |
| storageAccounts/tableServices | No |  No |
| usages | No |  No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| storageSyncServices | Sì | Yes |
| storageSyncServices/registeredServers | No |  No |
| storageSyncServices/syncGroups | No |  No |
| storageSyncServices/syncGroups/cloudEndpoints | No |  No |
| storageSyncServices/syncGroups/serverEndpoints | No |  No |
| storageSyncServices/workflows | No |  No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| managers | Yes | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| streamingjobs | Sì (vedere la nota seguente) | Sì |
| streamingjobs/diagnosticSettings | No |  No |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| CreateSubscription | No |  No |
| SubscriptionDefinitions | No |  No |
| SubscriptionOperations | No |  No |

## <a name="microsoftsupport"></a>microsoft.support
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| supporttickets | No |  No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| providerRegistrations | Sì | Sì |
| resources | Yes | Sì |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| environments | Yes | No |
| environments/accessPolicies | No |  No |
| environments/eventsources | Sì | No |
| environments/referenceDataSets | Sì | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Yes | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | No |  No |
| apiManagementAccounts/apiAcls | No |  No |
| apiManagementAccounts/apis | No |  No |
| apiManagementAccounts/apis/apiAcls | No |  No |
| apiManagementAccounts/apis/connectionAcls | No |  No |
| apiManagementAccounts/apis/connections | No |  No |
| apiManagementAccounts/apis/connections/connectionAcls | No |  No |
| apiManagementAccounts/apis/localizedDefinitions | No |  No |
| apiManagementAccounts/connectionAcls | No |  No |
| apiManagementAccounts/connections | No |  No |
| billingMeters | No |  No |
| certificates | Sì | Yes |
| connectionGateways | Sì | Yes |
| connections | Sì | Sì |
| customApis | Yes | Sì |
| deletedSites | No |  No |
| functions | No |  No |
| hostingEnvironments | Yes | Sì |
| hostingEnvironments/multiRolePools | No |  No |
| hostingEnvironments/multiRolePools/instances | No |  No |
| hostingEnvironments/workerPools | No |  No |
| hostingEnvironments/workerPools/instances | No |  No |
| publishingUsers | No |  No |
| raccomandazioni di film | No |  No |
| resourceHealthMetadata | No |  No |
| runtimes | No |  No |
| serverFarms | Yes | Sì |
| serverFarms/workers | No |  No |
| siti | Yes | Yes |
| sites/domainOwnershipIdentifiers | No |  No |
| sites/hostNameBindings | No |  No |
| sites/instances | No |  No |
| sites/instances/extensions | No |  No |
| sites/premieraddons | Sì | Sì |
| sites/recommendations | No |  No |
| sites/resourceHealthMetadata | No |  No |
| sites/slots | Sì | Sì |
| sites/slots/hostNameBindings | No |  No |
| sites/slots/instances | No |  No |
| sites/slots/instances/extensions | No |  No |
| sourceControls | No |  No |
| validate | No |  No |
| verifyHostingEnvironmentVnet | No |  No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | No |  No |
| diagnosticSettingsCategories | No |  No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| DeviceServices | Sì | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo di risorsa | Tag supportati | Tag nel report sui costi |
| ------------- | ----------- | ----------- |
| components | No |  No |
| componentsSummary | No |  No |
| monitorInstances | No |  No |
| monitorInstancesSummary | No |  No |
| monitors | No |  No |
| notificationSettings | No |  No |

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
