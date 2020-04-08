---
title: Supporto dei tag per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e012126995136bec15dc360be5e91007b6f69f09
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802493"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](tag-resources.md). I tag **Supports** della colonna indicano se il tipo di risorsa dispone di una proprietà per il tag. La colonna etichettata **Etichetta nel report dei costi** indica se quel tipo di risorsa passa il tag al report dei costi. È possibile visualizzare i costi in base ai tag nell'analisi dei costi di [gestione dei costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) e nella fattura di fatturazione di Azure e nei dati di utilizzo [giornalieri.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sì | Sì |
> | DomainServices / oucontainer | No | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
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
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | configurazioni | No | No |
> | generateRecommendations | No | No |
> | metadata | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | actionRules | Sì | Sì |
> | alerts | No | No |
> | alertsList | No | No |
> | avvisiMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | smartDetectorAlertRules | Sì | Sì |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | service | Sì | Sì |
> | validateServiceName | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration (Configurazione applicazioni)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sì | Sì |
> | configurationStores / eventGridFilters | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform (Piattaforma App)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Spring | Sì | Sì |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | attestationProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No | No |
> | dataAlias | No | No |
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
> | roleAssignmentsUsageMetrics | No | No |
> | roleDefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Sì | Sì |
> | automationAccount / configurazioni | Sì | Sì |
> | automationAccount / lavori | No | No |
> | automationAccounts / privateEndpointConnectionProxies | No | No |
> | automationAccounts / privateEndpointConnections | No | No |
> | automationAccounts / privateLinkResources | No | No |
> | automationAccount / runbook | Sì | Sì |
> | automationAccount / softwareUpdateConfigurations | No | No |
> | automationAccount / webhook | No | No |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sì | Sì |
> | configurationStores / eventGridFilters | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | environments | No | No |
> | ambienti / account | No | No |
> | ambienti/ account / spazi dei nomi | No | No |
> | ambienti / account / spazi dei nomi / configurazioni | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sì | No |
> | b2ctenant | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Sì | Sì |
> | postgresIi | Sì | Sì |
> | sqlInstances (istanza di sqlInstances) | Sì | Sì |
> | Sqlserverregistrations | Sì | Sì |
> | sqlServerRegistrations / sqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles (file cloudManifestFiles) | No | No |
> | registrations | Sì | Sì |
> | registrazioni / clientiSottoscrizioni | No | No |
> | registrazioni / prodotti | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sì | Sì |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccount / accordi | No | No |
> | billingAccount / billingPermissions | No | No |
> | billingAccount / billingProfiles | No | No |
> | billingAccount / billingProfiles / billingPermissions | No | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No | No |
> | billingAccount / billingProfiles / billingRoleDefinitions | No | No |
> | billingAccount / billingProfiles / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No | No |
> | billingAccount / billingProfiles / clienti | No | No |
> | billingAccount / billingProfiles / istruzioni | No | No |
> | billingAccount / billingProfiles / fatture | No | No |
> | billingAccount / billingProfiles / fatture / liste | No | No |
> | billingAccount / billingProfiles / fatture / transazioni | No | No |
> | billingAccount / billingProfiles / invoiceSections | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No | No |
> | billingAccounts / billingProfiles / invoiceSections / prodotti | No | No |
> | billingAccounts / billingProfiles / invoiceSections / prodotti / trasferimento | No | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No | No |
> | billingAccount / billingProfiles / invoiceSections / transazioni | No | No |
> | billingAccounts / billingProfiles / invoiceSezioni / trasferimenti | No | No |
> | billingAccounts / BillingProfiles / patchOperations | No | No |
> | billingAccount / billingProfiles / paymentMethods | No | No |
> | billingAccount / billingProfiles / criteri | No | No |
> | billingAccount / billingProfiles / listino prezzi | No | No |
> | billingAccount / billingProfiles / pricesheetDownloadOperations | No | No |
> | billingAccount / billingProfiles / prodotti | No | No |
> | billingAccount / billingProfiles / transazioni | No | No |
> | billingAccounts / billingRoleAssignments | No | No |
> | billingAccounts / billingRoleDefinitions | No | No |
> | billingAccount / billingSubscriptions | No | No |
> | billingAccount / billingSubscriptions / fatture | No | No |
> | billingAccounts / createBillingRoleAssignment | No | No |
> | billingAccounts / createInvoiceSectionOperations | No | No |
> | billingAccounts / clienti | No | No |
> | billingAccount / clienti / billingPermissions | No | No |
> | billingAccount / clienti / billingSottoscrizioni | No | No |
> | billingAccounts / clienti / initiateTransfer | No | No |
> | billingAccount / clienti / polizze | No | No |
> | billingAccount / clienti / prodotti | No | No |
> | billingAccount / clienti / transazioni | No | No |
> | billingAccount / clienti / trasferimenti | No | No |
> | billingAccount / reparti | No | No |
> | billingAccount / enrollmentAccounts | No | No |
> | billingAccount / fatture | No | No |
> | billingAccounts / invoiceSections | No | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No | No |
> | billingAccount / invoiceSections / billingSottoscrizioni | No | No |
> | billingAccounts / invoiceSections / billingSubscriptions / trasferimento | No | No |
> | billingAccounts / invoiceSections / elevate | No | No |
> | billingAccounts / invoiceSections / initiateTransfer | No | No |
> | billingAccounts / invoiceSections / patchOperations | No | No |
> | billingAccounts / invoiceSections / productMoveOperations | No | No |
> | billingAccount / invoiceSections / prodotti | No | No |
> | billingAccounts / invoiceSezioni / prodotti / trasferimento | No | No |
> | billingAccounts / invoiceSections / prodotti / updateAutoRenew | No | No |
> | billingAccount / invoiceSezioni / transazioni | No | No |
> | billingAccounts / invoiceSezioni / trasferimenti | No | No |
> | billingAccounts / lineOfCredit | No | No |
> | billingAccount / patchOperations | No | No |
> | billingAccounts / paymentMethods | No | No |
> | billingAccounts / prodotti | No | No |
> | billingAccount / transazioni | No | No |
> | billingPeriods | No | No |
> | billingAutorizzazioni | No | No |
> | billingProperty | No | No |
> | billingRoleAssignments (Assegnazione di billingRole) | No | No |
> | billingRoleDefinitions (Definizioni di ruolo) | No | No |
> | createBillingRoleAssignment | No | No |
> | departments | No | No |
> | enrollmentAccounts | No | No |
> | invoices | No | No |
> | transfers | No | No |
> | trasferimenti / acceptTransfer | No | No |
> | trasferimenti / declinoTrasferimento | No | No |
> | trasferimenti / operationStatus | No | No |
> | trasferimenti / validateTransfer | No | No |
> | validateAddress (indirizzo)ValidateAddress ( | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | mapApis | Sì | Sì |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sì | Sì |
> | cordaMembers (membri di corda) | Sì | Sì |
> | Osservatori | Sì | Sì |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | TokenServices (Servizi simbolici) | Sì | Sì |
> | TokenServices / BlockchainNetworks | No | No |
> | TokenServices / Gruppi | No | No |
> | TokenServices / Gruppi / Account | No | No |
> | TokenServices / TokenTemplates | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments / assignmentOperations | No | No |
> | blueprintAssignments / operazioni | No | No |
> | blueprints | No | No |
> | cianografie / artefatti | No | No |
> | blueprint / versioni | No | No |
> | blueprint / versioni / artefatti | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | botServices | Sì | Sì |
> | botServices / canali | No | No |
> | botServices/connessioni | No | No |
> | lingue | No | No |
> | Modelli | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Redis | Sì | Sì |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No | No |
> | autoQuotaIncrease | No | No |
> | calculateExchange | No | No |
> | calculatePrice | No | No |
> | calculatePurchasePrice (prezzodile) | No | No |
> | catalogs | No | No |
> | commercialReservationOrders | No | No |
> | exchange | No | No |
> | placePurchaseOrder | No | No |
> | reservationOrders | No | No |
> | reservationOrders / calculateRefund | No | No |
> | reservationOrders / merge | No | No |
> | prenotazioniOrdini / prenotazioni | No | No |
> | prenotazioniOrdini / prenotazioni / revisioni | No | No |
> | reservationOrders / restituzione | No | No |
> | reservationOrders / split | No | No |
> | reservationOrders / swap | No | No |
> | reservations | No | No |
> | resourceProviders | No | No |
> | resources | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallCriteri | Sì | Sì |
> | edgenodes | No | No |
> | Profili | Sì | Sì |
> | profili/endpoint | Sì | Sì |
> | profili / endpoint / customdomains | No | No |
> | profili/endpoint/gruppi di origine | No | No |
> | profili/ endpoint / origini | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sì | Sì |
> | certificateOrders / certificati | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | domainNames | No | No |
> | domainNames / funzionalità | No | No |
> | domainNames / internalLoadBalancers | No | No |
> | domainNames / serviceCertificates | No | No |
> | domainNames / slot | No | No |
> | domainNames / slot / ruoli | No | No |
> | domainNames / slot / ruoli / metricDefinitions | No | No |
> | domainNames / slot / ruoli / metriche | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | resourceTypes | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines / diagnosticSettings | No | No |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachines / metriche | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections/peerings | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks / virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | disks | No | No |
> | images | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts / blobServices | No | No |
> | storageAccounts / fileServices | No | No |
> | storageAccounts / metricDefinitions | No | No |
> | storageAccounts / metriche | No | No |
> | storageAccounts / queueServices | No | No |
> | storageAccounts / servizi | No | No |
> | storageAccounts / services / diagnosticSettings | No | No |
> | storageAccounts / servizi / metricDefinitions | No | No |
> | storageAccounts / servizi / metriche | No | No |
> | storageAccounts / tableServices | No | No |
> | storageAccounts / vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sì | Sì |
> | DiskEncryptionSets (Crittografia disk) | Sì | Sì |
> | disks | Sì | Sì |
> | galleries | Sì | Sì |
> | gallerie / applicazioni | No | No |
> | gallerie / applicazioni / versioni | No | No |
> | gallerie / immagini | No | No |
> | gallerie / immagini / versioni | No | No |
> | hostGruppi | Sì | Sì |
> | hostGruppi / host | Sì | Sì |
> | images | Sì | Sì |
> | prossimitàPlacementGroups | Sì | Sì |
> | restorePointCollections | Sì | Sì |
> | restorePointCollections / restorePoints | No | No |
> | sharedVMExtensions (estensioni sharedVM) | Sì | Sì |
> | sharedVMExtensions / versioni | No | No |
> | sharedVMImages | Sì | Sì |
> | sharedVMImages / versioni | No | No |
> | snapshots | Sì | Sì |
> | sshPublicKeys | Sì | Sì |
> | virtualMachines | Sì | Sì |
> | virtualMachines / estensioni | Sì | Sì |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachineScaleSets | Sì | Sì |
> | virtualMachineScaleSets/ estensioni | No | No |
> | virtualMachineScaleSets / networkInterfaces | No | No |
> | virtualMachineScaleSets / publicIPAddresses | No | No |
> | virtualMachineScaleSets / virtualMachines | No | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
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
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sì | Sì |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | registries | Sì | Sì |
> | registri / agentPools | Sì | Sì |
> | registri / build | No | No |
> | registri / costruzioni / annullare | No | No |
> | registri / build / getLogLink | No | No |
> | registri / buildTasks | Sì | Sì |
> | registri / buildTasks / passaggi | No | No |
> | registri / eventGridFilters | No | No |
> | registri / generateCredentials | No | No |
> | registri / getBuildSourceUploadUrl | No | No |
> | registri / GetCredentials | No | No |
> | registri / importImage | No | No |
> | registri / privateEndpointConnectionProxies | No | No |
> | registri / privateEndpointConnectionProxies / convalida | No | No |
> | registri / privateEndpointConnections | No | No |
> | registri / privateLinkResources | No | No |
> | registri / queueBuild | No | No |
> | registri / regenerateCredential | No | No |
> | registri / regenerateCredentials | No | No |
> | registri / repliche | Sì | Sì |
> | registri / esecuzioni | No | No |
> | registri / esecuzioni / annulla | No | No |
> | registri / scheduleRun | No | No |
> | registri / scopeMappe | No | No |
> | registri / taskRuns | Sì | Sì |
> | registri / attività | Sì | Sì |
> | registri / token | No | No |
> | registri / updatePolicies | No | No |
> | registri / webhooks | Sì | Sì |
> | registri / webhooks / getCallbackConfig | No | No |
> | registri / webhooks / ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | containerServices | Sì | Sì |
> | managedClusters | Sì | Sì |
> | openShiftManagedClusters | Sì | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Avvisi | No | No |
> | BillingAccounts | No | No |
> | Budget | No | No |
> | CloudConnectors | No | No |
> | Connettori | Sì | Sì |
> | Departments | No | No |
> | Dimensioni | No | No |
> | EnrollmentAccounts | No | No |
> | Esportazioni | No | No |
> | ExternalBillingAccounts | No | No |
> | ExternalBillingAccounts / Avvisi | No | No |
> | ExternalBillingAccounts / Dimensioni | No | No |
> | ExternalBillingAccounts / Previsione | No | No |
> | ExternalBillingAccounts / Query | No | No |
> | ExternalSubscriptions (Sottoscrizioni Esterne) | No | No |
> | ExternalSubscriptions / Avvisi | No | No |
> | ExternalSubscriptions / Dimensioni | No | No |
> | ExternalSottoscrizioni / Previsione | No | No |
> | ExternalSubscriptions / Query | No | No |
> | Previsione | No | No |
> | Query | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | Report | No | No |
> | Impostazioni | No | No |
> | showbackRules (regole showback) | No | No |
> | Viste | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | requests | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders (informazioni in base ai provider personalizzati)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | associazioni | No | No |
> | resourceProviders | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | jobs | Sì | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sì | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | Sì | No |
> | aree di lavoro / dbWorkspaces | No | No |
> | aree di lavoro / storageEncryption | No | No |
> | aree di lavoro / virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | catalogs | Sì | Sì |
> | datacatalogs | Sì | Sì |
> | datacatalogs / origini dati | No | No |
> | datacatalogs / fonti dati / scansioni | No | No |
> | datacatalogs / fonti di dati / scansioni / set di dati | No | No |
> | datacatalogs / datasources / scansioni / trigger | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sì | No |
> | dataFactories / diagnosticSettings | No | No |
> | dataFactories / metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | factories | Sì | No |
> | factory / integrationRuntimes | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | account / dataLakeStoreAccounts | No | No |
> | account / storageAccounts | No | No |
> | account / storageAccounts / contenitori | No | No |
> | account / transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts / eventGridFilters | No | No |
> | account / firewallRegole | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | servizi/ progetti | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | conti / azioni | No | No |
> | conti / condivisioni / set di dati | No | No |
> | conti / azioni / inviti | No | No |
> | account / azioni / providersharesottoscrizioni | No | No |
> | account/ condivisioni / sincronizzazioneImpostazioni | No | No |
> | account / sharesubscriptions | No | No |
> | account/ sharesubscriptions / consumerSourceDataSets | No | No |
> | account/ sharesubscriptions / datasetmapping | No | No |
> | account / sharesubscriptions / trigger | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | servers | Sì | Sì |
> | server / consulenti | No | No |
> | server / chiavi | No | No |
> | server / privateEndpointConnectionProxies | No | No |
> | server / privateEndpointConnections | No | No |
> | server / privateLinkResources | No | No |
> | server / queryTexts | No | No |
> | server / recoverableServers | No | No |
> | server / topQueryStatistics | No | No |
> | server / virtualNetworkRules | No | No |
> | server / waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | servers | Sì | Sì |
> | server / consulenti | No | No |
> | server / chiavi | No | No |
> | server / privateEndpointConnectionProxies | No | No |
> | server / privateEndpointConnections | No | No |
> | server / privateLinkResources | No | No |
> | server / queryTexts | No | No |
> | server / recoverableServers | No | No |
> | server / topQueryStatistics | No | No |
> | server / virtualNetworkRules | No | No |
> | server / waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | gruppi di server | Sì | Sì |
> | servers | Sì | Sì |
> | server / consulenti | No | No |
> | server / chiavi | No | No |
> | server / privateEndpointConnectionProxies | No | No |
> | server / privateEndpointConnections | No | No |
> | server / privateLinkResources | No | No |
> | server / queryTexts | No | No |
> | server / recoverableServers | No | No |
> | server / topQueryStatistics | No | No |
> | server / virtualNetworkRules | No | No |
> | server / waitStatistics | No | No |
> | serverv2 (serverv2) | Sì | Sì |
> | singleServer | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | artefattoSources | Sì | Sì |
> | rollouts | Sì | Sì |
> | serviceTopologies (Topologies servizio) | Sì | Sì |
> | serviceTopologies / servizi | Sì | Sì |
> | serviceTopologies / servizi / serviceUnits | Sì | Sì |
> | steps | Sì | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization (applicazione di virtualizzazione)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | applicationgroups (gruppi di applicazioni) | Sì | Sì |
> | applicationgroups / applicazioni | No | No |
> | applicationgroups / desktop | No | No |
> | applicationgroups / startmenuitems | No | No |
> | hostpools | Sì | Sì |
> | hostpools / sessionhosts | No | No |
> | hostpools / sessionhosts / usersessions | No | No |
> | hostpools / usersessions | No | No |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Sì | Sì |
> | ElasticPools / IotHubTenants | Sì | Sì |
> | ElasticPools / IotHubTenants / securitySettings | No | No |
> | Hub IoT | Sì | Sì |
> | IotHubs / eventGridFilters | No | No |
> | IotHubs / securitySettings | No | No |
> | ProvisioningServices | Sì | Sì |
> | usages | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps (informazioni in base al sistema operativo Microsoft.Dev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | pipeline | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | controllers | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | labcenters | Sì | Sì |
> | labs | Sì | Sì |
> | laboratori / ambienti | Sì | Sì |
> | laboratori / serviceRunners | Sì | Sì |
> | laboratori / macchine virtuali | Sì | Sì |
> | schedules | Sì | Sì |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | domains | Sì | Sì |
> | domini / domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects / clouddeployments | No | No |
> | lcsprojects / connettori | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | services | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | domains | Sì | Sì |
> | domini / argomenti | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | partnerNamespaces | Sì | Sì |
> | partnerNamespaces / eventChannels | No | No |
> | partnerRegistrazioni | Sì | Sì |
> | partnerArgomenti | Sì | Sì |
> | partnerTopics / eventSubscriptions | No | No |
> | systemArgomenti | Sì | Sì |
> | systemTopics / eventSubscriptions | No | No |
> | topics | Sì | Sì |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | spazi dei nomi | Sì | Sì |
> | spazi dei nomi / autorizzazioniregole | No | No |
> | spazi dei nomi / disasterrecoveryconfigs | No | No |
> | spazi dei nomi / eventhubs | No | No |
> | spazi dei nomi / eventhubs / authorizationrules | No | No |
> | spazi dei nomi / eventhubs / consumergroups | No | No |
> | spazi dei nomi / networkruleset | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | featureProviders | No | No |
> | funzionalità | No | No |
> | provider | No | No |
> | subscriptionFeatureRegistrazioni | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | enroll | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | myareas / aree | No | No |
> | myareas / aree / aree | No | No |
> | myareas / aree / aree / galleryelementi | No | No |
> | myareas / aree / galleryitems | No | No |
> | myareas / galleryitems | No | No |
> | register | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Sì | Sì |
> | autoManagedVmConfigurationProfiles | Sì | Sì |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile (profilato) | No | No |
> | softwareAggiornamenti | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sì | Sì |
> | sapMonitors | Sì | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | HSMs dedicati | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | cluster/applicazioni | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | services | Sì | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Macchine | Sì | Sì |
> | macchine / estensioni | Sì | Sì |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | DataManager | Sì | Sì |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | components | Sì | Sì |
> | networkScopes (Ambiti di rete) | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | jobs | Sì | Sì |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | appTemplates (modelli di app) | No | No |
> | IoTApps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Grafico | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools (Bobina hsm) | Sì | Sì |
> | insiemi di credenziali | Sì | Sì |
> | vault / accessPolicies | No | No |
> | vault / eventGridFilters | No | No |
> | volte / segreti | No | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | connectedCluster | Sì | Sì |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | cluster/ databaseconfigurazioni collegate | No | No |
> | cluster / database | No | No |
> | cluster / database / connessioni dati | No | No |
> | cluster/database/eventhubconnections | No | No |
> | cluster/database/principalassignments | No | No |
> | cluster/ connessioni dati | No | No |
> | cluster / principalassignments | No | No |
> | cluster / identità condivise | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sì | Sì |
> | users | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sì | Sì |
> | integrationAccounts | Sì | Sì |
> | integrationAmbienti di servizio | Sì | Sì |
> | integrationServiceEnvironments / managedApis | Sì | Sì |
> | isolatedAmbienti | Sì | Sì |
> | flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sì | Sì |
> | webServices | Sì | Sì |
> | Aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | Sì | Sì |
> | aree di lavoro/ calcola | No | No |
> | aree di lavoro / eventGridFilters | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Manutenzione

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | applyAggiornamenti | No | No |
> | configurationAssegnazioni | No | No |
> | maintenanceConfigurazionIconfigurazioni | Sì | Sì |
> | aggiornamenti | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Identities | No | No |
> | userAssignedIdentities | Sì | Sì |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrazioneAssegnazioni | No | No |
> | registrationDefinitions (definizioni di registrazione) | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | getEntities | No | No |
> | managementGroups | No | No |
> | managementGroups / impostazioni | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts / eventGridFilters | No | No |
> | account / privateAtlases | Sì | Sì |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | offers | No | No |
> | offerTypes | No | No |
> | offerTypes / editori | No | No |
> | offerTypes / editori / offerte | No | No |
> | offerTypes / editori / offerte / piani | No | No |
> | offerTypes / editori / offerte / piani / accordi | No | No |
> | offerTypes / editori / offerte / piani / configurazioni | No | No |
> | offerTypes / editori / offerte / piani / configurazioni / importImage | No | No |
> | privategalleryitems | No | No |
> | PrivateStoreClient | No | No |
> | privateStores | No | No |
> | privateStores / offerte | No | No |
> | products | No | No |
> | server di pubblicazione | No | No |
> | editori / offerte | No | No |
> | editori / offerte / emendamenti | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sì | Sì |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | agreements | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sì | Sì |
> | mediaservices / accountFilters | No | No |
> | mediaservices / risorse | No | No |
> | mediaservices / asset / assetFilters | No | No |
> | mediaservices / contentKeyPolicies | No | No |
> | mediaservices / eventGridFilters | No | No |
> | mediaservices / liveEventOperations | No | No |
> | mediaservices / liveEvents | Sì | Sì |
> | mediaservices / liveEvents / liveOutputs | No | No |
> | mediaservices / liveOutputOperations | No | No |
> | mediaservices / mediaGraphs | No | No |
> | mediaservices / streamingEndpointOperations | No | No |
> | mediaservices / streamingEndpoints | Sì | Sì |
> | mediaservices / streamingLocators | No | No |
> | mediaservices / streamingPolicies | No | No |
> | mediaservices/trasformazioni | No | No |
> | mediaservices/trasforma/lavori | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring (prodotti Microsoft.Microservices4Spring)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | appClusters | Sì | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | valutazioneProgetti | Sì | Sì |
> | migrateprojects | Sì | Sì |
> | moveCollections | Sì | Sì |
> | projects | Sì | Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality (realtà di Microsoft)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | olografitraBroadcastAccounts | Sì | Sì |
> | objectUnderstandingAccounts | Sì | Sì |
> | remoteRenderingAccounts (accountdiremoteRenderingAccounts) | Sì | Sì |
> | spatialAnchorsAccount | Sì | Sì |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | netAppAccount | Sì | No |
> | netAppAccounts / accountBackups | No | No |
> | netAppAccounts / capacityPools | Sì | No |
> | netAppAccounts / capacityPools / volumi | Sì | No |
> | netAppAccounts / capacityPools / volumi / istantanee | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sì | Sì |
> | ApplicationGatewayWebApplicationFirewallPolicies (ApplicazioneGatewayWebApplicationFirewallPolicies) | Sì | Sì |
> | applicationSecurityGroups | Sì | Sì |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | Sì | No |
> | bastionGli ospiti | Sì | Sì |
> | bgpServiceCommunities | No | No |
> | connections | Sì | Sì |
> | ddosCustomPolicies | Sì | Sì |
> | ddosProtectionPlans | Sì | Sì |
> | dnsOperationStatuses | No | No |
> | dnszones | Sì | Sì |
> | dnszones / A | No | No |
> | dnszones / AAAA | No | No |
> | dnszones / tutti | No | No |
> | dnszones / CAA | No | No |
> | dnszones / CNAME | No | No |
> | dnszones / MX | No | No |
> | dnszones / NS | No | No |
> | dnszones / PTR | No | No |
> | dnszones / recordset | No | No |
> | dnszones / SOA | No | No |
> | dnszones / SRV | No | No |
> | dnszones / TXT | No | No |
> | expressRouteCircuits | Sì | Sì |
> | expressRouteCrossConnections | Sì | Sì |
> | expressRouteGateways | Sì | Sì |
> | expressRoutePorts | Sì | Sì |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies (Criteri firewall) | Sì | Sì |
> | frontdoors | Sì, ma limitato (vedi [nota sotto](#frontdoor)) | Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sì, ma limitato (vedi [nota sotto](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Sì, ma limitato (vedi [nota sotto](#frontdoor)) | Sì |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | loadBalancers | Sì | No |
> | localNetworkGateways | Sì | Sì |
> | natGateways | Sì | Sì |
> | networkIntentPolicies | Sì | Sì |
> | networkInterfaces | Sì | Sì |
> | networkProfiles | Sì | Sì |
> | networkSecurityGroups | Sì | Sì |
> | networkWatchers | Sì | No |
> | networkWatchers / connectionMonitors | Sì | No |
> | networkOsservatori / lenti | Sì | No |
> | networkWatchers / pingMeshes | Sì | No |
> | p2sVpnGateways | Sì | Sì |
> | privateDnsOperationStatuses | No | No |
> | privateDns- | Sì | Sì |
> | privateDns- | No | No |
> | privateDns- | No | No |
> | privateDns- | No | No |
> | privateDns- | No | No |
> | privateDns- - MX | No | No |
> | privateDns-ones/ PTR | No | No |
> | privateDns- | No | No |
> | privateDns - SRV | No | No |
> | privateDNS - TXT | No | No |
> | privateDns - virtualNetworkLinks | Sì | Sì |
> | endpoint privati | Sì | Sì |
> | privateLinkServices | Sì | Sì |
> | publicIPAddresses | Sì | Sì |
> | publicIPPrefixes | Sì | Sì |
> | routeFilters | Sì | Sì |
> | routeTables | Sì | Sì |
> | serviceEndpointPolicies | Sì | Sì |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | Sì | Sì |
> | trafficmanagerprofiles/heatMaps | No | No |
> | TrafficManagerUserMetricsKeys | No | No |
> | virtualHubs | Sì | Sì |
> | virtualNetworkGateways | Sì | Sì |
> | virtualNetworks | Sì | Sì |
> | virtualNetworkTaps | Sì | Sì |
> | virtualWans | Sì | Sì |
> | vpnGateways | Sì | No |
> | vpnSites | Sì | Sì |
> | webApplicationFirewallPolicies | Sì | Sì |

<a id="frontdoor" />

> [!NOTE]
> Per il servizio Porta Frontale di Azure, è possibile applicare i tag durante la creazione della risorsa, ma l'aggiornamento o l'aggiunta di tag non è attualmente supportato.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Prossia per notebook | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | No |
> | spazi dei nomi / notificationHubs | Sì | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sì | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | HyperVSiti | Sì | Sì |
> | ImportareSiti | Sì | Sì |
> | Siti Server | Sì | Sì |
> | Siti VMware | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | aree di lavoro | Sì | Sì |
> | aree di lavoro / datiExports | No | No |
> | aree di lavoro/ dataSources | No | No |
> | aree di lavoro / linkedServices | No | No |
> | aree di lavoro / linkedStorageAccounts | No | No |
> | aree di lavoro/ query | No | No |
> | aree di lavoro / scopedPrivateLinkProxies | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | Sì | Sì |
> | solutions | Sì | Sì |
> | Viste | Sì | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | peerings | Sì | Sì |
> | peeringServiceCountries | No | No |
> | peeringServiceProviders | No | No |
> | peeringServices | Sì | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | policyEvents | No | No |
> | policyMetadata (policyMetadata) | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | consoles | No | No |
> | dashboards | Sì | Sì |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | capacities | Sì | Sì |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Aree di lavoro | Sì | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | insiemi di credenziali | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | Sì |
> | spazi dei nomi / autorizzazioniregole | No | No |
> | spazi dei nomi/connessioni ibride | No | No |
> | spazi dei nomi / connessioni ibride / autorizzazioniregole | No | No |
> | spazi dei nomi / wcfrelays | No | No |
> | spazi dei nomi / wcfrelays / authorizationrules | No | No |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp (informazioni in remoto)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | account | No | No |
> | raccolte | Sì | Sì |
> | collezioni / applicazioni | No | No |
> | raccolte / securityprincipals | No | No |
> | modelloImmagini | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | query | Sì | Sì |
> | resourceChangeDetails (ResourceChangeDetails) | No | No |
> | risorsaModifiche | No | No |
> | resources | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No | No |
> | childAvailabilityStatuses | No | No |
> | childResources | No | No |
> | emergentie emergenti | No | No |
> | eventi | No | No |
> | impactedResources | No | No |
> | metadata | No | No |
> | Notifiche | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | deployments | Sì | No |
> | distribuzioni/ operazioni | No | No |
> | distribuzioneScript | Sì | Sì |
> | deploymentScripts / registri | No | No |
> | collegamenti | No | No |
> | notifyResourceJobs | No | No |
> | provider | No | No |
> | resourceGroups | Sì | No |
> | subscriptions | Sì | No |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | Sì | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | adadattivoNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | valutazioni | No | No |
> | autoDismissAlertsRules | No | No |
> | Automazioni | Sì | Sì |
> | AutoProvisioningSettings | No | No |
> | Compliances | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups (gruppi di sicurezza) | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotSecuritySolutions | Sì | Sì |
> | iotSecuritySolutions / analyticsModels | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No | No |
> | jitNetworkAccessPolicies | No | No |
> | networkData (dati di rete) | No | No |
> | criteri | No | No |
> | pricings | No | No |
> | regolamentareStandard | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No | No |
> | secureScoreControlDefinitions | No | No |
> | secureScoreControls | No | No |
> | secureScores | No | No |
> | secureScores / secureScoreControls | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityValutazioni | No | No |
> | Scheda Impostazioni | No | No |
> | subValutazioni | No | No |
> | attività | No | No |
> | topologies | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | aggregations | No | No |
> | alertRules | No | No |
> | alertRuleTemplates (modelli alertRuleTemplates) | No | No |
> | segnalibri | No | No |
> | cases | No | No |
> | DataConnectors (Connettori dati) | No | No |
> | DataConnectorsCheckRequirements (Requisiti di DataConnectorsCheckRequirements) | No | No |
> | entities | No | No |
> | entityQueries (Query di entità) | No | No |
> | Incidenti | No | No |
> | ufficioCondimenti | No | No |
> | Scheda Impostazioni | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | No |
> | spazi dei nomi / autorizzazioniregole | No | No |
> | spazi dei nomi / disasterrecoveryconfigs | No | No |
> | spazi dei nomi / eventgridfilters | No | No |
> | spazi dei nomi / networkruleset | No | No |
> | spazi dei nomi / code | No | No |
> | spazi dei nomi / code / autorizzazioniregole | No | No |
> | spazi dei nomi / argomenti | No | No |
> | spazi dei nomi / argomenti / autorizzazioniregole | No | No |
> | spazi dei nomi / argomenti / sottoscrizioni | No | No |
> | spazi dei nomi / argomenti / sottoscrizioni / regole | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | clusters | Sì | Sì |
> | cluster/applicazioni | No | No |
> | containerGroups | Sì | Sì |
> | contenitoriSet | Sì | Sì |
> | edgeclusters | Sì | Sì |
> | edgecluster / applicazioni | No | No |
> | managedclusters | Sì | Sì |
> | managedclusters / nodetypes | No | No |
> | networks | Sì | Sì |
> | secretstores | Sì | Sì |
> | secreti / certificati | No | No |
> | secreti / segreti | No | No |
> | volumes | Sì | Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | containerGroups | Sì | Sì |
> | gateways | Sì | Sì |
> | networks | Sì | Sì |
> | chiavi private | Sì | Sì |
> | volumes | Sì | Sì |

## <a name="microsoftservices"></a>Microsoft.Servizi

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
> | rollouts | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | SignalR | Sì | Sì |
> | SignalR / eventGridFilters | No | No |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sì | Sì |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan (piano)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sì | Sì |
> | scala Web | Sì | Sì |
> | jitRequests | Sì | Sì |

## <a name="microsoftspoolservice"></a>Servizio Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | registeredSubscriptions (Sottoscrizioni registrate) | No | No |
> | Bobine | Sì | Sì |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sì | Sì |
> | managedInstances / database | Sì (vedere [la nota seguente](#sqlnote)) | Sì |
> | managedInstances / database / backupShortTermRetentionPolicies | No | No |
> | managedInstances / database / schemi / tabelle / colonne / sensitivityLabels | No | No |
> | managedInstances / database / vulnerabilitàValutazioni | No | No |
> | managedInstances / database / vulnerabilitàValutazioni / regole / linee di base | No | No |
> | managedInstances / encryptionProtector | No | No |
> | managedInstances / chiavi | No | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / vulnerabilitàValutazioni | No | No |
> | servers | Sì | Sì |
> | server / amministratori | No | No |
> | server / communicationLinks | No | No |
> | server / database | Sì (vedere [la nota seguente](#sqlnote)) | Sì |
> | server / encryptionProtector | No | No |
> | server / firewallRegole | No | No |
> | server / chiavi | No | No |
> | server / restorableDroppedDatabases | No | No |
> | server / obiettivi di servizio | No | No |
> | server / tdeCertificati | No | No |
> | virtualClusters | No | No |

<a id="sqlnote" />

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database di Azure SQL Data Warehouse, supportano i tag. I database di Azure SQL Data Warehouse devono essere nello stato attivo (non in pausa).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sì | Sì |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | Sì | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sì | Sì |
> | storageAccounts / blobServices | No | No |
> | storageAccounts / fileServices | No | No |
> | storageAccounts / queueServices | No | No |
> | storageAccounts / servizi | No | No |
> | storageAccounts / servizi / metricDefinitions | No | No |
> | storageAccounts / tableServices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | Cache | Sì | Sì |
> | cache / storageTargets | No | No |
> | usageModel | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | replicationGruppi | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / flussi di lavoro | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / flussi di lavoro | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices / registeredServers | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices / flussi di lavoro | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | managers | Sì | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sì (vedere la nota seguente) | Sì |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | cancel | No | No |
> | CreateSubscription | No | No |
> | abilitare | No | No |
> | ridenominazione | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |
> | subscriptions | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | environments | Sì | No |
> | ambienti / accessPolicies | No | No |
> | ambienti/origini eventi | Sì | No |
> | ambienti /referenceDataSets | Sì | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | dedicataCloudNodes | Sì | Sì |
> | dedicatoCloudServices | Sì | Sì |
> | virtualMachines | Sì | Sì |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | devices | Sì | Sì |
> | registeredSubscriptions (Sottoscrizioni registrate) | No | No |
> | Fornitori | No | No |
> | fornitori / skus | No | No |
> | fornitori / vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | Vnfs (in vnfs) | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts / apiAcls | No | No |
> | apiManagementAccounts / apis | No | No |
> | apiManagementAccounts / apis / apiAcls | No | No |
> | apiManagementAccounts / apis / connectionAcls | No | No |
> | apiManagementAccounts / apis / connessioni | No | No |
> | apiManagementAccounts / apis / connessioni / connectionAcls | No | No |
> | apiManagementAccounts / apis / localizedDefinitions | No | No |
> | apiManagementAccounts / connectionAcls | No | No |
> | apiManagementAccounts / connessioni | No | No |
> | billingMeters | No | No |
> | certificates | Sì | Sì |
> | connectionGateways | Sì | Sì |
> | connections | Sì | Sì |
> | customApis | Sì | Sì |
> | deletedSites | No | No |
> | hostingEnvironments | Sì | Sì |
> | hostingEnvironments / eventGridFilters | No | No |
> | hostingEnvironments / multiRolePools | No | No |
> | hostingEnvironments / workerPools | No | No |
> | kubeAmbienti | Sì | Sì |
> | publishingUsers | No | No |
> | raccomandazioni di film | No | No |
> | resourceHealthMetadata | No | No |
> | runtimes | No | No |
> | serverFarms | Sì | Sì |
> | serverFarms / eventGridFilters | No | No |
> | siti | Sì | Sì |
> | siti / config  | No | No |
> | siti / eventGridFilters | No | No |
> | siti / hostNameBindings | No | No |
> | siti / networkConfig | No | No |
> | siti / premieraddons | Sì | Sì |
> | siti / slot | Sì | Sì |
> | siti / slot / eventGridFilters | No | No |
> | siti / slot / hostNameBindings | No | No |
> | siti / slot / networkConfig | No | No |
> | sourceControls | No | No |
> | staticazioneSiti | Sì | Sì |
> | validate | No | No |
> | verifyHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sì | Sì |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Etichetta nel rapporto costi |
> | ------------- | ----------- | ----------- |
> | components | No | No |
> | componentsSummary | No | No |
> | monitorInstances | No | No |
> | monitorInstancesSummary | No | No |
> | monitors | No | No |
> | notificationSettings | No | No |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](tag-resources.md).
