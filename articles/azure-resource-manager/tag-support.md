---
title: Tag support for resources
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422133"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | SÌ | SÌ |
> | DomainServices / oucontainer | No | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurazioni | No | No |
> | generateRecommendations | No | No |
> | metadata | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | SÌ | SÌ |
> | alerts | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | feedback | No | No |
> | smartDetectorAlertRules | SÌ | SÌ |
> | smartDetectorRuntimeEnvironments | No | No |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | servizio | SÌ | SÌ |
> | validateServiceName | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | SÌ | SÌ |
> | configurationStores / eventGridFilters | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | SÌ | SÌ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No | No |
> | dataAliases | No | No |
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
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | SÌ | SÌ |
> | automationAccounts / configurations | SÌ | SÌ |
> | automationAccounts / jobs | No | No |
> | automationAccounts / runbooks | SÌ | SÌ |
> | automationAccounts / softwareUpdateConfigurations | No | No |
> | automationAccounts / webhooks | No | No |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | SÌ | SÌ |
> | configurationStores / eventGridFilters | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | No | No |
> | environments / accounts | No | No |
> | environments / accounts / namespaces | No | No |
> | environments / accounts / namespaces / configurations | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | SÌ | No |
> | b2ctenants | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | SÌ | SÌ |
> | postgresInstances | SÌ | SÌ |
> | sqlBigDataClusters | SÌ | SÌ |
> | sqlInstances | SÌ | SÌ |
> | sqlServerRegistrations | SÌ | SÌ |
> | sqlServerRegistrations / sqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | SÌ | SÌ |
> | registrations / customerSubscriptions | No | No |
> | registrations / products | No | No |
> | verificationKeys | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | SÌ | SÌ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccounts / agreements | No | No |
> | billingAccounts / billingPermissions | No | No |
> | billingAccounts / billingProfiles | No | No |
> | billingAccounts / billingProfiles / billingPermissions | No | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No | No |
> | billingAccounts / billingProfiles / customers | No | No |
> | billingAccounts / billingProfiles / invoices | No | No |
> | billingAccounts / billingProfiles / invoices / pricesheet | No | No |
> | billingAccounts / billingProfiles / invoiceSections | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No | No |
> | billingAccounts / billingProfiles / invoiceSections / products | No | No |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | No | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No | No |
> | billingAccounts / billingProfiles / invoiceSections / transactions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / transfers | No | No |
> | billingAccounts / BillingProfiles / patchOperations | No | No |
> | billingAccounts / billingProfiles / paymentMethods | No | No |
> | billingAccounts / billingProfiles / policies | No | No |
> | billingAccounts / billingProfiles / pricesheet | No | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No | No |
> | billingAccounts / billingProfiles / products | No | No |
> | billingAccounts / billingProfiles / transactions | No | No |
> | billingAccounts / billingRoleAssignments | No | No |
> | billingAccounts / billingRoleDefinitions | No | No |
> | billingAccounts / billingSubscriptions | No | No |
> | billingAccounts / billingSubscriptions / invoices | No | No |
> | billingAccounts / createBillingRoleAssignment | No | No |
> | billingAccounts / createInvoiceSectionOperations | No | No |
> | billingAccounts / customers | No | No |
> | billingAccounts / customers / billingPermissions | No | No |
> | billingAccounts / customers / billingSubscriptions | No | No |
> | billingAccounts / customers / initiateTransfer | No | No |
> | billingAccounts / customers / policies | No | No |
> | billingAccounts / customers / products | No | No |
> | billingAccounts / customers / transactions | No | No |
> | billingAccounts / customers / transfers | No | No |
> | billingAccounts / departments | No | No |
> | billingAccounts / enrollmentAccounts | No | No |
> | billingAccounts / invoices | No | No |
> | billingAccounts / invoiceSections | No | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No | No |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | No | No |
> | billingAccounts / invoiceSections / elevate | No | No |
> | billingAccounts / invoiceSections / initiateTransfer | No | No |
> | billingAccounts / invoiceSections / patchOperations | No | No |
> | billingAccounts / invoiceSections / productMoveOperations | No | No |
> | billingAccounts / invoiceSections / products | No | No |
> | billingAccounts / invoiceSections / products / transfer | No | No |
> | billingAccounts / invoiceSections / products / updateAutoRenew | No | No |
> | billingAccounts / invoiceSections / transactions | No | No |
> | billingAccounts / invoiceSections / transfers | No | No |
> | billingAccounts / lineOfCredit | No | No |
> | billingAccounts / patchOperations | No | No |
> | billingAccounts / paymentMethods | No | No |
> | billingAccounts / products | No | No |
> | billingAccounts / transactions | No | No |
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
> | transfers / acceptTransfer | No | No |
> | transfers / declineTransfer | No | No |
> | transfers / operationStatus | No | No |
> | transfers / validateTransfer | No | No |
> | validateAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | SÌ | SÌ |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | SÌ | SÌ |
> | cordaMembers | SÌ | SÌ |
> | visualizzazioni | SÌ | SÌ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments / assignmentOperations | No | No |
> | blueprintAssignments / operations | No | No |
> | blueprints | No | No |
> | blueprints / artifacts | No | No |
> | blueprints / versions | No | No |
> | blueprints / versions / artifacts | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | SÌ | SÌ |
> | botServices / channels | No | No |
> | botServices / connections | No | No |
> | lingue | No | No |
> | Modelli | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | SÌ | SÌ |
> | RedisConfigDefinition | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | reservationOrders / calculateRefund | No | No |
> | reservationOrders / merge | No | No |
> | reservationOrders / reservations | No | No |
> | reservationOrders / reservations / revisions | No | No |
> | reservationOrders / return | No | No |
> | reservationOrders / split | No | No |
> | reservationOrders / swap | No | No |
> | reservations | No | No |
> | resources | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | SÌ | SÌ |
> | edgenodes | No | No |
> | Profili | SÌ | SÌ |
> | profiles / endpoints | SÌ | SÌ |
> | profiles / endpoints / customdomains | No | No |
> | profiles / endpoints / origins | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | SÌ | SÌ |
> | certificateOrders / certificates | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | domainNames | No | No |
> | domainNames / capabilities | No | No |
> | domainNames / internalLoadBalancers | No | No |
> | domainNames / serviceCertificates | No | No |
> | domainNames / slots | No | No |
> | domainNames / slots / roles | No | No |
> | domainNames / slots / roles / metricDefinitions | No | No |
> | domainNames / slots / roles / metrics | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | resourceTypes | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines / diagnosticSettings | No | No |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachines / metrics | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections / peerings | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks / virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | dischi | No | No |
> | images | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts / blobServices | No | No |
> | storageAccounts / fileServices | No | No |
> | storageAccounts / metricDefinitions | No | No |
> | storageAccounts / metrics | No | No |
> | storageAccounts / queueServices | No | No |
> | storageAccounts / services | No | No |
> | storageAccounts / services / diagnosticSettings | No | No |
> | storageAccounts / services / metricDefinitions | No | No |
> | storageAccounts / services / metrics | No | No |
> | storageAccounts / tableServices | No | No |
> | storageAccounts / vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | SÌ | SÌ |
> | diskEncryptionSets | SÌ | SÌ |
> | dischi | SÌ | SÌ |
> | galleries | SÌ | SÌ |
> | galleries / applications | No | No |
> | galleries / applications / versions | No | No |
> | galleries / images | No | No |
> | galleries / images / versions | No | No |
> | hostGroups | SÌ | SÌ |
> | hostGroups / hosts | SÌ | SÌ |
> | images | SÌ | SÌ |
> | proximityPlacementGroups | SÌ | SÌ |
> | restorePointCollections | SÌ | SÌ |
> | restorePointCollections / restorePoints | No | No |
> | sharedVMImages | SÌ | SÌ |
> | sharedVMImages / versions | No | No |
> | snapshots | SÌ | SÌ |
> | virtualMachines | SÌ | SÌ |
> | virtualMachines / extensions | SÌ | SÌ |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachineScaleSets | SÌ | SÌ |
> | virtualMachineScaleSets / extensions | No | No |
> | virtualMachineScaleSets / networkInterfaces | No | No |
> | virtualMachineScaleSets / publicIPAddresses | No | No |
> | virtualMachineScaleSets / virtualMachines | No | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | SÌ | SÌ |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | SÌ | SÌ |
> | registries / builds | No | No |
> | registries / builds / cancel | No | No |
> | registries / builds / getLogLink | No | No |
> | registries / buildTasks | SÌ | SÌ |
> | registries / buildTasks / steps | No | No |
> | registries / eventGridFilters | No | No |
> | registries / generateCredentials | No | No |
> | registries / getBuildSourceUploadUrl | No | No |
> | registries / GetCredentials | No | No |
> | registries / importImage | No | No |
> | registries / queueBuild | No | No |
> | registries / regenerateCredential | No | No |
> | registries / regenerateCredentials | No | No |
> | registries / replications | SÌ | SÌ |
> | registries / runs | No | No |
> | registries / runs / cancel | No | No |
> | registries / scheduleRun | No | No |
> | registries / scopeMaps | No | No |
> | registries / taskRuns | SÌ | SÌ |
> | registries / tasks | SÌ | SÌ |
> | registries / tokens | No | No |
> | registries / updatePolicies | No | No |
> | registries / webhooks | SÌ | SÌ |
> | registries / webhooks / getCallbackConfig | No | No |
> | registries / webhooks / ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | SÌ | SÌ |
> | managedClusters | SÌ | SÌ |
> | openShiftManagedClusters | SÌ | SÌ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | ExternalBillingAccounts / Alerts | No | No |
> | ExternalBillingAccounts / Dimensions | No | No |
> | ExternalBillingAccounts / Forecast | No | No |
> | ExternalBillingAccounts / Query | No | No |
> | ExternalSubscriptions | No | No |
> | ExternalSubscriptions / Alerts | No | No |
> | ExternalSubscriptions / Dimensions | No | No |
> | ExternalSubscriptions / Forecast | No | No |
> | ExternalSubscriptions / Query | No | No |
> | Forecast | No | No |
> | Query | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | Report | No | No |
> | Impostazioni | No | No |
> | showbackRules | No | No |
> | Visualizzazioni | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | No | No |
> | resourceProviders | SÌ | SÌ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | SÌ | SÌ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | SÌ | SÌ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | SÌ | No |
> | workspaces / virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | SÌ | SÌ |
> | datacatalogs | SÌ | SÌ |
> | datacatalogs / datasources | No | No |
> | datacatalogs / datasources / scans | No | No |
> | datacatalogs / datasources / scans / datasets | No | No |
> | datacatalogs / datasources / scans / triggers | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | SÌ | No |
> | dataFactories / diagnosticSettings | No | No |
> | dataFactories / metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | factories | SÌ | No |
> | factories / integrationRuntimes | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts / dataLakeStoreAccounts | No | No |
> | accounts / storageAccounts | No | No |
> | accounts / storageAccounts / containers | No | No |
> | accounts / transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts / eventGridFilters | No | No |
> | accounts / firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | services / projects | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts / shares | No | No |
> | accounts / shares / datasets | No | No |
> | accounts / shares / invitations | No | No |
> | accounts / shares / providersharesubscriptions | No | No |
> | accounts / shares / synchronizationSettings | No | No |
> | accounts / sharesubscriptions | No | No |
> | accounts / sharesubscriptions / consumerSourceDataSets | No | No |
> | accounts / sharesubscriptions / datasetmappings | No | No |
> | accounts / sharesubscriptions / triggers | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |
> | servers / advisors | No | No |
> | servers / privateEndpointConnectionProxies | No | No |
> | servers / privateEndpointConnections | No | No |
> | servers / privateLinkResources | No | No |
> | servers / queryTexts | No | No |
> | servers / recoverableServers | No | No |
> | servers / topQueryStatistics | No | No |
> | servers / virtualNetworkRules | No | No |
> | servers / waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | server | SÌ | SÌ |
> | servers / advisors | No | No |
> | servers / privateEndpointConnectionProxies | No | No |
> | servers / privateEndpointConnections | No | No |
> | servers / privateLinkResources | No | No |
> | servers / queryTexts | No | No |
> | servers / recoverableServers | No | No |
> | servers / topQueryStatistics | No | No |
> | servers / virtualNetworkRules | No | No |
> | servers / waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | SÌ | SÌ |
> | server | SÌ | SÌ |
> | servers / advisors | No | No |
> | servers / keys | No | No |
> | servers / privateEndpointConnectionProxies | No | No |
> | servers / privateEndpointConnections | No | No |
> | servers / privateLinkResources | No | No |
> | servers / queryTexts | No | No |
> | servers / recoverableServers | No | No |
> | servers / topQueryStatistics | No | No |
> | servers / virtualNetworkRules | No | No |
> | servers / waitStatistics | No | No |
> | serversv2 | SÌ | SÌ |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | SÌ | SÌ |
> | rollouts | SÌ | SÌ |
> | serviceTopologies | SÌ | SÌ |
> | serviceTopologies / services | SÌ | SÌ |
> | serviceTopologies / services / serviceUnits | SÌ | SÌ |
> | steps | SÌ | SÌ |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | SÌ | SÌ |
> | applicationgroups / applications | No | No |
> | applicationgroups / desktops | No | No |
> | applicationgroups / startmenuitems | No | No |
> | hostpools | SÌ | SÌ |
> | hostpools / sessionhosts | No | No |
> | hostpools / sessionhosts / usersessions | No | No |
> | hostpools / usersessions | No | No |
> | aree di lavoro | SÌ | SÌ |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | SÌ | SÌ |
> | ElasticPools / IotHubTenants | SÌ | SÌ |
> | Hub IoT | SÌ | SÌ |
> | IotHubs / eventGridFilters | No | No |
> | ProvisioningServices | SÌ | SÌ |
> | usages | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | SÌ | SÌ |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | SÌ | SÌ |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | SÌ | SÌ |
> | labs | SÌ | SÌ |
> | labs / environments | SÌ | SÌ |
> | labs / serviceRunners | SÌ | SÌ |
> | labs / virtualMachines | SÌ | SÌ |
> | schedules | SÌ | SÌ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | SÌ | SÌ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | SÌ | SÌ |
> | domains / domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects / clouddeployments | No | No |
> | lcsprojects / connectors | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | SÌ | SÌ |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | SÌ | SÌ |
> | domains / topics | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | topics | SÌ | SÌ |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | spazi dei nomi | SÌ | SÌ |
> | namespaces / authorizationrules | No | No |
> | namespaces / disasterrecoveryconfigs | No | No |
> | namespaces / eventhubs | No | No |
> | namespaces / eventhubs / authorizationrules | No | No |
> | namespaces / eventhubs / consumergroups | No | No |
> | namespaces / networkrulesets | No | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | elastico | No | No |
> | provider | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | myareas / areas | No | No |
> | myareas / areas / areas | No | No |
> | myareas / areas / areas / galleryitems | No | No |
> | myareas / areas / galleryitems | No | No |
> | myareas / galleryitems | No | No |
> | register | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | SÌ | SÌ |
> | sapMonitors | SÌ | SÌ |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | SÌ | SÌ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | clusters / applications | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | SÌ | SÌ |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | SÌ | SÌ |
> | machines / extensions | SÌ | SÌ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | SÌ | SÌ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | SÌ | SÌ |
> | networkScopes | SÌ | SÌ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | SÌ | SÌ |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | SÌ | SÌ |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Grafo | SÌ | SÌ |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools | SÌ | SÌ |
> | insiemi di credenziali | SÌ | SÌ |
> | vaults / accessPolicies | No | No |
> | vaults / eventGridFilters | No | No |
> | vaults / secrets | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | clusters / attacheddatabaseconfigurations | No | No |
> | clusters / databases | No | No |
> | clusters / databases / dataconnections | No | No |
> | clusters / databases / eventhubconnections | No | No |
> | clusters / sharedidentities | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | SÌ | SÌ |
> | utenti | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | SÌ | SÌ |
> | integrationAccounts | SÌ | SÌ |
> | integrationServiceEnvironments | SÌ | SÌ |
> | integrationServiceEnvironments / managedApis | SÌ | SÌ |
> | isolatedEnvironments | SÌ | SÌ |
> | flussi di lavoro | SÌ | SÌ |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | SÌ | SÌ |
> | webServices | SÌ | SÌ |
> | Aree di lavoro | SÌ | SÌ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | SÌ | SÌ |
> | workspaces / computes | No | No |
> | workspaces / eventGridFilters | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | No | No |
> | userAssignedIdentities | SÌ | SÌ |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrationAssignments | No | No |
> | registrationDefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | No | No |
> | managementGroups | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | SÌ | SÌ |
> | accounts / eventGridFilters | No | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | No | No |
> | offerTypes | No | No |
> | offerTypes / publishers | No | No |
> | offerTypes / publishers / offers | No | No |
> | offerTypes / publishers / offers / plans | No | No |
> | offerTypes / publishers / offers / plans / agreements | No | No |
> | offerTypes / publishers / offers / plans / configs | No | No |
> | offerTypes / publishers / offers / plans / configs / importImage | No | No |
> | privategalleryitems | No | No |
> | products | No | No |
> | publishers | No | No |
> | publishers / offers | No | No |
> | publishers / offers / amendments | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | SÌ | SÌ |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | SÌ | SÌ |
> | mediaservices / accountFilters | No | No |
> | mediaservices / assets | No | No |
> | mediaservices / assets / assetFilters | No | No |
> | mediaservices / contentKeyPolicies | No | No |
> | mediaservices / eventGridFilters | No | No |
> | mediaservices / liveEventOperations | No | No |
> | mediaservices / liveEvents | SÌ | SÌ |
> | mediaservices / liveEvents / liveOutputs | No | No |
> | mediaservices / liveOutputOperations | No | No |
> | mediaservices / mediaGraphs | No | No |
> | mediaservices / streamingEndpointOperations | No | No |
> | mediaservices / streamingEndpoints | SÌ | SÌ |
> | mediaservices / streamingLocators | No | No |
> | mediaservices / streamingPolicies | No | No |
> | mediaservices / transforms | No | No |
> | mediaservices / transforms / jobs | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | SÌ | SÌ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | SÌ | SÌ |
> | migrateprojects | SÌ | SÌ |
> | projects | SÌ | SÌ |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | SÌ | SÌ |
> | objectUnderstandingAccounts | SÌ | SÌ |
> | remoteRenderingAccounts | SÌ | SÌ |
> | spatialAnchorsAccounts | SÌ | SÌ |
> | surfaceReconstructionAccounts | SÌ | SÌ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | SÌ | No |
> | netAppAccounts / capacityPools | SÌ | No |
> | netAppAccounts / capacityPools / volumes | SÌ | No |
> | netAppAccounts / capacityPools / volumes / mountTargets | SÌ | No |
> | netAppAccounts / capacityPools / volumes / snapshots | SÌ | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | dnszones / A | No | No |
> | dnszones / AAAA | No | No |
> | dnszones / all | No | No |
> | dnszones / CAA | No | No |
> | dnszones / CNAME | No | No |
> | dnszones / MX | No | No |
> | dnszones / NS | No | No |
> | dnszones / PTR | No | No |
> | dnszones / recordsets | No | No |
> | dnszones / SOA | No | No |
> | dnszones / SRV | No | No |
> | dnszones / TXT | No | No |
> | expressRouteCircuits | SÌ | SÌ |
> | expressRouteCrossConnections | SÌ | SÌ |
> | expressRouteGateways | SÌ | SÌ |
> | expressRoutePorts | SÌ | SÌ |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | SÌ | SÌ |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | SÌ |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | SÌ |
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
> | networkWatchers / connectionMonitors | SÌ | No |
> | networkWatchers / lenses | SÌ | No |
> | networkWatchers / pingMeshes | SÌ | No |
> | p2sVpnGateways | SÌ | SÌ |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | SÌ | SÌ |
> | privateDnsZones / A | No | No |
> | privateDnsZones / AAAA | No | No |
> | privateDnsZones / all | No | No |
> | privateDnsZones / CNAME | No | No |
> | privateDnsZones / MX | No | No |
> | privateDnsZones / PTR | No | No |
> | privateDnsZones / SOA | No | No |
> | privateDnsZones / SRV | No | No |
> | privateDnsZones / TXT | No | No |
> | privateDnsZones / virtualNetworkLinks | SÌ | SÌ |
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
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | No |
> | namespaces / notificationHubs | SÌ | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | SÌ | SÌ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | SÌ | SÌ |
> | ImportSites | SÌ | SÌ |
> | ServerSites | SÌ | SÌ |
> | VMwareSites | SÌ | SÌ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | SÌ | SÌ |
> | devices | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | aree di lavoro | SÌ | SÌ |
> | workspaces / dataSources | No | No |
> | workspaces / linkedServices | No | No |
> | workspaces / query | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | SÌ | SÌ |
> | solutions | SÌ | SÌ |
> | Viste | SÌ | SÌ |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | peerings | SÌ | SÌ |
> | peeringServiceProviders | No | No |
> | peeringServices | SÌ | SÌ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | No | No |
> | dashboards | SÌ | SÌ |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | SÌ | SÌ |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | SÌ | SÌ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | insiemi di credenziali | SÌ | SÌ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | SÌ |
> | namespaces / authorizationrules | No | No |
> | namespaces / hybridconnections | No | No |
> | namespaces / hybridconnections / authorizationrules | No | No |
> | namespaces / wcfrelays | No | No |
> | namespaces / wcfrelays / authorizationrules | No | No |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | account | No | No |
> | collections | SÌ | SÌ |
> | collections / applications | No | No |
> | collections / securityprincipals | No | No |
> | templateImages | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | query | SÌ | SÌ |
> | resourceChangeDetails | No | No |
> | resourceChanges | No | No |
> | resources | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
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
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | SÌ | No |
> | deployments / operations | No | No |
> | deploymentScripts | SÌ | SÌ |
> | deploymentScripts / logs | No | No |
> | links | No | No |
> | notifyResourceJobs | No | No |
> | provider | No | No |
> | resourceGroups | SÌ | No |
> | subscriptions | No | No |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | saasresources | No | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | SÌ | SÌ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | SÌ | SÌ |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | assessments | No | No |
> | autoDismissAlertsRules | No | No |
> | automations | SÌ | SÌ |
> | AutoProvisioningSettings | No | No |
> | Compliances | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotSecuritySolutions | SÌ | SÌ |
> | iotSecuritySolutions / analyticsModels | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No | No |
> | jitNetworkAccessPolicies | No | No |
> | networkData | No | No |
> | criteri | No | No |
> | pricings | No | No |
> | regulatoryComplianceStandards | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityAssessments | No | No |
> | Scheda Impostazioni | No | No |
> | subAssessments | No | No |
> | attività | No | No |
> | topologies | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | No | No |
> | alertRules | No | No |
> | alertRuleTemplates | No | No |
> | bookmarks | No | No |
> | cases | No | No |
> | dataConnectors | No | No |
> | entities | No | No |
> | entityQueries | No | No |
> | officeConsents | No | No |
> | Scheda Impostazioni | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | SÌ | No |
> | namespaces / authorizationrules | No | No |
> | namespaces / disasterrecoveryconfigs | No | No |
> | namespaces / eventgridfilters | No | No |
> | namespaces / networkrulesets | No | No |
> | namespaces / queues | No | No |
> | namespaces / queues / authorizationrules | No | No |
> | namespaces / topics | No | No |
> | namespaces / topics / authorizationrules | No | No |
> | namespaces / topics / subscriptions | No | No |
> | namespaces / topics / subscriptions / rules | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | clusters | SÌ | SÌ |
> | clusters / applications | No | No |
> | containerGroups | SÌ | SÌ |
> | containerGroupSets | SÌ | SÌ |
> | edgeclusters | SÌ | SÌ |
> | edgeclusters / applications | No | No |
> | networks | SÌ | SÌ |
> | secretstores | SÌ | SÌ |
> | secretstores / certificates | No | No |
> | secretstores / secrets | No | No |
> | volumes | SÌ | SÌ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicazioni | SÌ | SÌ |
> | containerGroups | SÌ | SÌ |
> | gateways | SÌ | SÌ |
> | networks | SÌ | SÌ |
> | chiavi private | SÌ | SÌ |
> | volumes | SÌ | SÌ |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
> | rollouts | SÌ | SÌ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | SÌ | SÌ |
> | SignalR / eventGridFilters | No | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | SÌ | SÌ |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | SÌ | SÌ |
> | applicazioni | SÌ | SÌ |
> | jitRequests | SÌ | SÌ |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | SÌ | SÌ |
> | managedInstances / databases | No | No |
> | managedInstances / databases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | No | No |
> | managedInstances / databases / vulnerabilityAssessments | No | No |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | No | No |
> | managedInstances / encryptionProtector | No | No |
> | managedInstances / keys | No | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / vulnerabilityAssessments | No | No |
> | server | SÌ | SÌ |
> | servers / administrators | No | No |
> | servers / communicationLinks | No | No |
> | servers / databases | Yes (see [note below](#sqlnote)) | SÌ |
> | servers / encryptionProtector | No | No |
> | servers / firewallRules | No | No |
> | servers / keys | No | No |
> | servers / restorableDroppedDatabases | No | No |
> | servers / serviceobjectives | No | No |
> | servers / tdeCertificates | No | No |
> | virtualClusters | No | No |

<a id="sqlnote" />

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | SÌ | SÌ |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | SÌ | SÌ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | SÌ | SÌ |
> | storageAccounts / blobServices | No | No |
> | storageAccounts / fileServices | No | No |
> | storageAccounts / queueServices | No | No |
> | storageAccounts / services | No | No |
> | storageAccounts / services / metricDefinitions | No | No |
> | storageAccounts / tableServices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | SÌ | SÌ |
> | caches / storageTargets | No | No |
> | usageModels | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / workflows | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SÌ | SÌ |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / workflows | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | SÌ | SÌ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sì (vedere la nota seguente) | SÌ |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | No | No |
> | CreateSubscription | No | No |
> | enable | No | No |
> | rename | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | SÌ | No |
> | environments / accessPolicies | No | No |
> | environments / eventsources | SÌ | No |
> | environments / referenceDataSets | SÌ | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | SÌ | SÌ |
> | dedicatedCloudServices | SÌ | SÌ |
> | virtualMachines | SÌ | SÌ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts / apiAcls | No | No |
> | apiManagementAccounts / apis | No | No |
> | apiManagementAccounts / apis / apiAcls | No | No |
> | apiManagementAccounts / apis / connectionAcls | No | No |
> | apiManagementAccounts / apis / connections | No | No |
> | apiManagementAccounts / apis / connections / connectionAcls | No | No |
> | apiManagementAccounts / apis / localizedDefinitions | No | No |
> | apiManagementAccounts / connectionAcls | No | No |
> | apiManagementAccounts / connections | No | No |
> | billingMeters | No | No |
> | certificates | SÌ | SÌ |
> | connectionGateways | SÌ | SÌ |
> | connections | SÌ | SÌ |
> | customApis | SÌ | SÌ |
> | deletedSites | No | No |
> | functions | No | No |
> | hostingEnvironments | SÌ | SÌ |
> | hostingEnvironments / multiRolePools | No | No |
> | hostingEnvironments / workerPools | No | No |
> | publishingUsers | No | No |
> | raccomandazioni di film | No | No |
> | resourceHealthMetadata | No | No |
> | runtimes | No | No |
> | serverFarms | SÌ | SÌ |
> | serverFarms / eventGridFilters | No | No |
> | siti | SÌ | SÌ |
> | sites / config  | No | No |
> | sites / eventGridFilters | No | No |
> | sites / hostNameBindings | No | No |
> | sites / networkConfig | No | No |
> | sites / premieraddons | SÌ | SÌ |
> | sites / slots | SÌ | SÌ |
> | sites / slots / eventGridFilters | No | No |
> | sites / slots / hostNameBindings | No | No |
> | sites / slots / networkConfig | No | No |
> | sourceControls | No | No |
> | validate | No | No |
> | verifyHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | SÌ | SÌ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | No | No |
> | componentsSummary | No | No |
> | monitorInstances | No | No |
> | monitorInstancesSummary | No | No |
> | monitors | No | No |
> | notificationSettings | No | No |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](resource-group-using-tags.md).
