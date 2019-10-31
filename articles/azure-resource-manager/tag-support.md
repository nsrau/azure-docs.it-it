---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 6b64eb955476a8ab5307b7d508484c290fd7660a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162165"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](resource-group-using-tags.md). La colonna con etichetta **supporta i tag** indica se il tipo di risorsa ha una proprietà per il tag. La colonna etichetta **nel report dei costi** indica se il tipo di risorsa passa il tag al report dei costi.

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestazione](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. Billing](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. consumer](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. databricks](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomica](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. Power bi](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DomainServices | SÌ | SÌ |
> | DomainServices/oucontainer | No | No |
> | DomainServices/ReplicaSets | SÌ | SÌ |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | agents | No | No |
> | anonymousapiusers | No | No |
> | configurazione | No | No |
> | log | No | No |
> | reports | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurazioni | No | No |
> | generateRecommendations | No | No |
> | metadata | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | actionRules | SÌ | SÌ |
> | alerts | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | Feedback | No | No |
> | smartDetectorAlertRules | SÌ | SÌ |
> | smartDetectorRuntimeEnvironments | No | No |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | servizio | SÌ | SÌ |
> | validateServiceName | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | SÌ | SÌ |
> | configurationStores/eventGridFilters | No | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Spring | SÌ | SÌ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | attestationProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No | No |
> | dataalias | No | No |
> | denyAssignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | locks | No | No |
> | autorizzazioni | No | No |
> | policyAssignments | No | No |
> | policyDefinitions | No | No |
> | policySetDefinitions | No | No |
> | providerOperations | No | No |
> | roleAssignments | No | No |
> | roleDefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | automationAccounts | SÌ | SÌ |
> | automationAccounts/configurations | SÌ | SÌ |
> | automationAccounts/jobs | No | No |
> | automationAccounts/runbooks | SÌ | SÌ |
> | automationAccounts/softwareUpdateConfigurations | No | No |
> | automationAccounts/webhooks | No | No |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | SÌ | SÌ |
> | configurationStores/eventGridFilters | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | environments | No | No |
> | environments/accounts | No | No |
> | environments/accounts/namespaces | No | No |
> | environments/accounts/namespaces/configurations | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | SÌ | No |
> | b2ctenants | No | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | SÌ | SÌ |
> | postgresInstances | SÌ | SÌ |
> | sqlBigDataClusters | SÌ | SÌ |
> | SQLInstances | SÌ | SÌ |
> | sqlServerRegistrations | SÌ | SÌ |
> | sqlServerRegistrations/SQLServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | registrations | SÌ | SÌ |
> | registrations/customerSubscriptions | No | No |
> | registrations/products | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | batchAccounts | SÌ | SÌ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccounts/contratti | No | No |
> | billingAccounts/billingPermissions | No | No |
> | billingAccounts/billingProfiles | No | No |
> | billingAccounts/billingProfiles/billingPermissions | No | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/clienti | No | No |
> | billingAccounts/billingProfiles/invoices | No | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No | No |
> | billingAccounts/billingProfiles/invoiceSections | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/trasferimenti | No | No |
> | billingAccounts/BillingProfiles/patchOperations | No | No |
> | billingAccounts/billingProfiles/paymentMethods | No | No |
> | billingAccounts/billingProfiles/policies | No | No |
> | billingAccounts/billingProfiles/pricesheet | No | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No | No |
> | billingAccounts/billingProfiles/products | No | No |
> | billingAccounts/billingProfiles/transactions | No | No |
> | billingAccounts/billingRoleAssignments | No | No |
> | billingAccounts/billingRoleDefinitions | No | No |
> | billingAccounts/billingSubscriptions | No | No |
> | billingAccounts/createBillingRoleAssignment | No | No |
> | billingAccounts/createInvoiceSectionOperations | No | No |
> | billingAccounts/clienti | No | No |
> | billingAccounts/Customers/billingPermissions | No | No |
> | billingAccounts/Customers/billingSubscriptions | No | No |
> | billingAccounts/Customers/initiateTransfer | No | No |
> | billingAccounts/clienti/criteri | No | No |
> | billingAccounts/clienti/prodotti | No | No |
> | billingAccounts/clienti/transazioni | No | No |
> | billingAccounts/clienti/trasferimenti | No | No |
> | billingAccounts/departments | No | No |
> | billingAccounts/enrollmentAccounts | No | No |
> | billingAccounts/invoices | No | No |
> | billingAccounts/invoiceSections | No | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No | No |
> | billingAccounts/invoiceSections/eleva | No | No |
> | billingAccounts/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/invoiceSections/patchOperations | No | No |
> | billingAccounts/invoiceSections/productMoveOperations | No | No |
> | billingAccounts/invoiceSections/products | No | No |
> | billingAccounts/invoiceSections/Products/Transfer | No | No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No | No |
> | billingAccounts/invoiceSections/Transactions | No | No |
> | billingAccounts/invoiceSections/transfers | No | No |
> | billingAccounts/lineOfCredit | No | No |
> | billingAccounts/patchOperations | No | No |
> | billingAccounts/paymentMethods | No | No |
> | billingAccounts/products | No | No |
> | billingAccounts/transactions | No | No |
> | billingPeriods | No | No |
> | billingPermissions | No | No |
> | billingProperty | No | No |
> | billingRoleAssignments | No | No |
> | billingRoleDefinitions | No | No |
> | createBillingRoleAssignment | No | No |
> | departments | No | No |
> | enrollmentAccounts | No | No |
> | invoices | No | No |
> | transfers | No | No |
> | transfers/acceptTransfer | No | No |
> | transfers/declineTransfer | No | No |
> | transfers/operationStatus | No | No |
> | trasferimenti/validateTransfer | No | No |
> | validateAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | mapApis | SÌ | SÌ |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | SÌ | SÌ |
> | cordaMembers | SÌ | SÌ |
> | visualizzazioni | SÌ | SÌ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments/assignmentOperations | No | No |
> | blueprintAssignments/operations | No | No |
> | blueprints | No | No |
> | blueprints/artifacts | No | No |
> | blueprints/versions | No | No |
> | blueprints/versions/artifacts | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | botServices | SÌ | SÌ |
> | botServices/channels | No | No |
> | botServices/connections | No | No |
> | lingue | No | No |
> | Modelli | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Redis | SÌ | SÌ |
> | RedisConfigDefinition | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No | No |
> | calculateExchange | No | No |
> | calculatePrice | No | No |
> | calculatePurchasePrice | No | No |
> | catalogs | No | No |
> | commercialReservationOrders | No | No |
> | exchange | No | No |
> | placePurchaseOrder | No | No |
> | reservationOrders | No | No |
> | reservationOrders/calculateRefund | No | No |
> | reservationOrders/merge | No | No |
> | reservationOrders/reservations | No | No |
> | reservationOrders/reservations/revisions | No | No |
> | reservationOrders/return | No | No |
> | reservationOrders/split | No | No |
> | reservationOrders/swap | No | No |
> | reservations | No | No |
> | resources | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | SÌ | SÌ |
> | edgenodes | No | No |
> | Profili | SÌ | SÌ |
> | profiles/endpoints | SÌ | SÌ |
> | profiles/endpoints/customdomains | No | No |
> | profiles/endpoints/origins | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | certificateOrders | SÌ | SÌ |
> | certificateOrders/certificates | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | domainNames | No | No |
> | domainNames/capabilities | No | No |
> | domainNames/internalLoadBalancers | No | No |
> | domainNames/serviceCertificates | No | No |
> | domainNames/slots | No | No |
> | domainNames/slots/roles | No | No |
> | domainNames/slot/ruoli/metricDefinitions | No | No |
> | domainNames/slot/ruoli/metriche | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | resourceTypes | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines/diagnosticSettings | No | No |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachines/metrics | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections/peerings | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks/virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | dischi | No | No |
> | images | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/metricDefinitions | No | No |
> | storageAccounts/metriche | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/diagnosticSettings | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/Servizi/metriche | No | No |
> | storageAccounts/tableServices | No | No |
> | storageAccounts/vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilitySets | SÌ | SÌ |
> | diskEncryptionSets | SÌ | SÌ |
> | dischi | SÌ | SÌ |
> | galleries | SÌ | SÌ |
> | raccolte/applicazioni | No | No |
> | raccolte/applicazioni/versioni | No | No |
> | galleries/images | No | No |
> | galleries/images/versions | No | No |
> | Gruppi host | SÌ | SÌ |
> | Gruppi host/host | SÌ | SÌ |
> | images | SÌ | SÌ |
> | proximityPlacementGroups | SÌ | SÌ |
> | restorePointCollections | SÌ | SÌ |
> | restorePointCollections/restorePoints | No | No |
> | sharedVMExtensions | SÌ | SÌ |
> | sharedVMExtensions/versioni | No | No |
> | sharedVMImages | SÌ | SÌ |
> | sharedVMImages/versions | No | No |
> | snapshots | SÌ | SÌ |
> | virtualMachines | SÌ | SÌ |
> | virtualMachines/extensions | SÌ | SÌ |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachineScaleSets | SÌ | SÌ |
> | virtualMachineScaleSets/extensions | No | No |
> | virtualMachineScaleSets/networkInterfaces | No | No |
> | virtualMachineScaleSets/publicIPAddresses | No | No |
> | virtualMachineScaleSets/virtualMachines | No | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | No | No |
> | Saldi | No | No |
> | Budget | No | No |
> | Charges | No | No |
> | CostTags | No | No |
> | credits | No | No |
> | eventi | No | No |
> | Previsioni | No | No |
> | lots | No | No |
> | Marketplace | No | No |
> | Pricesheets | No | No |
> | products | No | No |
> | ReservationDetails | No | No |
> | ReservationRecommendations | No | No |
> | ReservationSummaries | No | No |
> | ReservationTransactions | No | No |
> | Tag | No | No |
> | tenants | No | No |
> | Termini | No | No |
> | UsageDetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerGroups | SÌ | SÌ |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | registries | SÌ | SÌ |
> | registries/builds | No | No |
> | registries/builds/cancel | No | No |
> | registries/builds/getLogLink | No | No |
> | registries/buildTasks | SÌ | SÌ |
> | registries/buildTasks/steps | No | No |
> | registries/eventGridFilters | No | No |
> | registri/generateCredentials | No | No |
> | registries/getBuildSourceUploadUrl | No | No |
> | registries/GetCredentials | No | No |
> | registries/importImage | No | No |
> | registries/queueBuild | No | No |
> | registries/regenerateCredential | No | No |
> | registries/regenerateCredentials | No | No |
> | registries/replications | SÌ | SÌ |
> | registries/runs | No | No |
> | registries/runs/cancel | No | No |
> | registries/scheduleRun | No | No |
> | registri/scopeMaps | No | No |
> | registries/tasks | SÌ | SÌ |
> | registri/token | No | No |
> | registries/updatePolicies | No | No |
> | registries/webhooks | SÌ | SÌ |
> | registries/webhooks/getCallbackConfig | No | No |
> | registries/webhooks/ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerServices | SÌ | SÌ |
> | managedClusters | SÌ | SÌ |
> | openShiftManagedClusters | SÌ | SÌ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Avvisi | No | No |
> | BillingAccounts | No | No |
> | Budget | No | No |
> | CloudConnectors | No | No |
> | Connettori | SÌ | SÌ |
> | Departments | No | No |
> | Dimensioni | No | No |
> | EnrollmentAccounts | No | No |
> | Esportazioni | No | No |
> | ExternalBillingAccounts | No | No |
> | ExternalBillingAccounts/avvisi | No | No |
> | ExternalBillingAccounts/dimensioni | No | No |
> | ExternalBillingAccounts/previsione | No | No |
> | ExternalBillingAccounts/query | No | No |
> | ExternalSubscriptions | No | No |
> | ExternalSubscriptions/avvisi | No | No |
> | ExternalSubscriptions/dimensioni | No | No |
> | ExternalSubscriptions/previsione | No | No |
> | ExternalSubscriptions/query | No | No |
> | Previsione | No | No |
> | Query | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | Report | No | No |
> | Impostazioni | No | No |
> | showbackRules | No | No |
> | Visualizzazioni | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | requests | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | associazioni | No | No |
> | resourceProviders | SÌ | SÌ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | jobs | SÌ | SÌ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | SÌ | SÌ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | SÌ | No |
> | workspaces/virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | catalogs | SÌ | SÌ |
> | datacatalogs | SÌ | SÌ |
> | datacatalogs/origini dati | No | No |
> | sottocataloghi/origini dati/analisi | No | No |
> | datacatalogs/DataSources/scansioni/set di dati | No | No |
> | datacatalogs/origini dati/analisi/trigger | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dataFactories | SÌ | No |
> | dataFactories/diagnosticSettings | No | No |
> | dataFactories/metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | factories | SÌ | No |
> | factories/integrationRuntimes | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts/dataLakeStoreAccounts | No | No |
> | accounts/storageAccounts | No | No |
> | accounts/storageAccounts/containers | No | No |
> | account/transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts/eventGridFilters | No | No |
> | accounts/firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | services/projects | No | No |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | account/condivisioni | No | No |
> | account/condivisioni/set di impostazioni | No | No |
> | account/condivisioni/inviti | No | No |
> | Accounts/shares/providersharesubscriptions | No | No |
> | Accounts/shares/synchronizationSettings | No | No |
> | account/sharesubscriptions | No | No |
> | account/sharesubscriptions/consumerSourceDataSets | No | No |
> | account/sharesubscriptions/datasetmappings | No | No |
> | account/sharesubscriptions/trigger | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |
> | servers/advisors | No | No |
> | Server/privateEndpointConnectionProxies | No | No |
> | Server/privateEndpointConnections | No | No |
> | Server/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |
> | servers/advisors | No | No |
> | Server/privateEndpointConnectionProxies | No | No |
> | Server/privateEndpointConnections | No | No |
> | Server/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | serverGroups | SÌ | SÌ |
> | server | SÌ | SÌ |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | Server/privateEndpointConnectionProxies | No | No |
> | Server/privateEndpointConnections | No | No |
> | Server/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |
> | serversv2 | SÌ | SÌ |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | artifactSources | SÌ | SÌ |
> | rollouts | SÌ | SÌ |
> | serviceTopologies | SÌ | SÌ |
> | serviceTopologies/servizi | SÌ | SÌ |
> | serviceTopologies/Services/serviceUnits | SÌ | SÌ |
> | steps | SÌ | SÌ |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationgroups | SÌ | SÌ |
> | applicationgroups/applicazioni | No | No |
> | applicationgroups/desktop | No | No |
> | applicationgroups/startmenuitems | No | No |
> | hostpools | SÌ | SÌ |
> | hostpools/sessionhosts | No | No |
> | hostpools/sessionhosts/usersessions | No | No |
> | hostpools/usersessions | No | No |
> | aree di lavoro | SÌ | SÌ |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | ElasticPools | SÌ | SÌ |
> | ElasticPools/IotHubTenants | SÌ | SÌ |
> | Hub IoT | SÌ | SÌ |
> | IotHubs/eventGridFilters | No | No |
> | ProvisioningServices | SÌ | SÌ |
> | usages | No | No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Pipeline | SÌ | SÌ |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | controllers | SÌ | SÌ |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | labcenters | SÌ | SÌ |
> | labs | SÌ | SÌ |
> | Lab/ambienti | SÌ | SÌ |
> | labs/serviceRunners | SÌ | SÌ |
> | labs/virtualMachines | SÌ | SÌ |
> | schedules | SÌ | SÌ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | SÌ | SÌ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | SÌ | SÌ |
> | domains/domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects/clouddeployments | No | No |
> | lcsprojects/connectors | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | SÌ | SÌ |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | SÌ | SÌ |
> | domains/topics | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | topics | SÌ | SÌ |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | spazi dei nomi | SÌ | SÌ |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventhubs | No | No |
> | namespaces/eventhubs/authorizationrules | No | No |
> | namespaces/eventhubs/consumergroups | No | No |
> | spazi dei nomi/networkrulesets | No | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | elastico | No | No |
> | provider | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | enroll | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | myareas/areas | No | No |
> | myareas/areas/areas | No | No |
> | myareas/areas/areas/galleryitems | No | No |
> | myareas/areas/galleryitems | No | No |
> | myareas/galleryitems | No | No |
> | register | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | SÌ | SÌ |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hanaInstances | SÌ | SÌ |
> | sapMonitors | SÌ | SÌ |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | SÌ | SÌ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | clusters/applications | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | SÌ | SÌ |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | macchine | SÌ | SÌ |
> | computer/estensioni | SÌ | SÌ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | datamanager | SÌ | SÌ |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | components | SÌ | SÌ |
> | networkScopes | SÌ | SÌ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | jobs | SÌ | SÌ |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | SÌ | SÌ |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Grafo | SÌ | SÌ |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools | SÌ | SÌ |
> | insiemi di credenziali | SÌ | SÌ |
> | vaults/accessPolicies | No | No |
> | insiemi di credenziali/eventGridFilters | No | No |
> | vaults/secrets | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | cluster/attacheddatabaseconfigurations | No | No |
> | clusters/databases | No | No |
> | clusters/databases/dataconnections | No | No |
> | clusters/databases/eventhubconnections | No | No |
> | cluster/sharedidentities | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | labaccounts | SÌ | SÌ |
> | utenti | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | SÌ | SÌ |
> | integrationAccounts | SÌ | SÌ |
> | integrationServiceEnvironments | SÌ | SÌ |
> | integrationServiceEnvironments/managedApis | SÌ | SÌ |
> | isolatedEnvironments | SÌ | SÌ |
> | flussi di lavoro | SÌ | SÌ |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | SÌ | SÌ |
> | webServices | SÌ | SÌ |
> | Aree di lavoro | SÌ | SÌ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | SÌ | SÌ |
> | workspaces/computes | No | No |
> | aree di lavoro/eventGridFilters | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Identities | No | No |
> | userAssignedIdentities | SÌ | SÌ |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrationAssignments | No | No |
> | registrationDefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | getEntities | No | No |
> | managementGroups | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts/eventGridFilters | No | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | offers | No | No |
> | offerTypes | No | No |
> | offerTypes/publishers | No | No |
> | offerTypes/publishers/offers | No | No |
> | offerTypes/publishers/offers/plans | No | No |
> | offerTypes/publishers/offers/plans/agreements | No | No |
> | offerTypes/publishers/offers/plans/configs | No | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No | No |
> | privategalleryitems | No | No |
> | products | No | No |
> | pubblicazione | No | No |
> | editori/offerte | No | No |
> | editori/offerte/modifiche | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicDevServices | SÌ | SÌ |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | agreements | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | mediaservices | SÌ | SÌ |
> | mediaservices/accountFilters | No | No |
> | mediaservices/assets | No | No |
> | mediaservices/assets/assetFilters | No | No |
> | mediaservices/contentKeyPolicies | No | No |
> | mediaservices/eventGridFilters | No | No |
> | mediaservices/liveEventOperations | No | No |
> | mediaservices/liveEvents | SÌ | SÌ |
> | mediaservices/liveEvents/liveOutputs | No | No |
> | mediaservices/liveOutputOperations | No | No |
> | MediaServices/mediaGraphs | No | No |
> | mediaservices/streamingEndpointOperations | No | No |
> | mediaservices/streamingEndpoints | SÌ | SÌ |
> | mediaservices/streamingLocators | No | No |
> | mediaservices/streamingPolicies | No | No |
> | mediaservices/transforms | No | No |
> | mediaservices/transforms/jobs | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appClusters | SÌ | SÌ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | SÌ | SÌ |
> | migrateprojects | SÌ | SÌ |
> | projects | SÌ | SÌ |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | SÌ | SÌ |
> | objectUnderstandingAccounts | SÌ | SÌ |
> | remoteRenderingAccounts | SÌ | SÌ |
> | spatialAnchorsAccounts | SÌ | SÌ |
> | surfaceReconstructionAccounts | SÌ | SÌ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | SÌ | SÌ |
> | netAppAccounts/backupPolicies | SÌ | SÌ |
> | netAppAccounts/capacityPools | SÌ | SÌ |
> | netAppAccounts/capacityPools/volumi | SÌ | SÌ |
> | netAppAccounts/capacityPools/volumi/backup | No | No |
> | netAppAccounts/capacityPools/Volumes/mountTargets | SÌ | SÌ |
> | netAppAccounts/capacityPools/volumi/snapshot | SÌ | SÌ |
> | netAppAccounts/insiemi di credenziali | No | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationGateways | SÌ | SÌ |
> | applicationGatewayWebApplicationFirewallPolicies | SÌ | SÌ |
> | applicationSecurityGroups | SÌ | SÌ |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | SÌ | No |
> | bastionHosts | SÌ | SÌ |
> | bgpServiceCommunities | No | No |
> | connections | SÌ | SÌ |
> | ddosCustomPolicies | SÌ | SÌ |
> | ddosProtectionPlans | SÌ | SÌ |
> | dnsOperationStatuses | No | No |
> | dnszones | SÌ | SÌ |
> | dnszones/A | No | No |
> | dnszones/AAAA | No | No |
> | dnszones/all | No | No |
> | dnszones/CAA | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/NS | No | No |
> | dnszones/PTR | No | No |
> | dnszones/recordsets | No | No |
> | dnszones/SOA | No | No |
> | dnszones/SRV | No | No |
> | dnszones/TXT | No | No |
> | expressRouteCircuits | SÌ | SÌ |
> | expressRouteCrossConnections | SÌ | SÌ |
> | expressRouteGateways | SÌ | SÌ |
> | expressRoutePorts | SÌ | SÌ |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | SÌ | SÌ |
> | frontdoors | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) | SÌ |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) | SÌ |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | loadBalancers | SÌ | No |
> | localNetworkGateways | SÌ | SÌ |
> | natGateways | SÌ | SÌ |
> | networkIntentPolicies | SÌ | SÌ |
> | networkInterfaces | SÌ | SÌ |
> | networkProfiles | SÌ | SÌ |
> | networkSecurityGroups | SÌ | SÌ |
> | networkWatchers | SÌ | No |
> | networkWatchers/connectionMonitors | SÌ | No |
> | networkWatchers/lenses | SÌ | No |
> | networkWatchers/pingMeshes | SÌ | No |
> | p2sVpnGateways | SÌ | SÌ |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | SÌ | SÌ |
> | privateDnsZones/A | No | No |
> | privateDnsZones/AAAA | No | No |
> | privateDnsZones/tutti | No | No |
> | privateDnsZones/CNAME | No | No |
> | privateDnsZones/MX | No | No |
> | privateDnsZones/PTR | No | No |
> | privateDnsZones/SOA | No | No |
> | privateDnsZones/SRV | No | No |
> | privateDnsZones/TXT | No | No |
> | privateDnsZones/virtualNetworkLinks | SÌ | SÌ |
> | privateEndpoints | SÌ | SÌ |
> | privateLinkServices | SÌ | SÌ |
> | publicIPAddresses | SÌ | SÌ |
> | publicIPPrefixes | SÌ | SÌ |
> | routeFilters | SÌ | SÌ |
> | routeTables | SÌ | SÌ |
> | serviceEndpointPolicies | SÌ | SÌ |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | SÌ | SÌ |
> | trafficmanagerprofiles/heatMaps | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | SÌ | SÌ |
> | virtualNetworkGateways | SÌ | SÌ |
> | virtualNetworks | SÌ | SÌ |
> | virtualNetworkTaps | SÌ | SÌ |
> | virtualWans | SÌ | SÌ |
> | vpnGateways | SÌ | No |
> | vpnSites | SÌ | SÌ |
> | webApplicationFirewallPolicies | SÌ | SÌ |

<a id="frontdoor" />

> [!NOTE]
> Per il servizio front-end di Azure, è possibile applicare tag quando si crea la risorsa, ma non è attualmente supportata l'aggiornamento o l'aggiunta di tag.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | No |
> | namespaces/notificationHubs | SÌ | No |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | osNamespaces | SÌ | SÌ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | HyperVSites | SÌ | SÌ |
> | ImportSites | SÌ | SÌ |
> | ServerSites | SÌ | SÌ |
> | VMwareSites | SÌ | SÌ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | devices | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | aree di lavoro | SÌ | SÌ |
> | workspaces/dataSources | No | No |
> | workspaces/linkedServices | No | No |
> | workspaces/query | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | SÌ | SÌ |
> | solutions | SÌ | SÌ |
> | Viste | SÌ | SÌ |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | peering | SÌ | SÌ |
> | peeringServiceProviders | No | No |
> | peeringServices | SÌ | SÌ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | consoles | No | No |
> | dashboards | SÌ | SÌ |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | SÌ | SÌ |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capacities | SÌ | SÌ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | insiemi di credenziali | SÌ | SÌ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | SÌ |
> | namespaces/authorizationrules | No | No |
> | namespaces/hybridconnections | No | No |
> | namespaces/hybridconnections/authorizationrules | No | No |
> | namespaces/wcfrelays | No | No |
> | namespaces/wcfrelays/authorizationrules | No | No |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | No | No |
> | Collezioni | SÌ | SÌ |
> | raccolte/applicazioni | No | No |
> | raccolte/SecurityPrincipals | No | No |
> | templateImages | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | query | SÌ | SÌ |
> | resourceChangeDetails | No | No |
> | resourceChanges | No | No |
> | resources | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No | No |
> | childAvailabilityStatuses | No | No |
> | childResources | No | No |
> | eventi | No | No |
> | impactedResources | No | No |
> | metadata | No | No |
> | Notifiche | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deployments | SÌ | No |
> | deployments/operations | No | No |
> | deploymentScripts | SÌ | SÌ |
> | deploymentScripts/log | No | No |
> | links | No | No |
> | notifyResourceJobs | No | No |
> | provider | No | No |
> | resourceGroups | SÌ | No |
> | resources | No | No |
> | subscriptions | No | No |
> | subscriptions/providers | No | No |
> | subscriptions/resourceGroups | No | No |
> | subscriptions/resourcegroups/resources | No | No |
> | subscriptions/resources | No | No |
> | subscriptions/tagnames | No | No |
> | subscriptions/tagNames/tagValues | No | No |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | saasresources | No | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | jobcollections | SÌ | SÌ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | SÌ | SÌ |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | valutazioni | No | No |
> | Automazioni | SÌ | SÌ |
> | AutoProvisioningSettings | No | No |
> | Compliances | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotSecuritySolutions | SÌ | SÌ |
> | iotSecuritySolutions/analyticsModels | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No | No |
> | jitNetworkAccessPolicies | No | No |
> | File networkdata | No | No |
> | playbookConfigurations | SÌ | SÌ |
> | criteri | No | No |
> | pricings | No | No |
> | regulatoryComplianceStandards | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityAssessments | No | No |
> | Scheda Impostazioni | No | No |
> | sottovalutazioni | No | No |
> | attività | No | No |
> | topologies | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aggregazioni | No | No |
> | alertRules | No | No |
> | alertRuleTemplates | No | No |
> | segnalibri | No | No |
> | cases | No | No |
> | DataConnector | No | No |
> | entities | No | No |
> | entityQueries | No | No |
> | officeConsents | No | No |
> | Scheda Impostazioni | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | No |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventgridfilters | No | No |
> | spazi dei nomi/networkrulesets | No | No |
> | namespaces/queues | No | No |
> | namespaces/queues/authorizationrules | No | No |
> | namespaces/topics | No | No |
> | namespaces/topics/authorizationrules | No | No |
> | namespaces/topics/subscriptions | No | No |
> | namespaces/topics/subscriptions/rules | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | clusters | SÌ | SÌ |
> | clusters/applications | No | No |
> | containerGroups | SÌ | SÌ |
> | containerGroupSets | SÌ | SÌ |
> | edgeclusters | SÌ | SÌ |
> | edgeclusters/applicazioni | No | No |
> | networks | SÌ | SÌ |
> | secretstores | SÌ | SÌ |
> | secretstores/certificati | No | No |
> | secretstores/segreti | No | No |
> | volumes | SÌ | SÌ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | containerGroups | SÌ | SÌ |
> | gateways | SÌ | SÌ |
> | networks | SÌ | SÌ |
> | chiavi private | SÌ | SÌ |
> | volumes | SÌ | SÌ |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations/resourceTypeRegistrations | No | No |
> | rollouts | SÌ | SÌ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SignalR | SÌ | SÌ |
> | SignalR/eventGridFilters | No | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | SÌ | SÌ |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | SÌ | SÌ |
> | applicazioni | SÌ | SÌ |
> | jitRequests | SÌ | SÌ |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managedInstances | SÌ | SÌ |
> | managedInstances/databases | Sì (vedere la [Nota sotto](#sqlnote)) | SÌ |
> | managedInstances/databases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No | No |
> | managedInstances/databases/vulnerabilityAssessments | No | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No | No |
> | managedInstances/encryptionProtector | No | No |
> | managedInstances/keys | No | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/vulnerabilityAssessments | No | No |
> | server | SÌ | SÌ |
> | servers/administrators | No | No |
> | servers/communicationLinks | No | No |
> | servers/databases | Sì (vedere la [Nota sotto](#sqlnote)) | SÌ |
> | servers/encryptionProtector | No | No |
> | servers/firewallRules | No | No |
> | servers/keys | No | No |
> | servers/restorableDroppedDatabases | No | No |
> | servers/serviceobjectives | No | No |
> | servers/tdeCertificates | No | No |
> | virtualClusters | No | No |

<a id="sqlnote" />

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | SÌ | SÌ |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | SÌ | SÌ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageAccounts | SÌ | SÌ |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/tableServices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | cache | SÌ | SÌ |
> | cache/storageTargets | No | No |
> | usageModels | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managers | SÌ | SÌ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sì (vedere la nota seguente) | SÌ |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Annulla | No | No |
> | CreateSubscription | No | No |
> | enable | No | No |
> | Rinominare | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | environments | SÌ | No |
> | environments/accessPolicies | No | No |
> | environments/eventsources | SÌ | No |
> | environments/referenceDataSets | SÌ | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | SÌ | SÌ |
> | dedicatedCloudServices | SÌ | SÌ |
> | virtualMachines | SÌ | SÌ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts/apiAcls | No | No |
> | apiManagementAccounts/apis | No | No |
> | apiManagementAccounts/apis/apiAcls | No | No |
> | apiManagementAccounts/apis/connectionAcls | No | No |
> | apiManagementAccounts/apis/connections | No | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No | No |
> | apiManagementAccounts/apis/localizedDefinitions | No | No |
> | apiManagementAccounts/connectionAcls | No | No |
> | apiManagementAccounts/connections | No | No |
> | billingMeters | No | No |
> | certificates | SÌ | SÌ |
> | connectionGateways | SÌ | SÌ |
> | connections | SÌ | SÌ |
> | customApis | SÌ | SÌ |
> | deletedSites | No | No |
> | functions | No | No |
> | hostingEnvironments | SÌ | SÌ |
> | hostingEnvironments/multiRolePools | No | No |
> | hostingEnvironments/workerPools | No | No |
> | publishingUsers | No | No |
> | raccomandazioni di film | No | No |
> | resourceHealthMetadata | No | No |
> | runtimes | No | No |
> | serverFarms | SÌ | SÌ |
> | serverFarms/eventGridFilters | No | No |
> | siti | SÌ | SÌ |
> | siti/configurazione  | No | No |
> | siti/eventGridFilters | No | No |
> | sites/hostNameBindings | No | No |
> | siti/file networkconfig | No | No |
> | sites/premieraddons | SÌ | SÌ |
> | sites/slots | SÌ | SÌ |
> | siti/slot/eventGridFilters | No | No |
> | sites/slots/hostNameBindings | No | No |
> | siti/slot/file networkconfig | No | No |
> | sourceControls | No | No |
> | validate | No | No |
> | verifyHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DeviceServices | SÌ | SÌ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | components | No | No |
> | componentsSummary | No | No |
> | monitorInstances | No | No |
> | monitorInstancesSummary | No | No |
> | monitors | No | No |
> | notificationSettings | No | No |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
