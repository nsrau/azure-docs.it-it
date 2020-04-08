---
title: Eliminazione in modalità completa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802570"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa

Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

I tipi di risorsa contrassegnati con **Sì** vengono eliminati quando il tipo non è nel modello distribuito con la modalità completa.

I tipi di risorsa contrassegnati con **No** non vengono eliminati automaticamente quando non si è nel modello. Tuttavia, vengono eliminati se la risorsa padre viene eliminata. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).

Se si distribuisce in più di un gruppo di [risorse in un modello,](cross-resource-group-deployment.md)le risorse nel gruppo di risorse specificato nell'operazione di distribuzione sono idonee per l'eliminazione. Le risorse nei gruppi di risorse secondarie non vengono eliminate.

Passare a uno spazio dei nomi del provider di risorse:Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration (Configurazione applicazioni)](#microsoftappconfiguration)
> - [Microsoft.AppPlatform (Piattaforma App)](#microsoftappplatform)
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
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork (rete classica)](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumo](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders (informazioni in base ai provider personalizzati)](#microsoftcustomproviders)
> - [Informazioni su 200](#microsoftdatabox)
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
> - [Microsoft.DesktopVirtualization (applicazione di virtualizzazione)](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps (informazioni in base al sistema operativo Microsoft.Dev](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Falcon](#microsoftfalcon)
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
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Manutenzione](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring (prodotti Microsoft.Microservices4Spring)](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality (realtà di Microsoft)](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Informazioni su Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp (informazioni in remoto)](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Servizi](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan (piano)](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Servizio Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL (informazioni in lingua inglese)](#microsoftsql)
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
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DomainServices | Sì |
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
> | actionRules | Sì |
> | alerts | No |
> | alertsList | No |
> | avvisiMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Sì |
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
> | service | Sì |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration (Configurazione applicazioni)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | Sì |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform (Piattaforma App)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Spring | Sì |

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
> | dataAlias | No |
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
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | automationAccounts | Sì |
> | automationAccount / configurazioni | Sì |
> | automationAccount / lavori | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccount / runbook | Sì |
> | automationAccount / softwareUpdateConfigurations | No |
> | automationAccount / webhook | No |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | Sì |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | No |
> | ambienti / account | No |
> | ambienti/ account / spazi dei nomi | No |
> | ambienti / account / spazi dei nomi / configurazioni | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | b2cDirectories | Sì |
> | b2ctenant | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridDataManagers | Sì |
> | postgresIi | Sì |
> | sqlInstances (istanza di sqlInstances) | Sì |
> | Sqlserverregistrations | Sì |
> | sqlServerRegistrations / sqlServers | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cloudManifestFiles (file cloudManifestFiles) | No |
> | registrations | Sì |
> | registrazioni / clientiSottoscrizioni | No |
> | registrazioni / prodotti | No |

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
> | billingAccount / accordi | No |
> | billingAccount / billingPermissions | No |
> | billingAccount / billingProfiles | No |
> | billingAccount / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccount / billingProfiles / billingRoleDefinitions | No |
> | billingAccount / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccount / billingProfiles / clienti | No |
> | billingAccount / billingProfiles / istruzioni | No |
> | billingAccount / billingProfiles / fatture | No |
> | billingAccount / billingProfiles / fatture / liste | No |
> | billingAccount / billingProfiles / fatture / transazioni | No |
> | billingAccount / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts / billingProfiles / invoiceSections / prodotti | No |
> | billingAccounts / billingProfiles / invoiceSections / prodotti / trasferimento | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No |
> | billingAccount / billingProfiles / invoiceSections / transazioni | No |
> | billingAccounts / billingProfiles / invoiceSezioni / trasferimenti | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccount / billingProfiles / paymentMethods | No |
> | billingAccount / billingProfiles / criteri | No |
> | billingAccount / billingProfiles / listino prezzi | No |
> | billingAccount / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccount / billingProfiles / prodotti | No |
> | billingAccount / billingProfiles / transazioni | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccount / billingSubscriptions | No |
> | billingAccount / billingSubscriptions / fatture | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts / clienti | No |
> | billingAccount / clienti / billingPermissions | No |
> | billingAccount / clienti / billingSottoscrizioni | No |
> | billingAccounts / clienti / initiateTransfer | No |
> | billingAccount / clienti / polizze | No |
> | billingAccount / clienti / prodotti | No |
> | billingAccount / clienti / transazioni | No |
> | billingAccount / clienti / trasferimenti | No |
> | billingAccount / reparti | No |
> | billingAccount / enrollmentAccounts | No |
> | billingAccount / fatture | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccount / invoiceSections / billingSottoscrizioni | No |
> | billingAccounts / invoiceSections / billingSubscriptions / trasferimento | No |
> | billingAccounts / invoiceSections / elevate | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccount / invoiceSections / prodotti | No |
> | billingAccounts / invoiceSezioni / prodotti / trasferimento | No |
> | billingAccounts / invoiceSections / prodotti / updateAutoRenew | No |
> | billingAccount / invoiceSezioni / transazioni | No |
> | billingAccounts / invoiceSezioni / trasferimenti | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccount / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts / prodotti | No |
> | billingAccount / transazioni | No |
> | billingPeriods | No |
> | billingAutorizzazioni | No |
> | billingProperty | No |
> | billingRoleAssignments (Assegnazione di billingRole) | No |
> | billingRoleDefinitions (Definizioni di ruolo) | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | transfers | No |
> | trasferimenti / acceptTransfer | No |
> | trasferimenti / declinoTrasferimento | No |
> | trasferimenti / operationStatus | No |
> | trasferimenti / validateTransfer | No |
> | validateAddress (indirizzo)ValidateAddress ( | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mapApis | Sì |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blockchainMembers | Sì |
> | cordaMembers (membri di corda) | Sì |
> | Osservatori | Sì |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | TokenServices (Servizi simbolici) | Sì |
> | TokenServices / BlockchainNetworks | No |
> | TokenServices / Gruppi | No |
> | TokenServices / Gruppi / Account | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments / operazioni | No |
> | blueprints | No |
> | cianografie / artefatti | No |
> | blueprint / versioni | No |
> | blueprint / versioni / artefatti | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | botServices | Sì |
> | botServices / canali | No |
> | botServices/connessioni | No |
> | lingue | No |
> | Modelli | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Redis | Sì |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice (prezzodile) | No |
> | catalogs | No |
> | commercialReservationOrders | No |
> | exchange | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders / merge | No |
> | prenotazioniOrdini / prenotazioni | No |
> | prenotazioniOrdini / prenotazioni / revisioni | No |
> | reservationOrders / restituzione | No |
> | reservationOrders / split | No |
> | reservationOrders / swap | No |
> | reservations | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallCriteri | Sì |
> | edgenodes | No |
> | Profili | Sì |
> | profili/endpoint | Sì |
> | profili / endpoint / customdomains | No |
> | profili/endpoint/gruppi di origine | No |
> | profili/ endpoint / origini | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | certificateOrders | Sì |
> | certificateOrders / certificati | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Sì |
> | domainNames / funzionalità | No |
> | domainNames / internalLoadBalancers | No |
> | domainNames / serviceCertificates | No |
> | domainNames / slot | No |
> | domainNames / slot / ruoli | No |
> | domainNames / slot / ruoli / metricDefinitions | No |
> | domainNames / slot / ruoli / metriche | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Sì |
> | virtualMachines / diagnosticSettings | No |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / metriche | No |

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
> | networkSecurityGroups | Sì |
> | quotas | No |
> | reservedIps | Sì |
> | virtualNetworks | Sì |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

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
> | storageAccounts | Sì |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / metricDefinitions | No |
> | storageAccounts / metriche | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / servizi | No |
> | storageAccounts / services / diagnosticSettings | No |
> | storageAccounts / servizi / metricDefinitions | No |
> | storageAccounts / servizi / metriche | No |
> | storageAccounts / tableServices | No |
> | storageAccounts / vmImages | No |
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
> | availabilitySets | Sì |
> | DiskEncryptionSets (Crittografia disk) | Sì |
> | disks | Sì |
> | galleries | Sì |
> | gallerie / applicazioni | No |
> | gallerie / applicazioni / versioni | No |
> | gallerie / immagini | No |
> | gallerie / immagini / versioni | No |
> | hostGruppi | Sì |
> | hostGruppi / host | Sì |
> | images | Sì |
> | prossimitàPlacementGroups | Sì |
> | restorePointCollections | Sì |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions (estensioni sharedVM) | Sì |
> | sharedVMExtensions / versioni | No |
> | sharedVMImages | Sì |
> | sharedVMImages / versioni | No |
> | snapshots | Sì |
> | sshPublicKeys | Sì |
> | virtualMachines | Sì |
> | virtualMachines / estensioni | Sì |
> | virtualMachines / metricDefinitions | No |
> | virtualMachineScaleSets | Sì |
> | virtualMachineScaleSets/ estensioni | No |
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
> | containerGroups | Sì |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registries | Sì |
> | registri / agentPools | Sì |
> | registri / build | No |
> | registri / costruzioni / annullare | No |
> | registri / build / getLogLink | No |
> | registri / buildTasks | Sì |
> | registri / buildTasks / passaggi | No |
> | registri / eventGridFilters | No |
> | registri / generateCredentials | No |
> | registri / getBuildSourceUploadUrl | No |
> | registri / GetCredentials | No |
> | registri / importImage | No |
> | registri / privateEndpointConnectionProxies | No |
> | registri / privateEndpointConnectionProxies / convalida | No |
> | registri / privateEndpointConnections | No |
> | registri / privateLinkResources | No |
> | registri / queueBuild | No |
> | registri / regenerateCredential | No |
> | registri / regenerateCredentials | No |
> | registri / repliche | Sì |
> | registri / esecuzioni | No |
> | registri / esecuzioni / annulla | No |
> | registri / scheduleRun | No |
> | registri / scopeMappe | No |
> | registri / taskRuns | Sì |
> | registri / attività | Sì |
> | registri / token | No |
> | registri / updatePolicies | No |
> | registri / webhooks | Sì |
> | registri / webhooks / getCallbackConfig | No |
> | registri / webhooks / ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerServices | Sì |
> | managedClusters | Sì |
> | openShiftManagedClusters | Sì |

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
> | ExternalBillingAccounts / Avvisi | No |
> | ExternalBillingAccounts / Dimensioni | No |
> | ExternalBillingAccounts / Previsione | No |
> | ExternalBillingAccounts / Query | No |
> | ExternalSubscriptions (Sottoscrizioni Esterne) | No |
> | ExternalSubscriptions / Avvisi | No |
> | ExternalSubscriptions / Dimensioni | No |
> | ExternalSottoscrizioni / Previsione | No |
> | ExternalSubscriptions / Query | No |
> | Previsione | No |
> | Query | No |
> | register | No |
> | Reportconfigs | No |
> | Report | No |
> | Impostazioni | No |
> | showbackRules (regole showback) | No |
> | Viste | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | requests | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders (informazioni in base ai provider personalizzati)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | associazioni | No |
> | resourceProviders | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | Sì |
> | aree di lavoro / dbWorkspaces | No |
> | aree di lavoro / storageEncryption | No |
> | aree di lavoro / virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | catalogs | Sì |
> | datacatalogs | Sì |
> | datacatalogs / origini dati | No |
> | datacatalogs / fonti dati / scansioni | No |
> | datacatalogs / fonti di dati / scansioni / set di dati | No |
> | datacatalogs / datasources / scansioni / trigger | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataFactories | Sì |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | Sì |
> | factory / integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | account / dataLakeStoreAccounts | No |
> | account / storageAccounts | No |
> | account / storageAccounts / contenitori | No |
> | account / transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts / eventGridFilters | No |
> | account / firewallRegole | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |
> | servizi/ progetti | Sì |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | conti / azioni | No |
> | conti / condivisioni / set di dati | No |
> | conti / azioni / inviti | No |
> | account / azioni / providersharesottoscrizioni | No |
> | account/ condivisioni / sincronizzazioneImpostazioni | No |
> | account / sharesubscriptions | No |
> | account/ sharesubscriptions / consumerSourceDataSets | No |
> | account/ sharesubscriptions / datasetmapping | No |
> | account / sharesubscriptions / trigger | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì |
> | server / consulenti | No |
> | server / chiavi | No |
> | server / privateEndpointConnectionProxies | No |
> | server / privateEndpointConnections | No |
> | server / privateLinkResources | No |
> | server / queryTexts | No |
> | server / recoverableServers | No |
> | server / topQueryStatistics | No |
> | server / virtualNetworkRules | No |
> | server / waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì |
> | server / consulenti | No |
> | server / chiavi | No |
> | server / privateEndpointConnectionProxies | No |
> | server / privateEndpointConnections | No |
> | server / privateLinkResources | No |
> | server / queryTexts | No |
> | server / recoverableServers | No |
> | server / topQueryStatistics | No |
> | server / virtualNetworkRules | No |
> | server / waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | gruppi di server | Sì |
> | servers | Sì |
> | server / consulenti | No |
> | server / chiavi | No |
> | server / privateEndpointConnectionProxies | No |
> | server / privateEndpointConnections | No |
> | server / privateLinkResources | No |
> | server / queryTexts | No |
> | server / recoverableServers | No |
> | server / topQueryStatistics | No |
> | server / virtualNetworkRules | No |
> | server / waitStatistics | No |
> | serverv2 (serverv2) | Sì |
> | singleServer | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | artefattoSources | Sì |
> | rollouts | Sì |
> | serviceTopologies (Topologies servizio) | Sì |
> | serviceTopologies / servizi | Sì |
> | serviceTopologies / servizi / serviceUnits | Sì |
> | steps | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization (applicazione di virtualizzazione)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationgroups (gruppi di applicazioni) | Sì |
> | applicationgroups / applicazioni | No |
> | applicationgroups / desktop | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | Sì |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | aree di lavoro | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | ElasticPools | Sì |
> | ElasticPools / IotHubTenants | Sì |
> | ElasticPools / IotHubTenants / securitySettings | No |
> | Hub IoT | Sì |
> | IotHubs / eventGridFilters | No |
> | IotHubs / securitySettings | No |
> | ProvisioningServices | Sì |
> | usages | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps (informazioni in base al sistema operativo Microsoft.Dev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | pipeline | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | controllers | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labcenters | Sì |
> | labs | Sì |
> | laboratori / ambienti | Sì |
> | laboratori / serviceRunners | Sì |
> | laboratori / macchine virtuali | Sì |
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
> | domini / domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects / connettori | No |

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
> | domini / argomenti | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Sì |
> | partnerNamespaces / eventChannels | No |
> | partnerRegistrazioni | Sì |
> | partnerArgomenti | Sì |
> | partnerTopics / eventSubscriptions | No |
> | systemArgomenti | Sì |
> | systemTopics / eventSubscriptions | No |
> | topics | Sì |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | spazi dei nomi | Sì |
> | spazi dei nomi / autorizzazioniregole | No |
> | spazi dei nomi / disasterrecoveryconfigs | No |
> | spazi dei nomi / eventhubs | No |
> | spazi dei nomi / eventhubs / authorizationrules | No |
> | spazi dei nomi / eventhubs / consumergroups | No |
> | spazi dei nomi / networkruleset | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | featureProviders | No |
> | funzionalità | No |
> | provider | No |
> | subscriptionFeatureRegistrazioni | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas / aree | No |
> | myareas / aree / aree | No |
> | myareas / aree / aree / galleryelementi | No |
> | myareas / aree / galleryitems | No |
> | myareas / galleryitems | No |
> | register | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | autoManagedAccounts | Sì |
> | autoManagedVmConfigurationProfiles | Sì |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile (profilato) | No |
> | softwareAggiornamenti | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hanaInstances | Sì |
> | sapMonitors | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | HSMs dedicati | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | cluster/applicazioni | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Macchine | Sì |
> | macchine / estensioni | Sì |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataManager | Sì |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | components | Sì |
> | networkScopes (Ambiti di rete) | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Sì |

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
> | appTemplates (modelli di app) | No |
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
> | hsmPools (Bobina hsm) | Sì |
> | insiemi di credenziali | Sì |
> | vault / accessPolicies | No |
> | vault / eventGridFilters | No |
> | volte / segreti | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | connectedCluster | Sì |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | cluster/ databaseconfigurazioni collegate | No |
> | cluster / database | No |
> | cluster / database / connessioni dati | No |
> | cluster/database/eventhubconnections | No |
> | cluster/database/principalassignments | No |
> | cluster/ connessioni dati | No |
> | cluster / principalassignments | No |
> | cluster / identità condivise | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labaccounts | Sì |
> | users | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hostingEnvironments | Sì |
> | integrationAccounts | Sì |
> | integrationAmbienti di servizio | Sì |
> | integrationServiceEnvironments / managedApis | Sì |
> | isolatedAmbienti | Sì |
> | flussi di lavoro | Sì |

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
> | aree di lavoro | Sì |
> | aree di lavoro/ calcola | No |
> | aree di lavoro / eventGridFilters | No |

## <a name="microsoftmaintenance"></a>Microsoft.Manutenzione

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applyAggiornamenti | No |
> | configurationAssegnazioni | No |
> | maintenanceConfigurazionIconfigurazioni | Sì |
> | aggiornamenti | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | Sì |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrazioneAssegnazioni | No |
> | registrationDefinitions (definizioni di registrazione) | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups / impostazioni | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts / eventGridFilters | No |
> | account / privateAtlases | Sì |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | offers | No |
> | offerTypes | No |
> | offerTypes / editori | No |
> | offerTypes / editori / offerte | No |
> | offerTypes / editori / offerte / piani | No |
> | offerTypes / editori / offerte / piani / accordi | No |
> | offerTypes / editori / offerte / piani / configurazioni | No |
> | offerTypes / editori / offerte / piani / configurazioni / importImage | No |
> | privategalleryitems | No |
> | PrivateStoreClient | No |
> | privateStores | No |
> | privateStores / offerte | No |
> | products | No |
> | server di pubblicazione | No |
> | editori / offerte | No |
> | editori / offerte / emendamenti | No |
> | register | No |

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
> | mediaservices / accountFilters | No |
> | mediaservices / risorse | No |
> | mediaservices / asset / assetFilters | No |
> | mediaservices / contentKeyPolicies | No |
> | mediaservices / eventGridFilters | No |
> | mediaservices / liveEventOperations | No |
> | mediaservices / liveEvents | Sì |
> | mediaservices / liveEvents / liveOutputs | No |
> | mediaservices / liveOutputOperations | No |
> | mediaservices / mediaGraphs | No |
> | mediaservices / streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | Sì |
> | mediaservices / streamingLocators | No |
> | mediaservices / streamingPolicies | No |
> | mediaservices/trasformazioni | No |
> | mediaservices/trasforma/lavori | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring (prodotti Microsoft.Microservices4Spring)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appClusters | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | valutazioneProgetti | Sì |
> | migrateprojects | Sì |
> | moveCollections | Sì |
> | projects | Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality (realtà di Microsoft)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | olografitraBroadcastAccounts | Sì |
> | objectUnderstandingAccounts | Sì |
> | remoteRenderingAccounts (accountdiremoteRenderingAccounts) | Sì |
> | spatialAnchorsAccount | Sì |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | netAppAccount | Sì |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Sì |
> | netAppAccounts / capacityPools / volumi | Sì |
> | netAppAccounts / capacityPools / volumi / istantanee | Sì |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationGateways | Sì |
> | ApplicationGatewayWebApplicationFirewallPolicies (ApplicazioneGatewayWebApplicationFirewallPolicies) | Sì |
> | applicationSecurityGroups | Sì |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Sì |
> | bastionGli ospiti | Sì |
> | bgpServiceCommunities | No |
> | connections | Sì |
> | ddosCustomPolicies | Sì |
> | ddosProtectionPlans | Sì |
> | dnsOperationStatuses | No |
> | dnszones | Sì |
> | dnszones / A | No |
> | dnszones / AAAA | No |
> | dnszones / tutti | No |
> | dnszones / CAA | No |
> | dnszones / CNAME | No |
> | dnszones / MX | No |
> | dnszones / NS | No |
> | dnszones / PTR | No |
> | dnszones / recordset | No |
> | dnszones / SOA | No |
> | dnszones / SRV | No |
> | dnszones / TXT | No |
> | expressRouteCircuits | Sì |
> | expressRouteCrossConnections | Sì |
> | expressRouteGateways | Sì |
> | expressRoutePorts | Sì |
> | expressRouteServiceProviders | No |
> | firewallPolicies (Criteri firewall) | Sì |
> | frontdoors | Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Sì |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | loadBalancers | Sì |
> | localNetworkGateways | Sì |
> | natGateways | Sì |
> | networkIntentPolicies | Sì |
> | networkInterfaces | Sì |
> | networkProfiles | Sì |
> | networkSecurityGroups | Sì |
> | networkWatchers | Sì |
> | networkWatchers / connectionMonitors | Sì |
> | networkOsservatori / lenti | Sì |
> | networkWatchers / pingMeshes | Sì |
> | p2sVpnGateways | Sì |
> | privateDnsOperationStatuses | No |
> | privateDns- | Sì |
> | privateDns- | No |
> | privateDns- | No |
> | privateDns- | No |
> | privateDns- | No |
> | privateDns- - MX | No |
> | privateDns-ones/ PTR | No |
> | privateDns- | No |
> | privateDns - SRV | No |
> | privateDNS - TXT | No |
> | privateDns - virtualNetworkLinks | Sì |
> | endpoint privati | Sì |
> | privateLinkServices | Sì |
> | publicIPAddresses | Sì |
> | publicIPPrefixes | Sì |
> | routeFilters | Sì |
> | routeTables | Sì |
> | serviceEndpointPolicies | Sì |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Sì |
> | trafficmanagerprofiles / heatMappe | No |
> | TrafficManagerUserMetricsKeys | No |
> | virtualHubs | Sì |
> | virtualNetworkGateways | Sì |
> | virtualNetworks | Sì |
> | virtualNetworkTaps | Sì |
> | virtualWans | Sì |
> | vpnGateways | Sì |
> | vpnSites | Sì |
> | webApplicationFirewallPolicies | Sì |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Prossia per notebook | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | spazi dei nomi / notificationHubs | Sì |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | osNamespaces | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | HyperVSiti | Sì |
> | ImportareSiti | Sì |
> | Siti Server | Sì |
> | Siti VMware | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | aree di lavoro | Sì |
> | aree di lavoro / datiExports | No |
> | aree di lavoro/ dataSources | No |
> | aree di lavoro / linkedServices | No |
> | aree di lavoro / linkedStorageAccounts | No |
> | aree di lavoro/ query | No |
> | aree di lavoro / scopedPrivateLinkProxies | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Sì |
> | solutions | Sì |
> | Viste | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Sì |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | policyEvents | No |
> | policyMetadata (policyMetadata) | No |
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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Aree di lavoro | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | insiemi di credenziali | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | spazi dei nomi / autorizzazioniregole | No |
> | spazi dei nomi/connessioni ibride | No |
> | spazi dei nomi / connessioni ibride / autorizzazioniregole | No |
> | spazi dei nomi / wcfrelays | No |
> | spazi dei nomi / wcfrelays / authorizationrules | No |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp (informazioni in remoto)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | No |
> | raccolte | Sì |
> | collezioni / applicazioni | No |
> | raccolte / securityprincipals | No |
> | modelloImmagini | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | query | Sì |
> | resourceChangeDetails (ResourceChangeDetails) | No |
> | risorsaModifiche | No |
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
> | emergentie emergenti | No |
> | eventi | No |
> | impactedResources | No |
> | metadata | No |
> | Notifiche | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deployments | No |
> | distribuzioni/ operazioni | No |
> | distribuzioneScript | Sì |
> | deploymentScripts / registri | No |
> | collegamenti | No |
> | notifyResourceJobs | No |
> | provider | No |
> | resourceGroups | No |
> | subscriptions | No |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | saasresources | No |

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
> | adadattivoNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | valutazioni | No |
> | autoDismissAlertsRules | No |
> | Automazioni | Sì |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups (gruppi di sicurezza) | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | Sì |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | jitNetworkAccessPolicies | No |
> | networkData (dati di rete) | No |
> | criteri | No |
> | pricings | No |
> | regolamentareStandard | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityValutazioni | No |
> | Scheda Impostazioni | No |
> | subValutazioni | No |
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
> | alertRuleTemplates (modelli alertRuleTemplates) | No |
> | segnalibri | No |
> | cases | No |
> | DataConnectors (Connettori dati) | No |
> | DataConnectorsCheckRequirements (Requisiti di DataConnectorsCheckRequirements) | No |
> | entities | No |
> | entityQueries (Query di entità) | No |
> | Incidenti | No |
> | ufficioCondimenti | No |
> | Scheda Impostazioni | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | spazi dei nomi / autorizzazioniregole | No |
> | spazi dei nomi / disasterrecoveryconfigs | No |
> | spazi dei nomi / eventgridfilters | No |
> | spazi dei nomi / networkruleset | No |
> | spazi dei nomi / code | No |
> | spazi dei nomi / code / autorizzazioniregole | No |
> | spazi dei nomi / argomenti | No |
> | spazi dei nomi / argomenti / autorizzazioniregole | No |
> | spazi dei nomi / argomenti / sottoscrizioni | No |
> | spazi dei nomi / argomenti / sottoscrizioni / regole | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | clusters | Sì |
> | cluster/applicazioni | No |
> | containerGroups | Sì |
> | contenitoriSet | Sì |
> | edgeclusters | Sì |
> | edgecluster / applicazioni | No |
> | managedclusters | Sì |
> | managedclusters / nodetypes | No |
> | networks | Sì |
> | secretstores | Sì |
> | secreti / certificati | No |
> | secreti / segreti | No |
> | volumes | Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | containerGroups | Sì |
> | gateways | Sì |
> | networks | Sì |
> | chiavi private | Sì |
> | volumes | Sì |

## <a name="microsoftservices"></a>Microsoft.Servizi

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | rollouts | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SignalR | Sì |
> | SignalR / eventGridFilters | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sì |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan (piano)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationDefinitions | Sì |
> | scala Web | Sì |
> | jitRequests | Sì |

## <a name="microsoftspoolservice"></a>Servizio Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registeredSubscriptions (Sottoscrizioni registrate) | No |
> | Bobine | Sì |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedInstances | Sì |
> | managedInstances / database | Sì |
> | managedInstances / database / backupShortTermRetentionPolicies | No |
> | managedInstances / database / schemi / tabelle / colonne / sensitivityLabels | No |
> | managedInstances / database / vulnerabilitàValutazioni | No |
> | managedInstances / database / vulnerabilitàValutazioni / regole / linee di base | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances / chiavi | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilitàValutazioni | No |
> | servers | Sì |
> | server / amministratori | No |
> | server / communicationLinks | No |
> | server / database | Sì |
> | server / encryptionProtector | No |
> | server / firewallRegole | No |
> | server / chiavi | No |
> | server / restorableDroppedDatabases | No |
> | server / obiettivi di servizio | No |
> | server / tdeCertificati | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sì |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageAccounts | Sì |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / servizi | No |
> | storageAccounts / servizi / metricDefinitions | No |
> | storageAccounts / tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Cache | Sì |
> | cache / storageTargets | No |
> | usageModel | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | replicationGruppi | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / flussi di lavoro | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / flussi di lavoro | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / flussi di lavoro | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managers | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | streamingjobs | Sì |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cancel | No |
> | CreateSubscription | No |
> | abilitare | No |
> | ridenominazione | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | subscriptions | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | Sì |
> | ambienti / accessPolicies | No |
> | ambienti/origini eventi | Sì |
> | ambienti /referenceDataSets | Sì |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicataCloudNodes | Sì |
> | dedicatoCloudServices | Sì |
> | virtualMachines | Sì |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | devices | Sì |
> | registeredSubscriptions (Sottoscrizioni registrate) | No |
> | Fornitori | No |
> | fornitori / skus | No |
> | fornitori / vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | Vnfs (in vnfs) | Sì |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts / apis | No |
> | apiManagementAccounts / apis / apiAcls | No |
> | apiManagementAccounts / apis / connectionAcls | No |
> | apiManagementAccounts / apis / connessioni | No |
> | apiManagementAccounts / apis / connessioni / connectionAcls | No |
> | apiManagementAccounts / apis / localizedDefinitions | No |
> | apiManagementAccounts / connectionAcls | No |
> | apiManagementAccounts / connessioni | No |
> | billingMeters | No |
> | certificates | Sì |
> | connectionGateways | Sì |
> | connections | Sì |
> | customApis | Sì |
> | deletedSites | No |
> | hostingEnvironments | Sì |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | kubeAmbienti | Sì |
> | publishingUsers | No |
> | raccomandazioni di film | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Sì |
> | serverFarms / eventGridFilters | No |
> | siti | Sì |
> | siti/config  | No |
> | siti / eventGridFilters | No |
> | siti / hostNameBindings | No |
> | siti / networkConfig | No |
> | siti / premieraddons | Sì |
> | siti / slot | Sì |
> | siti / slot / eventGridFilters | No |
> | siti / slot / hostNameBindings | No |
> | siti / slot / networkConfig | No |
> | sourceControls | No |
> | staticazioneSiti | Sì |
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
