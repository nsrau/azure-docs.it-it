---
title: Eliminazione in modalità completa di Azure Resource Manager in base al tipo di risorsa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: d8238dddee041573d9b122e62cec66118c681ef6
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995255"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa

Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

I tipi di risorsa contrassegnati con **Sì** vengono eliminati quando il tipo non è incluso nel modello distribuito con la modalità completa.

I tipi di risorsa contrassegnati con No non vengono eliminati automaticamente quando non sono **presenti** nel modello; Tuttavia, vengono eliminati se la risorsa padre viene eliminata. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).

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
> - [Microsoft. SQL](#microsoftsql)
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
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DomainServices | Sì |
> | DomainServices/oucontainer | No |
> | DomainServices/ReplicaSets | Sì |

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
> | report | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurazioni | No |
> | generateRecommendations | No |
> | Metadati | No |
> | raccomandazioni | No |
> | suppressions | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | actionRules | Sì |
> | avvisi | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | Feedback | No |
> | smartDetectorAlertRules | Yes |
> | smartDetectorRuntimeEnvironments | No |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | reportFeedback | No |
> | servizio | Yes |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | Sì |
> | configurationStores/eventGridFilters | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Molla | Yes |

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
> | dataalias | No |
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
> | automationAccounts/configurations | Sì |
> | automationAccounts/jobs | No |
> | automationAccounts/runbooks | Sì |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | Sì |
> | configurationStores/eventGridFilters | No |

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
> | b2ctenants | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridDataManagers | Sì |
> | postgresInstances | Sì |
> | sqlBigDataClusters | Sì |
> | SQLInstances | Yes |
> | sqlServerRegistrations | Sì |
> | sqlServerRegistrations/SQLServers | No |

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
> | batchAccounts | Sì |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/contratti | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/clienti | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | No |
> | billingAccounts/billingProfiles/invoiceSections/trasferimenti | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/clienti | No |
> | billingAccounts/Customers/billingSubscriptions | No |
> | billingAccounts/Customers/initiateTransfer | No |
> | billingAccounts/clienti/prodotti | No |
> | billingAccounts/clienti/transazioni | No |
> | billingAccounts/clienti/trasferimenti | No |
> | billingAccounts/departments | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts/invoices | No |
> | billingAccounts/invoiceSections | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/eleva | No |
> | billingAccounts/invoiceSections/initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections/productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceSections/Products/Transfer | No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/Transactions | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/products | No |
> | billingAccounts/transactions | No |
> | billingPeriods | No |
> | BillingPermissions | No |
> | billingProperty | No |
> | BillingRoleAssignments | No |
> | BillingRoleDefinitions | No |
> | CreateBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | transfers | No |
> | transfers/acceptTransfer | No |
> | transfers/declineTransfer | No |
> | transfers/operationStatus | No |
> | trasferimenti/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | BizTalk | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | visualizzazioni | Sì |

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
> | lingue | No |
> | modelli | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Redis | Sì |
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
> | reservationOrders/calculateRefund | No |
> | reservationOrders/merge | No |
> | reservationOrders/reservations | No |
> | reservationOrders/reservations/revisions | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | reservations | No |
> | risorse | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Sì |
> | edgenodes | No |
> | profiles | Yes |
> | profiles/endpoints | Sì |
> | profiles/endpoints/customdomains | No |
> | profiles/endpoints/origins | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Yes |
> | domainNames/capabilities | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames/slot/ruoli/metricDefinitions | No |
> | domainNames/slot/ruoli/metriche | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Sì |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrics | No |

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
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | dischi | No |
> | immagini | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Sì |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metriche | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/Servizi/metriche | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |

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
> | diskEncryptionSets | Sì |
> | dischi | Sì |
> | galleries | Sì |
> | raccolte/applicazioni | Sì |
> | raccolte/applicazioni/versioni | Sì |
> | galleries/images | Yes |
> | galleries/images/versions | Sì |
> | Gruppi host | Yes |
> | Gruppi host/host | Sì |
> | immagini | Sì |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Sì |
> | restorePointCollections/restorePoints | No |
> | sharedVMImages | Yes |
> | sharedVMImages/versions | Sì |
> | snapshot | Yes |
> | virtualMachines | Sì |
> | virtualMachines/extensions | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/scriptJobs | No |
> | virtualMachines/softwareUpdateDeployments | No |
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
> | tenants | No |
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
> | openShiftManagedClusters | Sì |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | Yes |
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
> | Budget | No |
> | CloudConnectors | No |
> | Connettori | Sì |
> | Departments | No |
> | Dimensioni | No |
> | EnrollmentAccounts | No |
> | Esportazioni | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/avvisi | No |
> | ExternalBillingAccounts/dimensioni | No |
> | ExternalBillingAccounts/previsione | No |
> | ExternalBillingAccounts/query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/avvisi | No |
> | ExternalSubscriptions/dimensioni | No |
> | ExternalSubscriptions/previsione | No |
> | ExternalSubscriptions/query | No |
> | Previsione | No |
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
> | richieste | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | associazioni | No |
> | resourceProviders | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | processi | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

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
> | datacatalogs | Sì |
> | datacatalogs/origini dati | No |
> | sottocataloghi/origini dati/analisi | No |
> | datacatalogs/DataSources/scansioni/set di dati | No |
> | datacatalogs/origini dati/analisi/trigger | No |
> | datacatalogs/scantargets | No |
> | datacatalogs/scantargets/DataSets | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataFactories | Sì |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | Sì |
> | factories/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Yes |
> | accounts/dataLakeStoreAccounts | No |
> | accounts/storageAccounts | No |
> | accounts/storageAccounts/containers | No |
> | account/transferAnalyticsUnits | No |

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
> | services/projects | Sì |
> | slot | Sì |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | account/condivisioni | No |
> | account/condivisioni/set di impostazioni | No |
> | account/condivisioni/inviti | No |
> | Accounts/shares/providersharesubscriptions | No |
> | Accounts/shares/synchronizationSettings | No |
> | account/sharesubscriptions | No |
> | account/sharesubscriptions/consumerSourceDataSets | No |
> | account/sharesubscriptions/datasetmappings | No |
> | account/sharesubscriptions/trigger | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | Yes |
> | servers/advisors | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server | Yes |
> | servers/advisors | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | serverGroups | Yes |
> | server | Sì |
> | servers/advisors | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |
> | serversv2 | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | artifactSources | Sì |
> | rollouts | Sì |
> | serviceTopologies | Sì |
> | serviceTopologies/servizi | Sì |
> | serviceTopologies/Services/serviceUnits | Yes |
> | steps | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/applicazioni | No |
> | applicationgroups/startmenuitems | No |
> | hostpools | Sì |
> | hostpools/sessionhosts | No |
> | hostpools/sessionhosts/usersessions | No |
> | hostpools/usersessions | No |
> | aree di lavoro | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | elasticPools | Sì |
> | ElasticPools/IotHubTenants | Yes |
> | Hub IoT | Sì |
> | IotHubs/eventGridFilters | No |
> | ProvisioningServices | Sì |
> | usages | No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | pipeline | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | controllers | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labcenters | Yes |
> | labs | Sì |
> | Lab/ambienti | Sì |
> | labs/serviceRunners | Yes |
> | labs/virtualMachines | Sì |
> | schedules | Sì |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Sì |

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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | Sì |
> | domains/topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | topics | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | spazi dei nomi | Sì |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventhubs | No |
> | namespaces/eventhubs/authorizationrules | No |
> | namespaces/eventhubs/consumergroups | No |
> | spazi dei nomi/networkrulesets | No |

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
> | risorse | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Sì |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Yes |
> | clusters/applications | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | computer | Sì |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | datamanager | Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | components | Sì |
> | networkScopes | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | processi | Sì |

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
> | hsmPools | Yes |
> | insiemi di credenziali | Yes |
> | vaults/accessPolicies | No |
> | insiemi di credenziali/eventGridFilters | No |
> | vaults/secrets | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | cluster/attacheddatabaseconfigurations | No |
> | clusters/databases | No |
> | clusters/databases/dataconnections | No |
> | clusters/databases/eventhubconnections | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | Utenti | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Sì |
> | isolatedEnvironments | Sì |
> | flussi di lavoro | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | commitmentPlans | Sì |
> | webServices | Sì |
> | Aree di lavoro | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | Yes |
> | workspaces/computes | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Identità | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

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
> | risorse | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Yes |
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
> | editori | No |
> | editori/offerte | No |
> | editori/offerte/modifiche | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicDevServices | Yes |
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
> | mediaservices | Yes |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices/assets/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Sì |
> | mediaservices/liveEvents/liveOutputs | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transforms | No |
> | mediaservices/transforms/jobs | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appClusters | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | assessmentProjects | Sì |
> | migrateprojects | Yes |
> | projects | Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sì |
> | objectUnderstandingAccounts | Sì |
> | remoteRenderingAccounts | Sì |
> | spatialAnchorsAccounts | Yes |
> | surfaceReconstructionAccounts | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | netAppAccounts | Sì |
> | netAppAccounts/capacityPools | Sì |
> | netAppAccounts/capacityPools/volumi | Sì |
> | netAppAccounts/capacityPools/Volumes/mountTargets | Sì |
> | netAppAccounts/capacityPools/volumi/snapshot | Sì |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationGateways | Sì |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Sì |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Sì |
> | bastionHosts | Sì |
> | bgpServiceCommunities | No |
> | connessioni | Sì |
> | ddosCustomPolicies | Yes |
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
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Sì |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoor | Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Sì |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | loadBalancers | Yes |
> | localNetworkGateways | Sì |
> | natGateways | Sì |
> | networkIntentPolicies | Sì |
> | networkInterfaces | Yes |
> | networkProfiles | Sì |
> | networkSecurityGroups | Yes |
> | networkWatchers | Sì |
> | networkWatchers/connectionMonitors | Sì |
> | networkWatchers/lenses | Yes |
> | networkWatchers/pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/tutti | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Sì |
> | privateEndpoints | Sì |
> | privateLinkServices | Sì |
> | publicIPAddresses | Sì |
> | publicIPPrefixes | Sì |
> | routeFilters | Sì |
> | routeTables | Yes |
> | secureGateways | Sì |
> | serviceEndpointPolicies | Sì |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Sì |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Sì |
> | virtualNetworks | Yes |
> | virtualNetworkTaps | Sì |
> | virtualWans | Sì |
> | vpnGateways | Sì |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Sì |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | namespaces/notificationHubs | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | HyperVSites | Sì |
> | ImportSites | Yes |
> | ServerSites | Sì |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | dispositivi | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | aree di lavoro | Yes |
> | workspaces/dataSources | No |
> | workspaces/linkedServices | No |
> | workspaces/query | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Sì |
> | solutions | Sì |
> | visualizzazioni | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peering | Yes |
> | peeringServiceProviders | No |
> | peeringServices | Sì |

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

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | No |
> | Collezioni | Yes |
> | raccolte/applicazioni | No |
> | raccolte/SecurityPrincipals | No |
> | templateImages | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | query | Sì |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | risorse | No |
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
> | Metadati | No |
> | notifiche | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | distribuzioni | No |
> | deployments/operations | No |
> | links | No |
> | notifyResourceJobs | No |
> | provider | No |
> | resourceGroups | No |
> | risorse | No |
> | sottoscrizioni | No |
> | subscriptions/providers | No |
> | subscriptions/resourceGroups | No |
> | subscriptions/resourcegroups/resources | No |
> | subscriptions/resources | No |
> | subscriptions/tagnames | No |
> | subscriptions/tagNames/tagValues | No |
> | tags | No |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | Sì |
> | saasresources | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobcollections | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | avvisi | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | valutazioni | No |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions/analyticsModels | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | jitNetworkAccessPolicies | No |
> | File networkdata | No |
> | playbookConfigurations | Yes |
> | criteri | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | impostazioni | No |
> | attività | No |
> | topologies | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | le aggregazioni | No |
> | Regole di avviso | No |
> | alertRuleTemplates | No |
> | Segnalibri | No |
> | cases | No |
> | DataConnector | No |
> | entità | No |
> | entityQueries | No |
> | officeConsents | No |
> | impostazioni | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventgridfilters | No |
> | spazi dei nomi/networkrulesets | No |
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
> | applicazioni | Yes |
> | clusters | Sì |
> | clusters/applications | No |
> | containerGroups | Yes |
> | containerGroupSets | Sì |
> | edgeclusters | Sì |
> | edgeclusters/applicazioni | No |
> | Reti | Sì |
> | secretstores | Sì |
> | secretstores/certificati | No |
> | secretstores/segreti | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicazioni | Sì |
> | containerGroups | Sì |
> | gateways | Sì |
> | Reti | Yes |
> | segreti | Yes |
> | volumes | Sì |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/resourceTypeRegistrations | No |
> | rollouts | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SignalR | Sì |
> | SignalR/eventGridFilters | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sì |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationDefinitions | Sì |
> | applicazioni | Sì |
> | jitRequests | Sì |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedInstances | Sì |
> | managedInstances/databases | Sì |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | server | Yes |
> | servers/administrators | No |
> | servers/communicationLinks | No |
> | servers/databases | Yes |
> | servers/encryptionProtector | No |
> | servers/firewallRules | No |
> | servers/keys | No |
> | servers/restorableDroppedDatabases | No |
> | servers/serviceobjectives | No |
> | servers/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sì |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageAccounts | Sì |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cache | Sì |
> | cache/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managers | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | annulla | No |
> | CreateSubscription | No |
> | enable | No |
> | rinomina | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | Sì |
> | environments/accessPolicies | No |
> | environments/eventsources | Sì |
> | environments/referenceDataSets | Sì |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sì |
> | dedicatedCloudServices | Sì |
> | virtualMachines | Sì |

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
> | certificati | Yes |
> | connectionGateways | Sì |
> | connessioni | Sì |
> | customApis | Yes |
> | deletedSites | No |
> | funzioni | No |
> | hostingEnvironments | Sì |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | publishingUsers | No |
> | raccomandazioni | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | siti | Sì |
> | siti/configurazione  | No |
> | siti/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | siti/file networkconfig | No |
> | sites/premieraddons | Sì |
> | sites/slots | Sì |
> | siti/slot/eventGridFilters | No |
> | sites/slots/hostNameBindings | No |
> | siti/slot/file networkconfig | No |
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
> | DeviceServices | Yes |

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
