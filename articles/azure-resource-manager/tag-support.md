---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/10/2019
ms.author: tomfitz
ms.openlocfilehash: 7ef37323fb8150e3a6b52800bfafa2585ae328c2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523229"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](resource-group-using-tags.md). La colonna con etichettata **supporta i tag** indica se il tipo di risorsa dispone di una proprietà per il tag. La colonna con etichettata **Tag nel report dei costi** indica se il tipo di risorsa passa al tag per il report dei costi.

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| DomainServices | Sì | Sì |
| DomainServices/oucontainer | N. | N. |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | N. |  N. |
| diagnosticSettingsCategories | N. |  N. |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| supportProviders | N. |  N. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| aadsupportcases | N. |  N. |
| addsservices | N. |  N. |
| agents | N. |  N. |
| anonymousapiusers | N. |  N. |
| configurazione | N. |  N. |
| log | N. |  N. |
| report | N. |  N. |
| services | N. |  N. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| configurazioni | N. |  N. |
| generateRecommendations | N. |  N. |
| raccomandazioni | N. |  N. |
| suppressions | N. |  N. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| actionRules | N. |  N. |
| avvisi | N. |  N. |
| alertsList | N. |  N. |
| alertsSummary | N. |  N. |
| alertsSummaryList | N. |  N. |
| smartDetectorAlertRules | N. |  N. |
| smartDetectorRuntimeEnvironments | N. |  N. |
| smartGroups | N. |  N. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| server | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| reportFeedback | N. |  N. |
| servizio | Sì | Sì |
| validateServiceName | N. |  N. |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| attestationProviders | N. |  N. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| classicAdministrators | N. |  N. |
| denyAssignments | N. |  N. |
| elevateAccess | N. |  N. |
| locks | N. |  N. |
| autorizzazioni | N. |  N. |
| policyAssignments | N. |  N. |
| policyDefinitions | N. |  N. |
| policySetDefinitions | N. |  N. |
| providerOperations | N. |  N. |
| roleAssignments | N. |  N. |
| roleDefinitions | N. |  N. |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| automationAccounts | Sì | Sì |
| automationAccounts/configurations | Sì | Sì |
| automationAccounts/jobs | N. |  N. |
| automationAccounts/runbooks | Sì | Sì |
| automationAccounts/softwareUpdateConfigurations | N. | N. |
| automationAccounts/webhooks | N. |  N. |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| environments | N. |  N. |
| environments/accounts | N. |  N. |
| environments/accounts/namespaces | N. |  N. |
| environments/accounts/namespaces/configurations | N. |  N. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| b2cDirectories | Sì | N. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| registrations | Sì | Sì |
| registrations/customerSubscriptions | N. |  N. |
| registrations/products | N. |  N. |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| batchAccounts | Sì | Sì |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| billingAccounts | N. |  N. |
| billingAccounts/billingProfiles | N. |  N. |
| billingAccounts/billingProfiles/billingSubscriptions | N. |  N. |
| billingAccounts/billingProfiles/invoices | N. |  N. |
| billingAccounts/billingProfiles/invoices/pricesheet | N. |  N. |
| billingAccounts/billingProfiles/operationStatus | N. |  N. |
| billingAccounts/billingProfiles/paymentMethods | N. |  N. |
| billingAccounts/billingProfiles/policies | N. |  N. |
| billingAccounts/billingProfiles/pricesheet | N. |  N. |
| billingAccounts/billingProfiles/products | N. |  N. |
| billingAccounts/billingProfiles/transactions | N. |  N. |
| billingAccounts/billingSubscriptions | N. |  N. |
| billingAccounts/departments | N. |  N. |
| billingAccounts/eligibleOffers | N. |  N. |
| billingAccounts/enrollmentAccounts | N. |  N. |
| billingAccounts/invoices | N. |  N. |
| billingAccounts/invoiceSections | N. |  N. |
| billingAccounts/invoiceSections/billingSubscriptions | N. |  N. |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | N. |  N. |
| billingAccounts/invoiceSections/importRequests | N. |  N. |
| billingAccounts/invoiceSections/initiateImportRequest | N. |  N. |
| billingAccounts/invoiceSections/initiateTransfer | N. |  N. |
| billingAccounts/invoiceSections/operationStatus | N. |  N. |
| billingAccounts/invoiceSections/products | N. |  N. |
| billingAccounts/invoiceSections/transfers | N. |  N. |
| billingAccounts/products | N. |  N. |
| billingAccounts/projects | N. |  N. |
| billingAccounts/projects/billingSubscriptions | N. |  N. |
| billingAccounts/projects/importRequests | N. |  N. |
| billingAccounts/projects/initiateImportRequest | N. |  N. |
| billingAccounts/projects/operationStatus | N. |  N. |
| billingAccounts/projects/products | N. |  N. |
| billingAccounts/transactions | N. |  N. |
| billingPeriods | N. |  N. |
| BillingPermissions | N. |  N. |
| billingProperty | N. |  N. |
| BillingRoleAssignments | N. |  N. |
| BillingRoleDefinitions | N. |  N. |
| CreateBillingRoleAssignment | N. |  N. |
| departments | N. |  N. |
| enrollmentAccounts | N. |  N. |
| importRequests | N. |  N. |
| importRequests/acceptImportRequest | N. |  N. |
| importRequests/declineImportRequest | N. |  N. |
| invoices | N. |  N. |
| transfers | N. |  N. |
| transfers/acceptTransfer | N. |  N. |
| transfers/declineTransfer | N. |  N. |
| transfers/operationStatus | N. |  N. |
| usagePlans | N. |  N. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| mapApis | Sì | Sì |
| updateCommunicationPreference | N. |  N. |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| BizTalk | Sì | Sì |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| blueprintAssignments | N. |  N. |
| blueprintAssignments/assignmentOperations | N. |  N. |
| blueprintAssignments/operations | N. |  N. |
| blueprints | N. |  N. |
| blueprints/artifacts | N. |  N. |
| blueprints/versions | N. |  N. |
| blueprints/versions/artifacts | N. |  N. |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| botServices | Sì | Sì |
| botServices/channels | N. |  N. |
| botServices/connections | N. |  N. |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| Redis | Sì | Sì |
| RedisConfigDefinition | N. |  N. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| appliedReservations | N. |  N. |
| calculatePrice | N. |  N. |
| catalogs | N. |  N. |
| commercialReservationOrders | N. |  N. |
| reservationOrders | N. |  N. |
| reservationOrders/calculateRefund | N. |  N. |
| reservationOrders/merge | N. |  N. |
| reservationOrders/reservations | N. |  N. |
| reservationOrders/reservations/revisions | N. |  N. |
| reservationOrders/return | N. |  N. |
| reservationOrders/split | N. |  N. |
| reservationOrders/swap | N. |  N. |
| reservations | N. |  N. |
| risorse | N. |  N. |
| validateReservationOrder | N. |  N. |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| edgenodes | N. |  N. |
| Profili | Sì | Sì |
| profiles/endpoints | Sì | Sì |
| profiles/endpoints/customdomains | N. |  N. |
| profiles/endpoints/origins | N. |  N. |
| validateProbe | N. |  N. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| certificateOrders | Sì | Sì |
| certificateOrders/certificates | N. |  N. |
| validateCertificateRegistrationInformation | N. |  N. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| capabilities | N. |  N. |
| domainNames | N. |  N. |
| domainNames/capabilities | N. |  N. |
| domainNames/internalLoadBalancers | N. |  N. |
| domainNames/serviceCertificates | N. |  N. |
| domainNames/slots | N. |  N. |
| domainNames/slots/roles | N. |  N. |
| moveSubscriptionResources | N. |  N. |
| operatingSystemFamilies | N. |  N. |
| operatingSystems | N. |  N. |
| quotas | N. |  N. |
| resourceTypes | N. |  N. |
| validateSubscriptionMoveAvailability | N. |  N. |
| virtualMachines | N. |  N. |
| virtualMachines/diagnosticSettings | N. |  N. |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | N. |  N. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| capabilities | N. |  N. |
| expressRouteCrossConnections | N. |  N. |
| expressRouteCrossConnections/peerings | N. |  N. |
| gatewaySupportedDevices | N. |  N. |
| networkSecurityGroups | N. |  N. |
| quotas | N. |  N. |
| reservedIps | N. |  N. |
| virtualNetworks | N. |  N. |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | N. |  N. |
| virtualNetworks/virtualNetworkPeerings | N. |  N. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| capabilities | N. |  N. |
| dischi | N. |  N. |
| immagini | N. |  N. |
| osImages | N. |  N. |
| osPlatformImages | N. |  N. |
| publicImages | N. |  N. |
| quotas | N. |  N. |
| storageAccounts | N. |  N. |
| storageAccounts/services | N. |  N. |
| storageAccounts/services/diagnosticSettings | N. |  N. |
| storageAccounts/vmImages | N. |  N. |
| vmImages | N. |  N. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| RateCard | N. |  N. |
| UsageAggregates | N. |  N. |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| availabilitySets | Sì | Sì |
| dischi | Sì | Sì |
| immagini | Sì | Sì |
| restorePointCollections | Sì | Sì |
| restorePointCollections/restorePoints | N. |  N. |
| sharedVMImages | Sì | Sì |
| sharedVMImages/versions | Sì | Sì |
| snapshot | Sì | Sì |
| virtualMachines | Sì | Sì |
| virtualMachines/diagnosticSettings | N. |  N. |
| virtualMachines/extensions | Sì | Sì |
| virtualMachineScaleSets | Sì | Sì |
| virtualMachineScaleSets/extensions | N. |  N. |
| virtualMachineScaleSets/networkInterfaces | N. |  N. |
| virtualMachineScaleSets/publicIPAddresses | N. |  N. |
| virtualMachineScaleSets/virtualMachines | N. |  N. |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | N. |  N. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| AggregatedCost | N. |  N. |
| Saldi | N. |  N. |
| Budget | N. |  N. |
| Charges | N. |  N. |
| CostTags | N. |  N. |
| credits | N. |  N. |
| eventi | N. |  N. |
| Previsioni | N. |  N. |
| lots | N. |  N. |
| Marketplace | N. |  N. |
| Pricesheets | N. |  N. |
| products | N. |  N. |
| ReservationDetails | N. |  N. |
| ReservationRecommendations | N. |  N. |
| ReservationSummaries | N. |  N. |
| ReservationTransactions | N. |  N. |
| `Tags` | N. |  N. |
| Termini | N. |  N. |
| UsageDetails | N. |  N. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| containerGroups | Sì | Sì |
| serviceAssociationLinks | N. |  N. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| registries | Sì | Sì |
| registries/builds | N. |  N. |
| registries/builds/cancel | N. |  N. |
| registries/builds/getLogLink | N. |  N. |
| registries/buildTasks | Sì | Sì |
| registries/buildTasks/steps | N. |  N. |
| registries/eventGridFilters | N. |  N. |
| registries/getBuildSourceUploadUrl | N. |  N. |
| registries/GetCredentials | N. |  N. |
| registries/importImage | N. |  N. |
| registries/queueBuild | N. |  N. |
| registries/regenerateCredential | N. |  N. |
| registries/regenerateCredentials | N. |  N. |
| registries/replications | Sì | Sì |
| registries/runs | N. |  N. |
| registries/runs/cancel | N. |  N. |
| registries/scheduleRun | N. |  N. |
| registries/tasks | Sì | Sì |
| registries/updatePolicies | N. |  N. |
| registries/webhooks | Sì | Sì |
| registries/webhooks/getCallbackConfig | N. |  N. |
| registries/webhooks/ping | N. |  N. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| containerServices | Sì | Sì |
| managedClusters | Sì | Sì |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| applicazioni | Sì | Sì |
| updateCommunicationPreference | N. |  N. |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| Avvisi | N. |  N. |
| BillingAccounts | N. |  N. |
| Connettori | Sì | Sì |
| Departments | N. |  N. |
| Dimensioni | N. |  N. |
| EnrollmentAccounts | N. |  N. |
| Query | N. |  N. |
| register | N. |  N. |
| Reportconfigs | N. |  N. |
| Report | N. |  N. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| hubs | Sì | Sì |
| hubs/authorizationPolicies | N. |  N. |
| hubs/connectors | N. |  N. |
| hubs/connectors/mappings | N. |  N. |
| hubs/interactions | N. |  N. |
| hubs/kpi | N. |  N. |
| hubs/links | N. |  N. |
| hubs/profiles | N. |  N. |
| hubs/roleAssignments | N. |  N. |
| hubs/roles | N. |  N. |
| hubs/suggestTypeSchema | N. |  N. |
| hubs/views | N. |  N. |
| hubs/widgetTypes | N. |  N. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| processi | Sì | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Sì | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| aree di lavoro | Sì | N. |
| workspaces/virtualNetworkPeerings | N. |  N. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| catalogs | Sì | Sì |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| connectionManagers | Sì | Sì |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| dataFactories | Sì | N. |
| dataFactories/diagnosticSettings | N. |  N. |
| dataFactorySchema | N. |  N. |
| factories | Sì | N. |
| factories/integrationRuntimes | N. |  N. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| accounts/dataLakeStoreAccounts | N. |  N. |
| accounts/storageAccounts | N. |  N. |
| accounts/storageAccounts/containers | N. |  N. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| accounts/eventGridFilters | N. |  N. |
| accounts/firewallRules | N. |  N. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| services | Sì | Sì |
| services/projects | Sì | Sì |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| server | Sì | Sì |
| servers/recoverableServers | N. |  N. |
| servers/virtualNetworkRules | N. |  N. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| server | Sì | Sì |
| servers/recoverableServers | N. |  N. |
| servers/virtualNetworkRules | N. |  N. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| server | Sì | Sì |
| servers/advisors | N. |  N. |
| servers/queryTexts | N. |  N. |
| servers/recoverableServers | N. |  N. |
| servers/topQueryStatistics | N. |  N. |
| servers/virtualNetworkRules | N. |  N. |
| servers/waitStatistics | N. |  N. |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| Hub IoT | Sì | Sì |
| IotHubs/eventGridFilters | N. |  N. |
| ProvisioningServices | Sì | Sì |
| usages | N. |  N. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| controllers | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| labs | Sì | Sì |
| labs/serviceRunners | Sì | Sì |
| labs/virtualMachines | Sì | Sì |
| schedules | Sì | Sì |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| databaseAccountNames | N. |  N. |
| databaseAccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| domains | Sì | Sì |
| domains/domainOwnershipIdentifiers | N. |  N. |
| generateSsoRequest | N. |  N. |
| topLevelDomains | N. |  N. |
| validateDomainRegistrationInformation | N. |  N. |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| lcsprojects | N. |  N. |
| lcsprojects/clouddeployments | N. |  N. |
| lcsprojects/connectors | N. |  N. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| domains | Sì | N. |
| domains/topics | N. |  N. |
| eventSubscriptions | N. |  N. |
| extensionTopics | N. |  N. |
| topics | Sì | N. |
| topicTypes | N. |  N. |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | N. |
| spazi dei nomi | Sì | N. |
| namespaces/authorizationrules | N. |  N. |
| namespaces/disasterrecoveryconfigs | N. |  N. |
| namespaces/eventhubs | N. |  N. |
| namespaces/eventhubs/authorizationrules | N. |  N. |
| namespaces/eventhubs/consumergroups | N. |  N. |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| funzionalità | N. |  N. |
| provider | N. |  N. |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| enroll | N. |  N. |
| galleryitems | N. |  N. |
| generateartifactaccessuri | N. |  N. |
| myareas | N. |  N. |
| myareas/areas | N. |  N. |
| myareas/areas/areas | N. |  N. |
| myareas/areas/areas/galleryitems | N. |  N. |
| myareas/areas/galleryitems | N. |  N. |
| myareas/galleryitems | N. |  N. |
| register | N. |  N. |
| risorse | N. |  N. |
| retrieveresourcesbyid | N. |  N. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | N. |  N. |
| software | N. |  N. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| hanaInstances | Sì |  Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | Sì |
| clusters/applications | N. |  N. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| processi | Sì | Sì |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| labelGroups | N. |  N. |
| labelGroups/labels | N. |  N. |
| labelGroups/labels/conditions | N. |  N. |
| labelGroups/labels/subLabels | N. |  N. |
| labelGroups/labels/subLabels/conditions | N. |  N. |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| actiongroups | Sì | Sì |
| activityLogAlerts | Sì | Sì |
| alertrules | Sì | Sì |
| automatedExportSettings | N. |  N. |
| autoscalesettings | Sì | Sì |
| baseline | N. |  N. |
| calculatebaseline | N. |  N. |
| components | Sì | Sì |
| components/events | N. |  N. |
| components/pricingPlans | N. |  N. |
| components/query | N. |  N. |
| diagnosticSettings | N. |  N. |
| diagnosticSettingsCategories | N. |  N. |
| eventCategories | N. |  N. |
| eventtypes | N. |  N. |
| extendedDiagnosticSettings | N. |  N. |
| logDefinitions | N. |  N. |
| logprofiles | N. |  N. |
| log | N. |  N. |
| metricAlerts | Sì | Sì |
| migrateToNewPricingModel | N. |  N. |
| myWorkbooks | N. |  N. |
| query | N. |  N. |
| rollbackToLegacyPricingModel | N. |  N. |
| scheduledqueryrules | Sì | Sì |
| vmInsightsOnboardingStatuses | N. |  N. |
| webtests | Sì | Sì |
| cartelle di lavoro | Sì | Sì |

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | N. |  N. |
| diagnosticSettingsCategories | N. |  N. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| IoTApps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| Grafico | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| deletedVaults | N. |  N. |
| insiemi di credenziali | Sì | Sì |
| vaults/accessPolicies | N. |  N. |
| vaults/secrets | N. |  N. |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | Sì |
| clusters/databases | N. |  N. |
| clusters/databases/dataconnections | N. |  N. |
| clusters/databases/eventhubconnections | N. |  N. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| labaccounts | Sì | Sì |
| Utenti | N. |  N. |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| log | N. |  N. |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| integrationAccounts | Sì | Sì |
| flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| commitmentPlans | Sì | Sì |
| webServices | Sì | Sì |
| Aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| accounts/workspaces | Sì | Sì |
| accounts/workspaces/projects | Sì | Sì |
| teamAccounts | Sì | Sì |
| teamAccounts/workspaces | Sì | Sì |
| teamAccounts/workspaces/projects | Sì | Sì |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| aree di lavoro | Sì | Sì |
| workspaces/computes | N. |  N. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| Identità | N. |  N. |
| userAssignedIdentities | Sì | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| getEntities | N. |  N. |
| managementGroups | N. |  N. |
| risorse | N. |  N. |
| startTenantBackfill | N. |  N. |
| tenantBackfillStatus | N. |  N. |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| accounts/eventGridFilters | N. |  N. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| offers | N. |  N. |
| offerTypes | N. |  N. |
| offerTypes/publishers | N. |  N. |
| offerTypes/publishers/offers | N. |  N. |
| offerTypes/publishers/offers/plans | N. |  N. |
| offerTypes/publishers/offers/plans/agreements | N. |  N. |
| offerTypes/publishers/offers/plans/configs | N. |  N. |
| offerTypes/publishers/offers/plans/configs/importImage | N. |  N. |
| privategalleryitems | N. |  N. |
| products | N. |  N. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| classicDevServices | Sì | Sì |
| updateCommunicationPreference | N. |  N. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| agreements | N. |  N. |
| offertypes | N. |  N. |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| mediaservices | Sì | Sì |
| mediaservices/accountFilters | N. |  N. |
| mediaservices/assets | N. |  N. |
| mediaservices/assets/assetFilters | N. |  N. |
| mediaservices/contentKeyPolicies | N. |  N. |
| mediaservices/eventGridFilters | N. |  N. |
| mediaservices/liveEventOperations | N. |  N. |
| mediaservices/liveEvents | Sì | Sì |
| mediaservices/liveEvents/liveOutputs | N. |  N. |
| mediaservices/liveOutputOperations | N. |  N. |
| mediaservices/streamingEndpointOperations | N. |  N. |
| mediaservices/streamingEndpoints | Sì | Sì |
| mediaservices/streamingLocators | N. |  N. |
| mediaservices/streamingPolicies | N. |  N. |
| mediaservices/transforms | N. |  N. |
| mediaservices/transforms/jobs | N. |  N. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| projects | Sì | Sì |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| applicationGateways | Sì | N. |
| applicationSecurityGroups | Sì | Sì |
| azureFirewallFqdnTags | N. |  N. |
| azureFirewalls | Sì | N. |
| bgpServiceCommunities | N. |  N. |
| connessioni | Sì | Sì |
| ddosCustomPolicies | Sì | Sì |
| ddosProtectionPlans | Sì | Sì |
| dnsOperationStatuses | N. |  N. |
| dnszones | Sì | Sì |
| dnszones/A | N. |  N. |
| dnszones/AAAA | N. |  N. |
| dnszones/all | N. |  N. |
| dnszones/CAA | N. |  N. |
| dnszones/CNAME | N. |  N. |
| dnszones/MX | N. |  N. |
| dnszones/NS | N. |  N. |
| dnszones/PTR | N. |  N. |
| dnszones/recordsets | N. |  N. |
| dnszones/SOA | N. |  N. |
| dnszones/SRV | N. |  N. |
| dnszones/TXT | N. |  N. |
| expressRouteCircuits | Sì  | N. |
| expressRouteServiceProviders | N. |  N. |
| frontdoor | Sì | Sì |
| frontdoorWebApplicationFirewallPolicies | Sì | Sì |
| getDnsResourceReference | N. |  N. |
| interfaceEndpoints | Sì | Sì |
| internalNotify | N. |  N. |
| loadBalancers | Sì | N. |
| localNetworkGateways | Sì | Sì |
| natGateways | Sì | Sì |
| networkIntentPolicies | Sì | Sì |
| networkInterfaces | Sì | Sì |
| networkProfiles | Sì | Sì |
| networkSecurityGroups | Sì | Sì |
| networkWatchers | Sì | N. |
| networkWatchers/connectionMonitors | Sì | N. |
| networkWatchers/lenses | Sì | N. |
| networkWatchers/pingMeshes | Sì | N. |
| privateLinkServices | Sì | Sì |
| publicIPAddresses | Sì | Sì |
| publicIPPrefixes | Sì | Sì |
| routeFilters | Sì | Sì |
| routeTables | Sì | Sì |
| serviceEndpointPolicies | Sì | Sì |
| trafficManagerGeographicHierarchies | N. |  N. |
| trafficmanagerprofiles | Sì | Sì |
| trafficmanagerprofiles/heatMaps | N. |  N. |
| virtualHubs | Sì | Sì |
| virtualNetworkGateways | Sì | N. |
| virtualNetworks | Sì | Sì |
| virtualNetworks/subnets | N. |  N. |
| virtualNetworkTaps | Sì | Sì |
| virtualWans | Sì | Sì |
| vpnGateways | Sì | N. |
| vpnSites | Sì | Sì |
| webApplicationFirewallPolicies | Sì | Sì |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Sì | N. |
| namespaces/notificationHubs | Sì | N. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| dispositivi | N. |  N. |
| linkTargets | N. |  N. |
| storageInsightConfigs | N. |  N. |
| aree di lavoro | Sì | Sì |
| workspaces/dataSources | N. |  N. |
| workspaces/linkedServices | N. |  N. |
| workspaces/query | N. |  N. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| managementassociations | N. |  N. |
| managementconfigurations | Sì | Sì |
| solutions | Sì | Sì |
| visualizzazioni | Sì | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| policyEvents | N. |  N. |
| policyStates | N. |  N. |
| policyTrackedResources | N. |  N. |
| remediations | N. |  N. |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| consoles | N. |  N. |
| dashboards | Sì | Sì |
| userSettings | N. |  N. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| workspaceCollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| capacities | Sì | Sì |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| backupProtectedItems | N. |  N. |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Sì | Sì |
| namespaces/authorizationrules | N. |  N. |
| namespaces/hybridconnections | N. |  N. |
| namespaces/hybridconnections/authorizationrules | N. |  N. |
| namespaces/wcfrelays | N. |  N. |
| namespaces/wcfrelays/authorizationrules | N. |  N. |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| risorse | N. |  N. |
| subscriptionsStatus | N. |  N. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| availabilityStatuses | N. |  N. |
| childAvailabilityStatuses | N. |  N. |
| childResources | N. |  N. |
| eventi | N. |  N. |
| impactedResources | N. |  N. |
| notifiche | N. |  N. |

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| distribuzioni | N. |  N. |
| deployments/operations | N. |  N. |
| links | N. |  N. |
| notifyResourceJobs | N. |  N. |
| provider | N. |  N. |
| resourceGroups | N. |  N. |
| risorse | N. |  N. |
| sottoscrizioni | N. |  N. |
| subscriptions/providers | N. |  N. |
| subscriptions/resourceGroups | N. |  N. |
| subscriptions/resourcegroups/resources | N. |  N. |
| subscriptions/resources | N. |  N. |
| subscriptions/tagnames | N. |  N. |
| subscriptions/tagNames/tagValues | N. |  N. |
| tenants | N. |  N. |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| applicazioni | Sì | Sì |
| saasresources | N. |  N. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| flussi | Sì | Sì |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | N. |  N. |
| searchServices | Sì | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | N. |  N. |
| avvisi | N. |  N. |
| allowedConnections | N. |  N. |
| appliances | N. |  N. |
| applicationWhitelistings | N. |  N. |
| AutoProvisioningSettings | N. |  N. |
| Compliances | N. |  N. |
| dataCollectionAgents | N. |  N. |
| discoveredSecuritySolutions | N. |  N. |
| externalSecuritySolutions | N. |  N. |
| InformationProtectionPolicies | N. |  N. |
| jitNetworkAccessPolicies | N. |  N. |
| monitoring | N. |  N. |
| monitoring/antimalware | N. |  N. |
| monitoring/baseline | N. |  N. |
| monitoring/patch | N. |  N. |
| criteri | N. |  N. |
| pricings | N. |  N. |
| securityContacts | N. |  N. |
| securitySolutions | N. |  N. |
| securitySolutionsReferenceData | N. |  N. |
| securityStatus | N. |  N. |
| securityStatus/endpoints | N. |  N. |
| securityStatus/subnets | N. |  N. |
| securityStatus/virtualMachines | N. |  N. |
| securityStatuses | N. |  N. |
| securityStatusesSummaries | N. |  N. |
| impostazioni | N. |  N. |
| attività | N. |  N. |
| topologies | N. |  N. |
| workspaceSettings | N. |  N. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | N. |  N. |
| diagnosticSettingsCategories | N. |  N. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| spazi dei nomi | Sì | N. |
| namespaces/authorizationrules | N. |  N. |
| namespaces/disasterrecoveryconfigs | N. |  N. |
| namespaces/eventgridfilters | N. |  N. |
| namespaces/queues | N. |  N. |
| namespaces/queues/authorizationrules | N. |  N. |
| namespaces/topics | N. |  N. |
| namespaces/topics/authorizationrules | N. |  N. |
| namespaces/topics/subscriptions | N. |  N. |
| namespaces/topics/subscriptions/rules | N. |  N. |
| premiumMessagingRegions | N. |  N. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| clusters | Sì | Sì |
| clusters/applications | N. |  N. |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| applicazioni | Sì | Sì |
| gateways | Sì | Sì |
| Reti | Sì | Sì |
| segreti | Sì | Sì |
| volumi | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| SignalR | Sì | Sì |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Sì | Sì |
| appliances | Sì | Sì |
| applicationDefinitions | Sì | Sì |
| applicazioni | Sì | Sì |
| jitRequests | Sì | Sì |

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| managedInstances | Sì | Sì |
| managedInstances/databases | Sì (vedere la nota seguente) | Sì |
| managedInstances/databases/backupShortTermRetentionPolicies | N. | N. |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | N. | N. |
| managedInstances/databases/vulnerabilityAssessments | N. | N. |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | N. | N. |
| managedInstances/encryptionProtector | N. | N. |
| managedInstances/keys | N. | N. |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | N. | N. |
| managedInstances/vulnerabilityAssessments | N. | N. |
| server | Sì | Sì |
| servers/administrators | N. |  N. |
| servers/communicationLinks | N. |  N. |
| servers/databases | Sì (vedere la nota seguente) | Sì |
| servers/encryptionProtector | N. |  N. |
| servers/firewallRules | N. |  N. |
| servers/keys | N. |  N. |
| servers/restorableDroppedDatabases | N. |  N. |
| servers/serviceobjectives | N. |  N. |
| servers/tdeCertificates | N. |  N. |

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Sì | Sì |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | N. |  N. |
| SqlVirtualMachines | Sì | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| storageAccounts | Sì | Sì |
| storageAccounts/blobServices | N. |  N. |
| storageAccounts/fileServices | N. |  N. |
| storageAccounts/queueServices | N. |  N. |
| storageAccounts/services | N. |  N. |
| storageAccounts/tableServices | N. |  N. |
| usages | N. |  N. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| storageSyncServices | Sì | Sì |
| storageSyncServices/registeredServers | N. |  N. |
| storageSyncServices/syncGroups | N. |  N. |
| storageSyncServices/syncGroups/cloudEndpoints | N. |  N. |
| storageSyncServices/syncGroups/serverEndpoints | N. |  N. |
| storageSyncServices/workflows | N. |  N. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| managers | Sì | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| streamingjobs | Sì (vedere la nota seguente) | Sì |
| streamingjobs/diagnosticSettings | N. |  N. |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| CreateSubscription | N. |  N. |
| SubscriptionDefinitions | N. |  N. |
| SubscriptionOperations | N. |  N. |

## <a name="microsoftsupport"></a>microsoft.support
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| supporttickets | N. |  N. |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| providerRegistrations | Sì | Sì |
| risorse | Sì | Sì |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| environments | Sì | N. |
| environments/accessPolicies | N. |  N. |
| environments/eventsources | Sì | N. |
| environments/referenceDataSets | Sì | N. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | N. |  N. |
| apiManagementAccounts/apiAcls | N. |  N. |
| apiManagementAccounts/apis | N. |  N. |
| apiManagementAccounts/apis/apiAcls | N. |  N. |
| apiManagementAccounts/apis/connectionAcls | N. |  N. |
| apiManagementAccounts/apis/connections | N. |  N. |
| apiManagementAccounts/apis/connections/connectionAcls | N. |  N. |
| apiManagementAccounts/apis/localizedDefinitions | N. |  N. |
| apiManagementAccounts/connectionAcls | N. |  N. |
| apiManagementAccounts/connections | N. |  N. |
| billingMeters | N. |  N. |
| certificati | Sì | Sì |
| connectionGateways | Sì | Sì |
| connessioni | Sì | Sì |
| customApis | Sì | Sì |
| deletedSites | N. |  N. |
| funzioni | N. |  N. |
| hostingEnvironments | Sì | N. |
| hostingEnvironments/multiRolePools | N. |  N. |
| hostingEnvironments/multiRolePools/instances | N. |  N. |
| hostingEnvironments/workerPools | N. |  N. |
| hostingEnvironments/workerPools/instances | N. |  N. |
| publishingUsers | N. |  N. |
| raccomandazioni | N. |  N. |
| resourceHealthMetadata | N. |  N. |
| runtimes | N. |  N. |
| serverFarms | Sì | Sì |
| serverFarms/workers | N. |  N. |
| siti | Sì | Sì |
| sites/domainOwnershipIdentifiers | N. |  N. |
| sites/hostNameBindings | N. |  N. |
| sites/instances | N. |  N. |
| sites/instances/extensions | N. |  N. |
| sites/premieraddons | Sì | Sì |
| sites/recommendations | N. |  N. |
| sites/resourceHealthMetadata | N. |  N. |
| sites/slots | Sì | Sì |
| sites/slots/hostNameBindings | N. |  N. |
| sites/slots/instances | N. |  N. |
| sites/slots/instances/extensions | N. |  N. |
| sourceControls | N. |  N. |
| validate | N. |  N. |
| verifyHostingEnvironmentVnet | N. |  N. |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| diagnosticSettings | N. |  N. |
| diagnosticSettingsCategories | N. |  N. |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| DeviceServices | Sì | Sì |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo di risorsa | Tag supportati | Applicare i tag nei report dei costi |
| ------------- | ----------- | ----------- |
| components | N. |  N. |
| componentsSummary | N. |  N. |
| monitorInstances | N. |  N. |
| monitorInstancesSummary | N. |  N. |
| monitors | N. |  N. |
| notificationSettings | N. |  N. |

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
