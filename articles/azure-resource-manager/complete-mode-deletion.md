---
title: Eliminazione in modalità completa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232680"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa

Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DomainServices | SÌ |
> | DomainServices / oucontainer | No |

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
> | log | No |
> | reports | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurazioni | No |
> | generateRecommendations | No |
> | metadata | No |
> | raccomandazioni di film | No |
> | suppressions | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | actionRules | SÌ |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | feedback | No |
> | smartDetectorAlertRules | SÌ |
> | smartDetectorRuntimeEnvironments | No |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | SÌ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | reportFeedback | No |
> | servizio | SÌ |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | SÌ |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Spring | SÌ |

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
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
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
> | automationAccounts | SÌ |
> | automationAccounts / configurations | SÌ |
> | automationAccounts / jobs | No |
> | automationAccounts / runbooks | SÌ |
> | automationAccounts / softwareUpdateConfigurations | No |
> | automationAccounts / webhooks | No |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | SÌ |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | No |
> | environments / accounts | No |
> | environments / accounts / namespaces | No |
> | environments / accounts / namespaces / configurations | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | b2cDirectories | SÌ |
> | b2ctenants | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridDataManagers | SÌ |
> | postgresInstances | SÌ |
> | sqlBigDataClusters | SÌ |
> | sqlInstances | SÌ |
> | sqlServerRegistrations | SÌ |
> | sqlServerRegistrations / sqlServers | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registrations | SÌ |
> | registrations / customerSubscriptions | No |
> | registrations / products | No |
> | verificationKeys | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | batchAccounts | SÌ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts / agreements | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / customers | No |
> | billingAccounts / billingProfiles / invoices | No |
> | billingAccounts / billingProfiles / invoices / pricesheet | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products | No |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No |
> | billingAccounts / billingProfiles / invoiceSections / transactions | No |
> | billingAccounts / billingProfiles / invoiceSections / transfers | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts / billingProfiles / policies | No |
> | billingAccounts / billingProfiles / pricesheet | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts / billingProfiles / products | No |
> | billingAccounts / billingProfiles / transactions | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts / billingSubscriptions / invoices | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts / customers | No |
> | billingAccounts / customers / billingPermissions | No |
> | billingAccounts / customers / billingSubscriptions | No |
> | billingAccounts / customers / initiateTransfer | No |
> | billingAccounts / customers / policies | No |
> | billingAccounts / customers / products | No |
> | billingAccounts / customers / transactions | No |
> | billingAccounts / customers / transfers | No |
> | billingAccounts / departments | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / invoices | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | No |
> | billingAccounts / invoiceSections / elevate | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccounts / invoiceSections / products | No |
> | billingAccounts / invoiceSections / products / transfer | No |
> | billingAccounts / invoiceSections / products / updateAutoRenew | No |
> | billingAccounts / invoiceSections / transactions | No |
> | billingAccounts / invoiceSections / transfers | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts / products | No |
> | billingAccounts / transactions | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | transfers | No |
> | transfers / acceptTransfer | No |
> | transfers / declineTransfer | No |
> | transfers / operationStatus | No |
> | transfers / validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mapApis | SÌ |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blockchainMembers | SÌ |
> | cordaMembers | SÌ |
> | visualizzazioni | SÌ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments / operations | No |
> | blueprints | No |
> | blueprints / artifacts | No |
> | blueprints / versions | No |
> | blueprints / versions / artifacts | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | botServices | SÌ |
> | botServices / channels | No |
> | botServices / connections | No |
> | lingue | No |
> | Modelli | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Redis | SÌ |
> | RedisConfigDefinition | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appliedReservations | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | catalogs | No |
> | commercialReservationOrders | No |
> | exchange | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders / merge | No |
> | reservationOrders / reservations | No |
> | reservationOrders / reservations / revisions | No |
> | reservationOrders / return | No |
> | reservationOrders / split | No |
> | reservationOrders / swap | No |
> | reservations | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | SÌ |
> | edgenodes | No |
> | Profili | SÌ |
> | profiles / endpoints | SÌ |
> | profiles / endpoints / customdomains | No |
> | profiles / endpoints / origins | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | certificateOrders | SÌ |
> | certificateOrders / certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | SÌ |
> | domainNames / capabilities | No |
> | domainNames / internalLoadBalancers | No |
> | domainNames / serviceCertificates | No |
> | domainNames / slots | No |
> | domainNames / slots / roles | No |
> | domainNames / slots / roles / metricDefinitions | No |
> | domainNames / slots / roles / metrics | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | SÌ |
> | virtualMachines / diagnosticSettings | No |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / metrics | No |

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
> | expressRouteCrossConnections / peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | SÌ |
> | quotas | No |
> | reservedIps | SÌ |
> | virtualNetworks | SÌ |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | dischi | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | SÌ |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / metricDefinitions | No |
> | storageAccounts / metrics | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / services | No |
> | storageAccounts / services / diagnosticSettings | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts / services / metrics | No |
> | storageAccounts / tableServices | No |
> | storageAccounts / vmImages | No |
> | vmImages | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |

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
> | availabilitySets | SÌ |
> | diskEncryptionSets | SÌ |
> | dischi | SÌ |
> | galleries | SÌ |
> | galleries / applications | No |
> | galleries / applications / versions | No |
> | galleries / images | No |
> | galleries / images / versions | No |
> | hostGroups | SÌ |
> | hostGroups / hosts | SÌ |
> | images | SÌ |
> | proximityPlacementGroups | SÌ |
> | restorePointCollections | SÌ |
> | restorePointCollections / restorePoints | No |
> | sharedVMImages | SÌ |
> | sharedVMImages / versions | No |
> | snapshots | SÌ |
> | virtualMachines | SÌ |
> | virtualMachines / extensions | SÌ |
> | virtualMachines / metricDefinitions | No |
> | virtualMachineScaleSets | SÌ |
> | virtualMachineScaleSets / extensions | No |
> | virtualMachineScaleSets / networkInterfaces | No |
> | virtualMachineScaleSets / publicIPAddresses | No |
> | virtualMachineScaleSets / virtualMachines | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No |

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
> | tenants | No |
> | Termini | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerGroups | SÌ |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registries | SÌ |
> | registries / builds | No |
> | registries / builds / cancel | No |
> | registries / builds / getLogLink | No |
> | registries / buildTasks | SÌ |
> | registries / buildTasks / steps | No |
> | registries / eventGridFilters | No |
> | registries / generateCredentials | No |
> | registries / getBuildSourceUploadUrl | No |
> | registries / GetCredentials | No |
> | registries / importImage | No |
> | registries / queueBuild | No |
> | registries / regenerateCredential | No |
> | registries / regenerateCredentials | No |
> | registries / replications | SÌ |
> | registries / runs | No |
> | registries / runs / cancel | No |
> | registries / scheduleRun | No |
> | registries / scopeMaps | No |
> | registries / taskRuns | SÌ |
> | registries / tasks | SÌ |
> | registries / tokens | No |
> | registries / updatePolicies | No |
> | registries / webhooks | SÌ |
> | registries / webhooks / getCallbackConfig | No |
> | registries / webhooks / ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerServices | SÌ |
> | managedClusters | SÌ |
> | openShiftManagedClusters | SÌ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Avvisi | No |
> | BillingAccounts | No |
> | Budget | No |
> | CloudConnectors | No |
> | Connettori | SÌ |
> | Departments | No |
> | Dimensioni | No |
> | EnrollmentAccounts | No |
> | Esportazioni | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts / Alerts | No |
> | ExternalBillingAccounts / Dimensions | No |
> | ExternalBillingAccounts / Forecast | No |
> | ExternalBillingAccounts / Query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions / Alerts | No |
> | ExternalSubscriptions / Dimensions | No |
> | ExternalSubscriptions / Forecast | No |
> | ExternalSubscriptions / Query | No |
> | Forecast | No |
> | Query | No |
> | register | No |
> | Reportconfigs | No |
> | Report | No |
> | Impostazioni | No |
> | showbackRules | No |
> | Visualizzazioni | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | requests | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | associations | No |
> | resourceProviders | SÌ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | SÌ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | SÌ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | SÌ |
> | workspaces / virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | catalogs | SÌ |
> | datacatalogs | SÌ |
> | datacatalogs / datasources | No |
> | datacatalogs / datasources / scans | No |
> | datacatalogs / datasources / scans / datasets | No |
> | datacatalogs / datasources / scans / triggers | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataFactories | SÌ |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | SÌ |
> | factories / integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |
> | accounts / dataLakeStoreAccounts | No |
> | accounts / storageAccounts | No |
> | accounts / storageAccounts / containers | No |
> | accounts / transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |
> | accounts / eventGridFilters | No |
> | accounts / firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | SÌ |
> | services / projects | SÌ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |
> | accounts / shares | No |
> | accounts / shares / datasets | No |
> | accounts / shares / invitations | No |
> | accounts / shares / providersharesubscriptions | No |
> | accounts / shares / synchronizationSettings | No |
> | accounts / sharesubscriptions | No |
> | accounts / sharesubscriptions / consumerSourceDataSets | No |
> | accounts / sharesubscriptions / datasetmappings | No |
> | accounts / sharesubscriptions / triggers | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | SÌ |
> | servers / advisors | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers / privateEndpointConnections | No |
> | servers / privateLinkResources | No |
> | servers / queryTexts | No |
> | servers / recoverableServers | No |
> | servers / topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | servers / waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | SÌ |
> | servers / advisors | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers / privateEndpointConnections | No |
> | servers / privateLinkResources | No |
> | servers / queryTexts | No |
> | servers / recoverableServers | No |
> | servers / topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | servers / waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | serverGroups | SÌ |
> | server | SÌ |
> | servers / advisors | No |
> | servers / keys | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers / privateEndpointConnections | No |
> | servers / privateLinkResources | No |
> | servers / queryTexts | No |
> | servers / recoverableServers | No |
> | servers / topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | servers / waitStatistics | No |
> | serversv2 | SÌ |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | artifactSources | SÌ |
> | rollouts | SÌ |
> | serviceTopologies | SÌ |
> | serviceTopologies / services | SÌ |
> | serviceTopologies / services / serviceUnits | SÌ |
> | steps | SÌ |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationgroups | SÌ |
> | applicationgroups / applications | No |
> | applicationgroups / desktops | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | SÌ |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | aree di lavoro | SÌ |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | ElasticPools | SÌ |
> | ElasticPools / IotHubTenants | SÌ |
> | Hub IoT | SÌ |
> | IotHubs / eventGridFilters | No |
> | ProvisioningServices | SÌ |
> | usages | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | pipelines | SÌ |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | controllers | SÌ |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labcenters | SÌ |
> | labs | SÌ |
> | labs / environments | SÌ |
> | labs / serviceRunners | SÌ |
> | labs / virtualMachines | SÌ |
> | schedules | SÌ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | SÌ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | SÌ |
> | domains / domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects / connectors | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | SÌ |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | SÌ |
> | domains / topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | topics | SÌ |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | SÌ |
> | spazi dei nomi | SÌ |
> | namespaces / authorizationrules | No |
> | namespaces / disasterrecoveryconfigs | No |
> | namespaces / eventhubs | No |
> | namespaces / eventhubs / authorizationrules | No |
> | namespaces / eventhubs / consumergroups | No |
> | namespaces / networkrulesets | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | elastico | No |
> | provider | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas / areas | No |
> | myareas / areas / areas | No |
> | myareas / areas / areas / galleryitems | No |
> | myareas / areas / galleryitems | No |
> | myareas / galleryitems | No |
> | register | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | SÌ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hanaInstances | SÌ |
> | sapMonitors | SÌ |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedHSMs | SÌ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | SÌ |
> | clusters / applications | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | SÌ |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | machines | SÌ |
> | machines / extensions | SÌ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataManagers | SÌ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | components | SÌ |
> | networkScopes | SÌ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | SÌ |

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
> | appTemplates | No |
> | IoTApps | SÌ |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Grafo | SÌ |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deletedVaults | No |
> | hsmPools | SÌ |
> | insiemi di credenziali | SÌ |
> | vaults / accessPolicies | No |
> | vaults / eventGridFilters | No |
> | vaults / secrets | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | SÌ |
> | clusters / attacheddatabaseconfigurations | No |
> | clusters / databases | No |
> | clusters / databases / dataconnections | No |
> | clusters / databases / eventhubconnections | No |
> | clusters / sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labaccounts | SÌ |
> | utenti | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hostingEnvironments | SÌ |
> | integrationAccounts | SÌ |
> | integrationServiceEnvironments | SÌ |
> | integrationServiceEnvironments / managedApis | SÌ |
> | isolatedEnvironments | SÌ |
> | flussi di lavoro | SÌ |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | commitmentPlans | SÌ |
> | webServices | SÌ |
> | Aree di lavoro | SÌ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | SÌ |
> | workspaces / computes | No |
> | workspaces / eventGridFilters | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | SÌ |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

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
> | account | SÌ |
> | accounts / eventGridFilters | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | offers | No |
> | offerTypes | No |
> | offerTypes / publishers | No |
> | offerTypes / publishers / offers | No |
> | offerTypes / publishers / offers / plans | No |
> | offerTypes / publishers / offers / plans / agreements | No |
> | offerTypes / publishers / offers / plans / configs | No |
> | offerTypes / publishers / offers / plans / configs / importImage | No |
> | privategalleryitems | No |
> | products | No |
> | publishers | No |
> | publishers / offers | No |
> | publishers / offers / amendments | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicDevServices | SÌ |
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
> | mediaservices | SÌ |
> | mediaservices / accountFilters | No |
> | mediaservices / assets | No |
> | mediaservices / assets / assetFilters | No |
> | mediaservices / contentKeyPolicies | No |
> | mediaservices / eventGridFilters | No |
> | mediaservices / liveEventOperations | No |
> | mediaservices / liveEvents | SÌ |
> | mediaservices / liveEvents / liveOutputs | No |
> | mediaservices / liveOutputOperations | No |
> | mediaservices / mediaGraphs | No |
> | mediaservices / streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | SÌ |
> | mediaservices / streamingLocators | No |
> | mediaservices / streamingPolicies | No |
> | mediaservices / transforms | No |
> | mediaservices / transforms / jobs | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appClusters | SÌ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | assessmentProjects | SÌ |
> | migrateprojects | SÌ |
> | projects | SÌ |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | SÌ |
> | objectUnderstandingAccounts | SÌ |
> | remoteRenderingAccounts | SÌ |
> | spatialAnchorsAccounts | SÌ |
> | surfaceReconstructionAccounts | SÌ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | netAppAccounts | SÌ |
> | netAppAccounts / capacityPools | SÌ |
> | netAppAccounts / capacityPools / volumes | SÌ |
> | netAppAccounts / capacityPools / volumes / mountTargets | SÌ |
> | netAppAccounts / capacityPools / volumes / snapshots | SÌ |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationGateways | SÌ |
> | applicationGatewayWebApplicationFirewallPolicies | SÌ |
> | applicationSecurityGroups | SÌ |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | SÌ |
> | bastionHosts | SÌ |
> | bgpServiceCommunities | No |
> | connections | SÌ |
> | ddosCustomPolicies | SÌ |
> | ddosProtectionPlans | SÌ |
> | dnsOperationStatuses | No |
> | dnszones | SÌ |
> | dnszones / A | No |
> | dnszones / AAAA | No |
> | dnszones / all | No |
> | dnszones / CAA | No |
> | dnszones / CNAME | No |
> | dnszones / MX | No |
> | dnszones / NS | No |
> | dnszones / PTR | No |
> | dnszones / recordsets | No |
> | dnszones / SOA | No |
> | dnszones / SRV | No |
> | dnszones / TXT | No |
> | expressRouteCircuits | SÌ |
> | expressRouteCrossConnections | SÌ |
> | expressRouteGateways | SÌ |
> | expressRoutePorts | SÌ |
> | expressRouteServiceProviders | No |
> | firewallPolicies | SÌ |
> | frontdoors | SÌ |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | SÌ |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | loadBalancers | SÌ |
> | localNetworkGateways | SÌ |
> | natGateways | SÌ |
> | networkIntentPolicies | SÌ |
> | networkInterfaces | SÌ |
> | networkProfiles | SÌ |
> | networkSecurityGroups | SÌ |
> | networkWatchers | SÌ |
> | networkWatchers / connectionMonitors | SÌ |
> | networkWatchers / lenses | SÌ |
> | networkWatchers / pingMeshes | SÌ |
> | p2sVpnGateways | SÌ |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | SÌ |
> | privateDnsZones / A | No |
> | privateDnsZones / AAAA | No |
> | privateDnsZones / all | No |
> | privateDnsZones / CNAME | No |
> | privateDnsZones / MX | No |
> | privateDnsZones / PTR | No |
> | privateDnsZones / SOA | No |
> | privateDnsZones / SRV | No |
> | privateDnsZones / TXT | No |
> | privateDnsZones / virtualNetworkLinks | SÌ |
> | privateEndpoints | SÌ |
> | privateLinkServices | SÌ |
> | publicIPAddresses | SÌ |
> | publicIPPrefixes | SÌ |
> | routeFilters | SÌ |
> | routeTables | SÌ |
> | serviceEndpointPolicies | SÌ |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | SÌ |
> | trafficmanagerprofiles / heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | SÌ |
> | virtualNetworkGateways | SÌ |
> | virtualNetworks | SÌ |
> | virtualNetworkTaps | SÌ |
> | virtualWans | SÌ |
> | vpnGateways | SÌ |
> | vpnSites | SÌ |
> | webApplicationFirewallPolicies | SÌ |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | SÌ |
> | namespaces / notificationHubs | SÌ |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | osNamespaces | SÌ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | HyperVSites | SÌ |
> | ImportSites | SÌ |
> | ServerSites | SÌ |
> | VMwareSites | SÌ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | SÌ |
> | devices | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | aree di lavoro | SÌ |
> | workspaces / dataSources | No |
> | workspaces / linkedServices | No |
> | workspaces / query | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | SÌ |
> | solutions | SÌ |
> | Viste | SÌ |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | SÌ |
> | peeringServiceProviders | No |
> | peeringServices | SÌ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | remediations | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | SÌ |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | workspaceCollections | SÌ |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capacities | SÌ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | insiemi di credenziali | SÌ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | SÌ |
> | namespaces / authorizationrules | No |
> | namespaces / hybridconnections | No |
> | namespaces / hybridconnections / authorizationrules | No |
> | namespaces / wcfrelays | No |
> | namespaces / wcfrelays / authorizationrules | No |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | No |
> | collections | SÌ |
> | collections / applications | No |
> | collections / securityprincipals | No |
> | templateImages | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | query | SÌ |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
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
> | metadata | No |
> | Notifiche | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deployments | No |
> | deployments / operations | No |
> | deploymentScripts | SÌ |
> | deploymentScripts / logs | No |
> | links | No |
> | notifyResourceJobs | No |
> | provider | No |
> | resourceGroups | No |
> | resources | No |
> | subscriptions | No |
> | subscriptions / providers | No |
> | subscriptions / resources | No |
> | subscriptions / tagnames | No |
> | subscriptions / tagNames / tagValues | No |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | SÌ |
> | saasresources | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobcollections | SÌ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | SÌ |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | assessments | No |
> | autoDismissAlertsRules | No |
> | automations | SÌ |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | SÌ |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | jitNetworkAccessPolicies | No |
> | networkData | No |
> | criteri | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | Scheda Impostazioni | No |
> | subAssessments | No |
> | attività | No |
> | topologies | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aggregations | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | bookmarks | No |
> | cases | No |
> | dataConnectors | No |
> | entities | No |
> | entityQueries | No |
> | officeConsents | No |
> | Scheda Impostazioni | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | SÌ |
> | namespaces / authorizationrules | No |
> | namespaces / disasterrecoveryconfigs | No |
> | namespaces / eventgridfilters | No |
> | namespaces / networkrulesets | No |
> | namespaces / queues | No |
> | namespaces / queues / authorizationrules | No |
> | namespaces / topics | No |
> | namespaces / topics / authorizationrules | No |
> | namespaces / topics / subscriptions | No |
> | namespaces / topics / subscriptions / rules | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | SÌ |
> | clusters | SÌ |
> | clusters / applications | No |
> | containerGroups | SÌ |
> | containerGroupSets | SÌ |
> | edgeclusters | SÌ |
> | edgeclusters / applications | No |
> | networks | SÌ |
> | secretstores | SÌ |
> | secretstores / certificates | No |
> | secretstores / secrets | No |
> | volumes | SÌ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | SÌ |
> | containerGroups | SÌ |
> | gateways | SÌ |
> | networks | SÌ |
> | chiavi private | SÌ |
> | volumes | SÌ |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | rollouts | SÌ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SignalR | SÌ |
> | SignalR / eventGridFilters | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SiteRecoveryVault | SÌ |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationDefinitions | SÌ |
> | applicazioni | SÌ |
> | jitRequests | SÌ |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedInstances | SÌ |
> | managedInstances / databases | SÌ |
> | managedInstances / databases / backupShortTermRetentionPolicies | No |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | No |
> | managedInstances / databases / vulnerabilityAssessments | No |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances / keys | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilityAssessments | No |
> | server | SÌ |
> | servers / administrators | No |
> | servers / communicationLinks | No |
> | servers / databases | SÌ |
> | servers / encryptionProtector | No |
> | servers / firewallRules | No |
> | servers / keys | No |
> | servers / restorableDroppedDatabases | No |
> | servers / serviceobjectives | No |
> | servers / tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | SÌ |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | SÌ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageAccounts | SÌ |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / services | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts / tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | caches | SÌ |
> | caches / storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | SÌ |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / workflows | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | SÌ |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / workflows | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | SÌ |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managers | SÌ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | streamingjobs | SÌ |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cancel | No |
> | CreateSubscription | No |
> | enable | No |
> | rename | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | SÌ |
> | environments / accessPolicies | No |
> | environments / eventsources | SÌ |
> | environments / referenceDataSets | SÌ |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedCloudNodes | SÌ |
> | dedicatedCloudServices | SÌ |
> | virtualMachines | SÌ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts / apis | No |
> | apiManagementAccounts / apis / apiAcls | No |
> | apiManagementAccounts / apis / connectionAcls | No |
> | apiManagementAccounts / apis / connections | No |
> | apiManagementAccounts / apis / connections / connectionAcls | No |
> | apiManagementAccounts / apis / localizedDefinitions | No |
> | apiManagementAccounts / connectionAcls | No |
> | apiManagementAccounts / connections | No |
> | billingMeters | No |
> | certificates | SÌ |
> | connectionGateways | SÌ |
> | connections | SÌ |
> | customApis | SÌ |
> | deletedSites | No |
> | functions | No |
> | hostingEnvironments | SÌ |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | publishingUsers | No |
> | raccomandazioni di film | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | SÌ |
> | serverFarms / eventGridFilters | No |
> | siti | SÌ |
> | sites/config  | No |
> | sites / eventGridFilters | No |
> | sites / hostNameBindings | No |
> | sites / networkConfig | No |
> | sites / premieraddons | SÌ |
> | sites / slots | SÌ |
> | sites / slots / eventGridFilters | No |
> | sites / slots / hostNameBindings | No |
> | sites / slots / networkConfig | No |
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
> | DeviceServices | SÌ |

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
