---
title: Supporto dei tag di Azure Resource Manager per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: tomfitz
ms.openlocfilehash: 8bcbd7b14de497cb6a8bb5cf12de4ec8c555b97b
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983895"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](resource-group-using-tags.md). La colonna con etichetta **supporta i tag** indica se il tipo di risorsa ha una proprietà per il tag. La colonna etichetta **nel report dei costi** indica se il tipo di risorsa passa il tag al report dei costi.

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestazione](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. consumer](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sì |Sì |
> | DomainServices/oucontainer | No |No |
> | DomainServices/ReplicaSets | Sì |Yes |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | supportProviders | No |No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | No |No |
> | addsservices | No |No |
> | agents | No |No |
> | anonymousapiusers | No |No |
> | configurazione | No |No |
> | logs | No |No |
> | report | No |No |
> | servicehealthmetrics | No |No |
> | services | No |No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurazioni | No |No |
> | generateRecommendations | No |No |
> | Metadati | No |No |
> | raccomandazioni | No |No |
> | suppressions | No |No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | actionRules | Sì |Yes |
> | avvisi | No |No |
> | alertsList | No |No |
> | alertsMetaData | No |No |
> | alertsSummary | No |No |
> | alertsSummaryList | No |No |
> | Feedback | No |No |
> | smartDetectorAlertRules | Sì |Sì |
> | smartDetectorRuntimeEnvironments | No |No |
> | smartGroups | No |No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | Sì |Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No |No |
> | servizio | Sì |Yes |
> | validateServiceName | No |No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sì |Sì |
> | configurationStores/eventGridFilters | No |No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Molla | Sì |Sì |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | attestationProviders | No |No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No |No |
> | dataalias | No |No |
> | denyAssignments | No |No |
> | elevateAccess | No |No |
> | locks | No |No |
> | autorizzazioni | No |No |
> | policyAssignments | No |No |
> | policyDefinitions | No |No |
> | policySetDefinitions | No |No |
> | providerOperations | No |No |
> | roleAssignments | No |No |
> | roleDefinitions | No |No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Sì |Sì |
> | automationAccounts/configurations | Sì |Sì |
> | automationAccounts/jobs | No |No |
> | automationAccounts/runbooks | Yes |Sì |
> | automationAccounts/softwareUpdateConfigurations | No |No |
> | automationAccounts/webhooks | No |No |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sì |Yes |
> | configurationStores/eventGridFilters | No |No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | environments | No |No |
> | environments/accounts | No |No |
> | environments/accounts/namespaces | No |No |
> | environments/accounts/namespaces/configurations | No |No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sì |No |
> | b2ctenants | No |No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Sì |Yes |
> | postgresInstances | Sì |Sì |
> | sqlBigDataClusters | Sì |Sì |
> | SQLInstances | Sì |Yes |
> | sqlServerRegistrations | Sì |Sì |
> | sqlServerRegistrations/SQLServers | No |No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | registrations | Yes |Sì |
> | registrations/customerSubscriptions | No |No |
> | registrations/products | No |No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sì |Sì |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No |No |
> | billingAccounts/contratti | No |No |
> | billingAccounts/billingPermissions | No |No |
> | billingAccounts/billingProfiles | No |No |
> | billingAccounts/billingProfiles/billingPermissions | No |No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |No |
> | billingAccounts/billingProfiles/clienti | No |No |
> | billingAccounts/billingProfiles/invoices | No |No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |No |
> | billingAccounts/billingProfiles/invoiceSections | No |No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No |No |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | No |No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No |No |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | No |No |
> | billingAccounts/billingProfiles/invoiceSections/trasferimenti | No |No |
> | billingAccounts/BillingProfiles/patchOperations | No |No |
> | billingAccounts/billingProfiles/paymentMethods | No |No |
> | billingAccounts/billingProfiles/policies | No |No |
> | billingAccounts/billingProfiles/pricesheet | No |No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |No |
> | billingAccounts/billingProfiles/products | No |No |
> | billingAccounts/billingProfiles/transactions | No |No |
> | billingAccounts/billingRoleAssignments | No |No |
> | billingAccounts/billingRoleDefinitions | No |No |
> | billingAccounts/billingSubscriptions | No |No |
> | billingAccounts/createBillingRoleAssignment | No |No |
> | billingAccounts/createInvoiceSectionOperations | No |No |
> | billingAccounts/clienti | No |No |
> | billingAccounts/Customers/billingSubscriptions | No |No |
> | billingAccounts/Customers/initiateTransfer | No |No |
> | billingAccounts/clienti/prodotti | No |No |
> | billingAccounts/clienti/transazioni | No |No |
> | billingAccounts/clienti/trasferimenti | No |No |
> | billingAccounts/departments | No |No |
> | billingAccounts/enrollmentAccounts | No |No |
> | billingAccounts/invoices | No |No |
> | billingAccounts/invoiceSections | No |No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |No |
> | billingAccounts/invoiceSections/eleva | No |No |
> | billingAccounts/invoiceSections/initiateTransfer | No |No |
> | billingAccounts/invoiceSections/patchOperations | No |No |
> | billingAccounts/invoiceSections/productMoveOperations | No |No |
> | billingAccounts/invoiceSections/products | No |No |
> | billingAccounts/invoiceSections/Products/Transfer | No |No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No |No |
> | billingAccounts/invoiceSections/Transactions | No |No |
> | billingAccounts/invoiceSections/transfers | No |No |
> | billingAccounts/lineOfCredit | No |No |
> | billingAccounts/patchOperations | No |No |
> | billingAccounts/paymentMethods | No |No |
> | billingAccounts/products | No |No |
> | billingAccounts/transactions | No |No |
> | billingPeriods | No |No |
> | BillingPermissions | No |No |
> | billingProperty | No |No |
> | BillingRoleAssignments | No |No |
> | BillingRoleDefinitions | No |No |
> | CreateBillingRoleAssignment | No |No |
> | departments | No |No |
> | enrollmentAccounts | No |No |
> | invoices | No |No |
> | transfers | No |No |
> | transfers/acceptTransfer | No |No |
> | transfers/declineTransfer | No |No |
> | transfers/operationStatus | No |No |
> | trasferimenti/validateTransfer | No |No |
> | validateAddress | No |No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | mapApis | Sì |Sì |
> | updateCommunicationPreference | No |No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | BizTalk | Sì |Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sì |Yes |
> | visualizzazioni | Sì |Sì |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No |No |
> | blueprintAssignments/assignmentOperations | No |No |
> | blueprintAssignments/operations | No |No |
> | blueprints | No |No |
> | blueprints/artifacts | No |No |
> | blueprints/versions | No |No |
> | blueprints/versions/artifacts | No |No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | botServices | Sì |Yes |
> | botServices/channels | No |No |
> | botServices/connections | No |No |
> | lingue | No |No |
> | modelli | No |No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Redis | Sì |Yes |
> | RedisConfigDefinition | No |No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No |No |
> | calculateExchange | No |No |
> | calculatePrice | No |No |
> | calculatePurchasePrice | No |No |
> | catalogs | No |No |
> | commercialReservationOrders | No |No |
> | exchange | No |No |
> | placePurchaseOrder | No |No |
> | reservationOrders | No |No |
> | reservationOrders/calculateRefund | No |No |
> | reservationOrders/merge | No |No |
> | reservationOrders/reservations | No |No |
> | reservationOrders/reservations/revisions | No |No |
> | reservationOrders/return | No |No |
> | reservationOrders/split | No |No |
> | reservationOrders/swap | No |No |
> | reservations | No |No |
> | risorse | No |No |
> | validateReservationOrder | No |No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |No |
> | CdnWebApplicationFirewallPolicies | Yes |Yes |
> | edgenodes | No |No |
> | profiles | Sì |Yes |
> | profiles/endpoints | Sì |Sì |
> | profiles/endpoints/customdomains | No |No |
> | profiles/endpoints/origins | No |No |
> | validateProbe | No |No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sì |Yes |
> | certificateOrders/certificates | No |No |
> | validateCertificateRegistrationInformation | No |No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No |No |
> | domainNames | Sì |Sì |
> | domainNames/capabilities | No |No |
> | domainNames/internalLoadBalancers | No |No |
> | domainNames/serviceCertificates | No |No |
> | domainNames/slots | No |No |
> | domainNames/slots/roles | No |No |
> | domainNames/slot/ruoli/metricDefinitions | No |No |
> | domainNames/slot/ruoli/metriche | No |No |
> | moveSubscriptionResources | No |No |
> | operatingSystemFamilies | No |No |
> | operatingSystems | No |No |
> | quotas | No |No |
> | resourceTypes | No |No |
> | validateSubscriptionMoveAvailability | No |No |
> | virtualMachines | Yes |Sì |
> | virtualMachines/diagnosticSettings | No |No |
> | virtualMachines/metricDefinitions | No |No |
> | virtualMachines/metrics | No |No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No |No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No |No |
> | expressRouteCrossConnections | No |No |
> | expressRouteCrossConnections/peerings | No |No |
> | gatewaySupportedDevices | No |No |
> | networkSecurityGroups | Yes |Yes |
> | quotas | No |No |
> | reservedIps | Sì |Sì |
> | virtualNetworks | Yes |Sì |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |No |
> | virtualNetworks/virtualNetworkPeerings | No |No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No |No |
> | dischi | No |No |
> | immagini | No |No |
> | osImages | No |No |
> | osPlatformImages | No |No |
> | publicImages | No |No |
> | quotas | No |No |
> | storageAccounts | Yes |Yes |
> | storageAccounts/blobServices | No |No |
> | storageAccounts/fileServices | No |No |
> | storageAccounts/metricDefinitions | No |No |
> | storageAccounts/metriche | No |No |
> | storageAccounts/queueServices | No |No |
> | storageAccounts/services | No |No |
> | storageAccounts/services/diagnosticSettings | No |No |
> | storageAccounts/services/metricDefinitions | No |No |
> | storageAccounts/Servizi/metriche | No |No |
> | storageAccounts/tableServices | No |No |
> | storageAccounts/vmImages | No |No |
> | vmImages | No |No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Yes |Yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | RateCard | No |No |
> | UsageAggregates | No |No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sì |Sì |
> | diskEncryptionSets | Yes |Sì |
> | dischi | Yes |Sì |
> | galleries | Sì |Sì |
> | raccolte/applicazioni | Sì |Yes |
> | raccolte/applicazioni/versioni | Sì |Sì |
> | galleries/images | Yes |Sì |
> | galleries/images/versions | Sì |Yes |
> | Gruppi host | Sì |Yes |
> | Gruppi host/host | Sì |Sì |
> | immagini | Sì |Sì |
> | proximityPlacementGroups | Sì |Sì |
> | restorePointCollections | Sì |Sì |
> | restorePointCollections/restorePoints | No |No |
> | sharedVMImages | Sì |Sì |
> | sharedVMImages/versions | Sì |Yes |
> | snapshot | Yes |Yes |
> | virtualMachines | Sì |Sì |
> | virtualMachines/extensions | Yes |Yes |
> | virtualMachines/metricDefinitions | No |No |
> | virtualMachines/scriptJobs | No |No |
> | virtualMachines/softwareUpdateDeployments | No |No |
> | virtualMachineScaleSets | Sì |Sì |
> | virtualMachineScaleSets/extensions | No |No |
> | virtualMachineScaleSets/networkInterfaces | No |No |
> | virtualMachineScaleSets/publicIPAddresses | No |No |
> | virtualMachineScaleSets/virtualMachines | No |No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | No |No |
> | Saldi | No |No |
> | Budget | No |No |
> | Charges | No |No |
> | CostTags | No |No |
> | credits | No |No |
> | eventi | No |No |
> | Previsioni | No |No |
> | lots | No |No |
> | Marketplace | No |No |
> | Pricesheets | No |No |
> | products | No |No |
> | ReservationDetails | No |No |
> | ReservationRecommendations | No |No |
> | ReservationSummaries | No |No |
> | ReservationTransactions | No |No |
> | Tag | No |No |
> | tenants | No |No |
> | Termini | No |No |
> | UsageDetails | No |No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sì |Sì |
> | serviceAssociationLinks | No |No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | registries | Yes |Sì |
> | registries/builds | No |No |
> | registries/builds/cancel | No |No |
> | registries/builds/getLogLink | No |No |
> | registries/buildTasks | Yes |Sì |
> | registries/buildTasks/steps | No |No |
> | registries/eventGridFilters | No |No |
> | registries/getBuildSourceUploadUrl | No |No |
> | registries/GetCredentials | No |No |
> | registries/importImage | No |No |
> | registries/queueBuild | No |No |
> | registries/regenerateCredential | No |No |
> | registries/regenerateCredentials | No |No |
> | registries/replications | Sì |Sì |
> | registries/runs | No |No |
> | registries/runs/cancel | No |No |
> | registries/scheduleRun | No |No |
> | registries/tasks | Sì |Sì |
> | registries/updatePolicies | No |No |
> | registries/webhooks | Sì |Yes |
> | registries/webhooks/getCallbackConfig | No |No |
> | registries/webhooks/ping | No |No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerServices | Yes |Sì |
> | managedClusters | Yes |Yes |
> | openShiftManagedClusters | Yes |Sì |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | Yes |Sì |
> | updateCommunicationPreference | No |No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì |Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Avvisi | No |No |
> | BillingAccounts | No |No |
> | Budget | No |No |
> | CloudConnectors | No |No |
> | Connettori | Sì |Sì |
> | Departments | No |No |
> | Dimensioni | No |No |
> | EnrollmentAccounts | No |No |
> | Esportazioni | No |No |
> | ExternalBillingAccounts | No |No |
> | ExternalBillingAccounts/avvisi | No |No |
> | ExternalBillingAccounts/dimensioni | No |No |
> | ExternalBillingAccounts/previsione | No |No |
> | ExternalBillingAccounts/query | No |No |
> | ExternalSubscriptions | No |No |
> | ExternalSubscriptions/avvisi | No |No |
> | ExternalSubscriptions/dimensioni | No |No |
> | ExternalSubscriptions/previsione | No |No |
> | ExternalSubscriptions/query | No |No |
> | Previsione | No |No |
> | Query | No |No |
> | register | No |No |
> | Reportconfigs | No |No |
> | Report | No |No |
> | Impostazioni | No |No |
> | showbackRules | No |No |
> | Visualizzazioni | No |No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | richieste | No |No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | associazioni | No |No |
> | resourceProviders | Sì |Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | processi | Sì |Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Yes |Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | Sì |No |
> | workspaces/virtualNetworkPeerings | No |No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | catalogs | Sì |Sì |
> | datacatalogs | Sì |Sì |
> | datacatalogs/origini dati | No |No |
> | sottocataloghi/origini dati/analisi | No |No |
> | datacatalogs/DataSources/scansioni/set di dati | No |No |
> | datacatalogs/origini dati/analisi/trigger | No |No |
> | datacatalogs/scantargets | No |No |
> | datacatalogs/scantargets/DataSets | No |No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sì |No |
> | dataFactories/diagnosticSettings | No |No |
> | dataFactories/metricDefinitions | No |No |
> | dataFactorySchema | No |No |
> | factories | Yes |No |
> | factories/integrationRuntimes | No |No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì |Sì |
> | accounts/dataLakeStoreAccounts | No |No |
> | accounts/storageAccounts | No |No |
> | accounts/storageAccounts/containers | No |No |
> | account/transferAnalyticsUnits | No |No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Yes |Yes |
> | accounts/eventGridFilters | No |No |
> | accounts/firewallRules | No |No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | No |No |
> | services/projects | No |No |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì |Yes |
> | account/condivisioni | No |No |
> | account/condivisioni/set di impostazioni | No |No |
> | account/condivisioni/inviti | No |No |
> | Accounts/shares/providersharesubscriptions | No |No |
> | Accounts/shares/synchronizationSettings | No |No |
> | account/sharesubscriptions | No |No |
> | account/sharesubscriptions/consumerSourceDataSets | No |No |
> | account/sharesubscriptions/datasetmappings | No |No |
> | account/sharesubscriptions/trigger | No |No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | Yes |Sì |
> | servers/advisors | No |No |
> | servers/queryTexts | No |No |
> | servers/recoverableServers | No |No |
> | servers/topQueryStatistics | No |No |
> | servers/virtualNetworkRules | No |No |
> | servers/waitStatistics | No |No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | server | Sì |Sì |
> | servers/advisors | No |No |
> | servers/queryTexts | No |No |
> | servers/recoverableServers | No |No |
> | servers/topQueryStatistics | No |No |
> | servers/virtualNetworkRules | No |No |
> | servers/waitStatistics | No |No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | serverGroups | Yes |Sì |
> | server | Sì |Yes |
> | servers/advisors | No |No |
> | servers/queryTexts | No |No |
> | servers/recoverableServers | No |No |
> | servers/topQueryStatistics | No |No |
> | servers/virtualNetworkRules | No |No |
> | servers/waitStatistics | No |No |
> | serversv2 | Yes |Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | artifactSources | Yes |Sì |
> | rollouts | Yes |Sì |
> | serviceTopologies | Sì |Sì |
> | serviceTopologies/servizi | Yes |Sì |
> | serviceTopologies/Services/serviceUnits | Yes |Yes |
> | steps | Yes |Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Yes |Yes |
> | applicationgroups/applicazioni | No |No |
> | applicationgroups/startmenuitems | No |No |
> | hostpools | Yes |Yes |
> | hostpools/sessionhosts | No |No |
> | hostpools/sessionhosts/usersessions | No |No |
> | hostpools/usersessions | No |No |
> | aree di lavoro | Sì |Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | elasticPools | Sì |Yes |
> | ElasticPools/IotHubTenants | Sì |Sì |
> | Hub IoT | Yes |Sì |
> | IotHubs/eventGridFilters | No |No |
> | ProvisioningServices | Sì |Yes |
> | usages | No |No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | pipeline | Sì |Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | controllers | Yes |Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | labcenters | Sì |Sì |
> | labs | Yes |Sì |
> | Lab/ambienti | Yes |Sì |
> | labs/serviceRunners | Sì |Yes |
> | labs/virtualMachines | Yes |Sì |
> | schedules | Yes |Sì |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No |No |
> | databaseAccounts | Sì |Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | Sì |Sì |
> | domains/domainOwnershipIdentifiers | No |No |
> | generateSsoRequest | No |No |
> | topLevelDomains | No |No |
> | validateDomainRegistrationInformation | No |No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No |No |
> | lcsprojects/clouddeployments | No |No |
> | lcsprojects/connectors | No |No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | Sì |Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | Yes |Sì |
> | domains/topics | No |No |
> | eventSubscriptions | No |No |
> | extensionTopics | No |No |
> | topics | Sì |Sì |
> | topicTypes | No |No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì |Sì |
> | spazi dei nomi | Sì |Sì |
> | namespaces/authorizationrules | No |No |
> | namespaces/disasterrecoveryconfigs | No |No |
> | namespaces/eventhubs | No |No |
> | namespaces/eventhubs/authorizationrules | No |No |
> | namespaces/eventhubs/consumergroups | No |No |
> | spazi dei nomi/networkrulesets | No |No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | funzionalità | No |No |
> | provider | No |No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | enroll | No |No |
> | galleryitems | No |No |
> | generateartifactaccessuri | No |No |
> | myareas | No |No |
> | myareas/areas | No |No |
> | myareas/areas/areas | No |No |
> | myareas/areas/areas/galleryitems | No |No |
> | myareas/areas/galleryitems | No |No |
> | myareas/galleryitems | No |No |
> | register | No |No |
> | risorse | No |No |
> | retrieveresourcesbyid | No |No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì |Sì |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | Sì |Sì |
> | guestConfigurationAssignments | No |No |
> | software | No |No |
> | softwareUpdateProfile | No |No |
> | softwareUpdates | No |No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sì |Sì |
> | sapMonitors | Sì |Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Yes |Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì |Sì |
> | clusters/applications | No |No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | Sì |Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | computer | Sì |Sì |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | datamanager | Sì |Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | components | Sì |Sì |
> | networkScopes | Sì |Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | processi | Yes |Sì |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No |No |
> | diagnosticSettingsCategories | No |No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appTemplates | No |No |
> | IoTApps | Sì |Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Grafico | Sì |Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No |No |
> | hsmPools | Sì |Sì |
> | insiemi di credenziali | Sì |Yes |
> | vaults/accessPolicies | No |No |
> | insiemi di credenziali/eventGridFilters | No |No |
> | vaults/secrets | No |No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì |Yes |
> | cluster/attacheddatabaseconfigurations | No |No |
> | clusters/databases | No |No |
> | clusters/databases/dataconnections | No |No |
> | clusters/databases/eventhubconnections | No |No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | labaccounts | Yes |Sì |
> | Utenti | No |No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sì |Yes |
> | integrationAccounts | Yes |Yes |
> | integrationServiceEnvironments | Yes |Yes |
> | integrationServiceEnvironments/managedApis | Sì |Yes |
> | isolatedEnvironments | Sì |Yes |
> | flussi di lavoro | Yes |Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Yes |Sì |
> | webServices | Sì |Sì |
> | Aree di lavoro | Sì |Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | Yes |Sì |
> | workspaces/computes | No |No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Identità | No |No |
> | userAssignedIdentities | Sì |Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No |No |
> | registrationAssignments | No |No |
> | registrationDefinitions | No |No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | getEntities | No |No |
> | managementGroups | No |No |
> | risorse | No |No |
> | startTenantBackfill | No |No |
> | tenantBackfillStatus | No |No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì |Sì |
> | accounts/eventGridFilters | No |No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | offers | No |No |
> | offerTypes | No |No |
> | offerTypes/publishers | No |No |
> | offerTypes/publishers/offers | No |No |
> | offerTypes/publishers/offers/plans | No |No |
> | offerTypes/publishers/offers/plans/agreements | No |No |
> | offerTypes/publishers/offers/plans/configs | No |No |
> | offerTypes/publishers/offers/plans/configs/importImage | No |No |
> | privategalleryitems | No |No |
> | products | No |No |
> | editori | No |No |
> | editori/offerte | No |No |
> | editori/offerte/modifiche | No |No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Yes |Yes |
> | updateCommunicationPreference | No |No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | agreements | No |No |
> | offertypes | No |No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sì |Yes |
> | mediaservices/accountFilters | No |No |
> | mediaservices/assets | No |No |
> | mediaservices/assets/assetFilters | No |No |
> | mediaservices/contentKeyPolicies | No |No |
> | mediaservices/eventGridFilters | No |No |
> | mediaservices/liveEventOperations | No |No |
> | mediaservices/liveEvents | Sì |Sì |
> | mediaservices/liveEvents/liveOutputs | No |No |
> | mediaservices/liveOutputOperations | No |No |
> | mediaservices/streamingEndpointOperations | No |No |
> | mediaservices/streamingEndpoints | Yes |Sì |
> | mediaservices/streamingLocators | No |No |
> | mediaservices/streamingPolicies | No |No |
> | mediaservices/transforms | No |No |
> | mediaservices/transforms/jobs | No |No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appClusters | Sì |Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Sì |Sì |
> | migrateprojects | Sì |Sì |
> | projects | Sì |Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Sì |Yes |
> | objectUnderstandingAccounts | Yes |Sì |
> | remoteRenderingAccounts | Sì |Yes |
> | spatialAnchorsAccounts | Sì |Sì |
> | surfaceReconstructionAccounts | Sì |Sì |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sì |Sì |
> | netAppAccounts/capacityPools | Sì |Sì |
> | netAppAccounts/capacityPools/volumi | Yes |Sì |
> | netAppAccounts/capacityPools/Volumes/mountTargets | Yes |Sì |
> | netAppAccounts/capacityPools/volumi/snapshot | Yes |Sì |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sì |Sì |
> | applicationGatewayWebApplicationFirewallPolicies | Sì |Sì |
> | applicationSecurityGroups | Yes |Sì |
> | azureFirewallFqdnTags | No |No |
> | azureFirewalls | Sì |Yes |
> | bastionHosts | Sì |Yes |
> | bgpServiceCommunities | No |No |
> | connessioni | Sì |Sì |
> | ddosCustomPolicies | Sì |Sì |
> | ddosProtectionPlans | Sì |Sì |
> | dnsOperationStatuses | No |No |
> | dnszones | Sì |Yes |
> | dnszones/A | No |No |
> | dnszones/AAAA | No |No |
> | dnszones/all | No |No |
> | dnszones/CAA | No |No |
> | dnszones/CNAME | No |No |
> | dnszones/MX | No |No |
> | dnszones/NS | No |No |
> | dnszones/PTR | No |No |
> | dnszones/recordsets | No |No |
> | dnszones/SOA | No |No |
> | dnszones/SRV | No |No |
> | dnszones/TXT | No |No |
> | expressRouteCircuits | Sì |Yes |
> | expressRouteCrossConnections | Sì |Yes |
> | expressRouteGateways | Sì |Sì |
> | expressRoutePorts | Yes |Yes |
> | expressRouteServiceProviders | No |No |
> | firewallPolicies | Sì |Sì |
> | frontdoor | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) |Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) |No |
> | frontdoorWebApplicationFirewallPolicies | Sì, ma limitato (vedere la [Nota di seguito](#frontdoor)) |Yes |
> | getDnsResourceReference | No |No |
> | internalNotify | No |No |
> | loadBalancers | Yes |No |
> | localNetworkGateways | Yes |Sì |
> | natGateways | Sì |Sì |
> | networkIntentPolicies | Sì |Sì |
> | networkInterfaces | Yes |Sì |
> | networkProfiles | Sì |Sì |
> | networkSecurityGroups | Sì |Sì |
> | networkWatchers | Yes |No |
> | networkWatchers/connectionMonitors | Sì |No |
> | networkWatchers/lenses | Yes |No |
> | networkWatchers/pingMeshes | Sì |No |
> | p2sVpnGateways | Sì |Sì |
> | privateDnsOperationStatuses | No |No |
> | privateDnsZones | Sì |Sì |
> | privateDnsZones/A | No |No |
> | privateDnsZones/AAAA | No |No |
> | privateDnsZones/tutti | No |No |
> | privateDnsZones/CNAME | No |No |
> | privateDnsZones/MX | No |No |
> | privateDnsZones/PTR | No |No |
> | privateDnsZones/SOA | No |No |
> | privateDnsZones/SRV | No |No |
> | privateDnsZones/TXT | No |No |
> | privateDnsZones/virtualNetworkLinks | Sì |Sì |
> | privateEndpoints | Sì |Sì |
> | privateLinkServices | Sì |Sì |
> | publicIPAddresses | Sì |Sì |
> | publicIPPrefixes | Sì |Yes |
> | routeFilters | Sì |Sì |
> | routeTables | Yes |Yes |
> | secureGateways | Sì |Sì |
> | serviceEndpointPolicies | Sì |Yes |
> | trafficManagerGeographicHierarchies | No |No |
> | trafficmanagerprofiles | Yes |Sì |
> | trafficmanagerprofiles/heatMaps | No |No |
> | trafficManagerUserMetricsKeys | No |No |
> | virtualHubs | Sì |Sì |
> | virtualNetworkGateways | Yes |Sì |
> | virtualNetworks | Sì |Sì |
> | virtualNetworkTaps | Sì |Sì |
> | virtualWans | Sì |Sì |
> | vpnGateways | Sì |No |
> | vpnSites | Sì |Sì |
> | webApplicationFirewallPolicies | Sì |Sì |

<a id="frontdoor" />

> [!NOTE]
> Per il servizio front-end di Azure, è possibile applicare tag quando si crea la risorsa, ma non è attualmente supportata l'aggiornamento o l'aggiunta di tag.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì |No |
> | namespaces/notificationHubs | Sì |No |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Yes |Yes |
> | ImportSites | Sì |Sì |
> | ServerSites | Sì |Sì |
> | VMwareSites | Yes |Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Yes |Sì |
> | dispositivi | No |No |
> | linkTargets | No |No |
> | storageInsightConfigs | No |No |
> | aree di lavoro | Sì |Yes |
> | workspaces/dataSources | No |No |
> | workspaces/linkedServices | No |No |
> | workspaces/query | No |No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managementassociations | No |No |
> | managementconfigurations | Yes |Sì |
> | solutions | Yes |Yes |
> | visualizzazioni | Sì |Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No |No |
> | peerAsns | No |No |
> | peering | Yes |Sì |
> | peeringServiceProviders | No |No |
> | peeringServices | Yes |Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | policyEvents | No |No |
> | policyStates | No |No |
> | policyTrackedResources | No |No |
> | remediations | No |No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | consoles | No |No |
> | dashboards | Yes |Yes |
> | userSettings | No |No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Yes |Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capacities | Sì |Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No |No |
> | insiemi di credenziali | Sì |Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì |Sì |
> | namespaces/authorizationrules | No |No |
> | namespaces/hybridconnections | No |No |
> | namespaces/hybridconnections/authorizationrules | No |No |
> | namespaces/wcfrelays | No |No |
> | namespaces/wcfrelays/authorizationrules | No |No |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | No |No |
> | Collezioni | Sì |Sì |
> | raccolte/applicazioni | No |No |
> | raccolte/SecurityPrincipals | No |No |
> | templateImages | No |No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | query | Sì |Sì |
> | resourceChangeDetails | No |No |
> | resourceChanges | No |No |
> | risorse | No |No |
> | resourcesHistory | No |No |
> | subscriptionsStatus | No |No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No |No |
> | childAvailabilityStatuses | No |No |
> | childResources | No |No |
> | eventi | No |No |
> | impactedResources | No |No |
> | Metadati | No |No |
> | notifiche | No |No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | distribuzioni | Yes |No |
> | deployments/operations | No |No |
> | links | No |No |
> | notifyResourceJobs | No |No |
> | provider | No |No |
> | resourceGroups | Sì |No |
> | risorse | No |No |
> | sottoscrizioni | No |No |
> | subscriptions/providers | No |No |
> | subscriptions/resources | No |No |
> | subscriptions/tagnames | No |No |
> | subscriptions/tagNames/tagValues | No |No |
> | tenants | No |No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | Yes |Yes |
> | saasresources | No |No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | jobcollections | Sì |Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No |No |
> | searchServices | Yes |Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No |No |
> | advancedThreatProtectionSettings | No |No |
> | avvisi | No |No |
> | allowedConnections | No |No |
> | applicationWhitelistings | No |No |
> | assessmentMetadata | No |No |
> | valutazioni | No |No |
> | AutoProvisioningSettings | No |No |
> | Compliances | No |No |
> | dataCollectionAgents | No |No |
> | deviceSecurityGroups | No |No |
> | discoveredSecuritySolutions | No |No |
> | externalSecuritySolutions | No |No |
> | InformationProtectionPolicies | No |No |
> | iotSecuritySolutions | Sì |Sì |
> | iotSecuritySolutions/analyticsModels | No |No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |No |
> | jitNetworkAccessPolicies | No |No |
> | File networkdata | No |No |
> | playbookConfigurations | Sì |Yes |
> | criteri | No |No |
> | pricings | No |No |
> | regulatoryComplianceStandards | No |No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |No |
> | securityContacts | No |No |
> | securitySolutions | No |No |
> | securitySolutionsReferenceData | No |No |
> | securityStatuses | No |No |
> | securityStatusesSummaries | No |No |
> | serverVulnerabilityAssessments | No |No |
> | impostazioni | No |No |
> | attività | No |No |
> | topologies | No |No |
> | workspaceSettings | No |No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No |No |
> | diagnosticSettingsCategories | No |No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | le aggregazioni | No |No |
> | Regole di avviso | No |No |
> | alertRuleTemplates | No |No |
> | Segnalibri | No |No |
> | cases | No |No |
> | DataConnector | No |No |
> | entità | No |No |
> | entityQueries | No |No |
> | officeConsents | No |No |
> | impostazioni | No |No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì |No |
> | namespaces/authorizationrules | No |No |
> | namespaces/disasterrecoveryconfigs | No |No |
> | namespaces/eventgridfilters | No |No |
> | spazi dei nomi/networkrulesets | No |No |
> | namespaces/queues | No |No |
> | namespaces/queues/authorizationrules | No |No |
> | namespaces/topics | No |No |
> | namespaces/topics/authorizationrules | No |No |
> | namespaces/topics/subscriptions | No |No |
> | namespaces/topics/subscriptions/rules | No |No |
> | premiumMessagingRegions | No |No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | Yes |Yes |
> | clusters | Yes |Sì |
> | clusters/applications | No |No |
> | containerGroups | Sì |Sì |
> | containerGroupSets | Yes |Yes |
> | edgeclusters | Sì |Sì |
> | edgeclusters/applicazioni | No |No |
> | Reti | Sì |Yes |
> | secretstores | Sì |Sì |
> | secretstores/certificati | No |No |
> | secretstores/segreti | No |No |
> | volumes | Yes |Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicazioni | Yes |Sì |
> | containerGroups | Yes |Sì |
> | gateways | Yes |Sì |
> | Reti | Yes |Yes |
> | segreti | Sì |Yes |
> | volumes | Sì |Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No |No |
> | providerRegistrations/resourceTypeRegistrations | No |No |
> | rollouts | Yes |Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SignalR | Sì |Sì |
> | SignalR/eventGridFilters | No |No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sì |Sì |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No |No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sì |Sì |
> | applicazioni | Sì |Sì |
> | jitRequests | Yes |Sì |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sì | Yes |
> | managedInstances/databases | Sì (vedere la [Nota sotto](#sqlnote)) | Sì |
> | managedInstances/databases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No | No |
> | managedInstances/databases/vulnerabilityAssessments | No | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No | No |
> | managedInstances/encryptionProtector | No | No |
> | managedInstances/keys | No | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/vulnerabilityAssessments | No | No |
> | server | Sì | Sì |
> | servers/administrators | No | No |
> | servers/communicationLinks | No | No |
> | servers/databases | Sì (vedere la [Nota sotto](#sqlnote)) | Yes |
> | servers/encryptionProtector | No | No |
> | servers/firewallRules | No | No |
> | servers/keys | No | No |
> | servers/restorableDroppedDatabases | No | No |
> | servers/serviceobjectives | No | No |
> | servers/tdeCertificates | No | No |

<a id="sqlnote" />

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sì |Sì |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |No |
> | SqlVirtualMachines | Sì |Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sì |Yes |
> | storageAccounts/blobServices | No |No |
> | storageAccounts/fileServices | No |No |
> | storageAccounts/queueServices | No |No |
> | storageAccounts/services | No |No |
> | storageAccounts/services/metricDefinitions | No |No |
> | storageAccounts/tableServices | No |No |
> | usages | No |No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | cache | Yes |Yes |
> | cache/storageTargets | No |No |
> | usageModels | No |No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No |No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì |Yes |
> | storageSyncServices/registeredServers | No |No |
> | storageSyncServices/syncGroups | No |No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |No |
> | storageSyncServices/syncGroups/serverEndpoints | No |No |
> | storageSyncServices/workflows | No |No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes |Sì |
> | storageSyncServices/registeredServers | No |No |
> | storageSyncServices/syncGroups | No |No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |No |
> | storageSyncServices/syncGroups/serverEndpoints | No |No |
> | storageSyncServices/workflows | No |No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì |Sì |
> | storageSyncServices/registeredServers | No |No |
> | storageSyncServices/syncGroups | No |No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |No |
> | storageSyncServices/syncGroups/serverEndpoints | No |No |
> | storageSyncServices/workflows | No |No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managers | Yes |Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sì (vedere la nota seguente) |Sì |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | annulla | No |No |
> | CreateSubscription | No |No |
> | enable | No |No |
> | rinomina | No |No |
> | SubscriptionDefinitions | No |No |
> | SubscriptionOperations | No |No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | environments | Yes |No |
> | environments/accessPolicies | No |No |
> | environments/eventsources | Yes |No |
> | environments/referenceDataSets | Sì |No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Sì |Sì |
> | dedicatedCloudServices | Sì |Sì |
> | virtualMachines | Sì |Yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No |No |
> | apiManagementAccounts/apiAcls | No |No |
> | apiManagementAccounts/apis | No |No |
> | apiManagementAccounts/apis/apiAcls | No |No |
> | apiManagementAccounts/apis/connectionAcls | No |No |
> | apiManagementAccounts/apis/connections | No |No |
> | apiManagementAccounts/apis/connections/connectionAcls | No |No |
> | apiManagementAccounts/apis/localizedDefinitions | No |No |
> | apiManagementAccounts/connectionAcls | No |No |
> | apiManagementAccounts/connections | No |No |
> | billingMeters | No |No |
> | certificati | Sì |Sì |
> | connectionGateways | Yes |Sì |
> | connessioni | Yes |Sì |
> | customApis | Yes |Yes |
> | deletedSites | No |No |
> | funzioni | No |No |
> | hostingEnvironments | Sì |Sì |
> | hostingEnvironments/multiRolePools | No |No |
> | hostingEnvironments/workerPools | No |No |
> | publishingUsers | No |No |
> | raccomandazioni | No |No |
> | resourceHealthMetadata | No |No |
> | runtimes | No |No |
> | serverFarms | Sì |Yes |
> | serverFarms/eventGridFilters | No |No |
> | siti | Yes |Sì |
> | siti/configurazione  | No | No |
> | siti/eventGridFilters | No |No |
> | sites/hostNameBindings | No |No |
> | siti/file networkconfig | No |No |
> | sites/premieraddons | Yes |Yes |
> | sites/slots | Sì |Sì |
> | siti/slot/eventGridFilters | No |No |
> | sites/slots/hostNameBindings | No |No |
> | siti/slot/file networkconfig | No |No |
> | sourceControls | No |No |
> | validate | No |No |
> | verifyHostingEnvironmentVnet | No |No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No |No |
> | diagnosticSettingsCategories | No |No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sì |Sì |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | components | No |No |
> | componentsSummary | No |No |
> | monitorInstances | No |No |
> | monitorInstancesSummary | No |No |
> | monitors | No |No |
> | notificationSettings | No |No |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
