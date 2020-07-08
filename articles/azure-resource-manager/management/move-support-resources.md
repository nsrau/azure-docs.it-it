---
title: Supporto per lo spostamento per tipo di risorsa
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 7f241e12200101e2f8f9efa7cf31e4483b2d4229
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044533"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse

Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Vengono inoltre fornite informazioni sulle condizioni speciali da considerare quando si sposta una risorsa.

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft. APPSERVICE](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [MICROSOFT. BIZTALKSERVICES](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. CONTENTMODERATOR](#microsoftcontentmoderator)
> - [Microsoft. CORTANAANALYTICS](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft. CUSTOMERINSIGHTS](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DATACONNECT](#microsoftdataconnect)
> - [Microsoft. DATAEXCHANGE](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft. DATALAKE](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft. GENOMICA](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft. LOCATIONBASEDSERVICES](#microsoftlocationbasedservices)
> - [Microsoft. LOCATIONSERVICES](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MACHINELEARNINGCOMPUTE](#microsoftmachinelearningcompute)
> - [Microsoft. MACHINELEARNINGEXPERIMENTATION](#microsoftmachinelearningexperimentation)
> - [Microsoft. MACHINELEARNINGMODELMANAGEMENT](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. MANAGEDNETWORK](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. MICROSERVICES4SPRING](#microsoftmicroservices4spring)
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
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft. SERVERMANAGEMENT](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Servizi](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. STORAGECACHE](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft. STORAGESYNCDEV](#microsoftstoragesyncdev)
> - [Microsoft. STORAGESYNCINT](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. STREAMANALYTICSEXPLORER](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | No | No |
> | domainservices/oucontainer | No | No |
> | locations | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | azureadmetrics | No | No |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | Operazioni | No | No |
> | privatelinkforazuread | No | No |
> | tenants | No | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationresults | No | No |
> | Operazioni | No | No |
> | supportproviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | agents | No | No |
> | anonymousapiusers | No | No |
> | configurazione | No | No |
> | log | No | No |
> | Operazioni | No | No |
> | reports | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurazioni | No | No |
> | generaterecommendations | No | No |
> | metadata | No | No |
> | Operazioni | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | No | No |
> | alerts | No | No |
> | alertslist | No | No |
> | alertsmetadata | No | No |
> | alertssummary | No | No |
> | alertssummarylist | No | No |
> | Operazioni | No | No |
> | smartdetectoralertrules | No | No |
> | smartgroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | servers | No | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | No | No |
> | checknameavailability | No | No |
> | checkservicenameavailability | No | No |
> | Operazioni | No | No |
> | reportfeedback | No | No |
> | service | Sì | Sì |
> | validateservicename | No | No |

> [!IMPORTANT]
> Non è possibile spostare un servizio Gestione API impostato sull'utilizzo delle SKU.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | configurationstores | No | No |
> | configurationstores/eventgridfilters | No | No |
> | locations | No | No |
> | locations/operationsstatus | No | No |
> | Operazioni | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatus | No | No |
> | Operazioni | No | No |
> | spring | No | No |
> | primavera/app | No | No |
> | Spring/Apps/distribuzioni | No | No |

## <a name="microsoftappservice"></a>Microsoft. APPSERVICE

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentities | No | No |
> | gateways | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | No | No |
> | defaultproviders | No | No |
> | locations | No | No |
> | località/defaultProvider | No | No |
> | Operazioni | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | No | No |
> | classicadministrators | No | No |
> | dataaliases | No | No |
> | denyassignments | No | No |
> | elevateaccess | No | No |
> | findorphanroleassignments | No | No |
> | locks | No | No |
> | Operazioni | No | No |
> | operationstatus | No | No |
> | autorizzazioni | No | No |
> | policyassignments | No | No |
> | policydefinitions | No | No |
> | policysetdefinitions | No | No |
> | privatelinkassociations | No | No |
> | provideroperations | No | No |
> | resourcemanagementprivatelinks | No | No |
> | roleassignments | No | No |
> | roleassignmentsusagemetrics | No | No |
> | roledefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sì | Sì |
> | automationaccounts/configurations | No | No |
> | automationaccounts/jobs | No | No |
> | automationaccounts/privateendpointconnectionproxies | No | No |
> | automationaccounts/privateendpointconnections | No | No |
> | automationaccounts/privatelinkresources | No | No |
> | automationaccounts/runbooks | No | No |
> | automationaccounts/softwareupdateconfigurations | No | No |
> | automationaccounts/webhooks | No | No |
> | Operazioni | No | No |

> [!IMPORTANT]
> Manuali operativi deve esistere nello stesso gruppo di risorse dell'account di automazione.
>
> Per informazioni, vedere [spostare l'account di automazione di Azure in un'altra sottoscrizione](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checkquotaavailability | No | No |
> | località/checktrialavailability | No | No |
> | Operazioni | No | No |
> | privateclouds | No | No |
> | privateclouds/clusters | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sì | Sì |
> | b2ctenants | No | No |
> | checknameavailability | No | No |
> | guestusages | Sì | Sì |
> | Operazioni | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Sì | Sì |
> | hybriddatamanagers | Sì | Sì |
> | Operazioni | No | No |
> | postgresinstances | Sì | Sì |
> | sqlinstances | Sì | Sì |
> | sqlmanagedinstances | Sì | Sì |
> | sqlserverinstances | Sì | Sì |
> | sqlserverregistrations | Sì | Sì |
> | sqlserverregistrations/sqlservers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | No | No |
> | edgesubscriptions | No | No |
> | Operazioni | No | No |
> | registrations | No | No |
> | registrations/customersubscriptions | No | No |
> | registrations/products | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | Operazioni | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sì | Sì |
> | locations | No | No |
> | locations/accountoperationresults | No | No |
> | locations/checknameavailability | No | No |
> | locations/quotas | No | No |
> | Operazioni | No | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | No | No |
> | billingaccounts/agreements | No | No |
> | billingaccounts/billingpermissions | No | No |
> | billingaccounts/billingprofiles | No | No |
> | billingaccounts/billingprofiles/availablebalance | No | No |
> | billingaccounts/billingprofiles/billingpermissions | No | No |
> | billingaccounts/billingprofiles/billingroleassignments | No | No |
> | billingaccounts/billingprofiles/billingroledefinitions | No | No |
> | billingaccounts/billingprofiles/billingsubscriptions | No | No |
> | billingaccounts/billingprofiles/createbillingroleassignment | No | No |
> | billingaccounts/billingprofiles/customers | No | No |
> | billingaccounts/billingprofiles/instructions | No | No |
> | billingaccounts/billingprofiles/invoices | No | No |
> | billingaccounts/billingprofiles/invoices/pricesheet | No | No |
> | billingaccounts/billingprofiles/invoices/transactions | No | No |
> | billingaccounts/billingprofiles/invoicesections | No | No |
> | billingaccounts/billingprofiles/invoicesections/billingpermissions | No | No |
> | billingaccounts/billingprofiles/invoicesections/billingroleassignments | No | No |
> | billingaccounts/billingprofiles/invoicesections/billingroledefinitions | No | No |
> | billingaccounts/billingprofiles/invoicesections/billingsubscriptions | No | No |
> | billingaccounts/billingprofiles/invoicesections/createbillingroleassignment | No | No |
> | billingaccounts/billingprofiles/invoicesections/initiatetransfer | No | No |
> | billingaccounts/billingprofiles/invoicesections/products | No | No |
> | billingaccounts/billingprofiles/invoicesections/products/transfer | No | No |
> | billingaccounts/billingprofiles/invoicesections/products/updateautorenew | No | No |
> | billingaccounts/billingprofiles/invoicesections/transactions | No | No |
> | billingaccounts/billingprofiles/invoicesections/transfers | No | No |
> | billingaccounts/billingprofiles/patchoperations | No | No |
> | billingaccounts/billingprofiles/paymentmethods | No | No |
> | billingaccounts/billingprofiles/policies | No | No |
> | billingaccounts/billingprofiles/pricesheet | No | No |
> | billingaccounts/billingprofiles/pricesheetdownloadoperations | No | No |
> | billingaccounts/billingprofiles/products | No | No |
> | billingaccounts/billingprofiles/transactions | No | No |
> | billingaccounts/billingroleassignments | No | No |
> | billingaccounts/billingroledefinitions | No | No |
> | billingaccounts/billingsubscriptions | No | No |
> | billingaccounts/billingsubscriptions/invoices | No | No |
> | billingaccounts/createbillingroleassignment | No | No |
> | billingaccounts/createinvoicesectionoperations | No | No |
> | billingaccounts/customers | No | No |
> | billingaccounts/customers/billingpermissions | No | No |
> | billingaccounts/customers/billingsubscriptions | No | No |
> | billingaccounts/customers/initiatetransfer | No | No |
> | billingaccounts/customers/policies | No | No |
> | billingaccounts/customers/products | No | No |
> | billingaccounts/customers/transactions | No | No |
> | billingaccounts/customers/transfers | No | No |
> | billingaccounts/departments | No | No |
> | billingaccounts/reparti/billingpermissions | No | No |
> | billingaccounts/reparti/billingroleassignments | No | No |
> | billingaccounts/reparti/billingroledefinitions | No | No |
> | billingaccounts/enrollmentaccounts | No | No |
> | billingaccounts / enrollmentaccounts / billingpermissions | No | No |
> | billingaccounts / enrollmentaccounts / billingroleassignments | No | No |
> | billingaccounts / enrollmentaccounts / billingroledefinitions | No | No |
> | billingaccounts/invoices | No | No |
> | billingaccounts/fatture/transazioni | No | No |
> | billingaccounts/invoicesections | No | No |
> | billingaccounts/invoicesections/billingsubscriptionmoveoperations | No | No |
> | billingaccounts/invoicesections/billingsubscriptions | No | No |
> | billingaccounts/invoicesections/billingsubscriptions/transfer | No | No |
> | billingaccounts/invoicesections/elevate | No | No |
> | billingaccounts/invoicesections/initiatetransfer | No | No |
> | billingaccounts/invoicesections/patchoperations | No | No |
> | billingaccounts/invoicesections/productmoveoperations | No | No |
> | billingaccounts/invoicesections/products | No | No |
> | billingaccounts/invoicesections/products/transfer | No | No |
> | billingaccounts/invoicesections/products/updateautorenew | No | No |
> | billingaccounts/invoicesections/producttransfersresults | No | No |
> | billingaccounts/invoicesections/transactions | No | No |
> | billingaccounts/invoicesections/transfers | No | No |
> | billingaccounts/lineofcredit | No | No |
> | billingaccounts/listinvoicesectionswithcreatesubscriptionpermission | No | No |
> | billingaccounts/operationresults | No | No |
> | billingaccounts/patchoperations | No | No |
> | billingaccounts/paymentmethods | No | No |
> | billingaccounts/products | No | No |
> | billingaccounts/transactions | No | No |
> | billingperiods | No | No |
> | billingpermissions | No | No |
> | billingproperty | No | No |
> | billingroleassignments | No | No |
> | billingroledefinitions | No | No |
> | createbillingroleassignment | No | No |
> | departments | No | No |
> | enrollmentaccounts | No | No |
> | invoices | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | operationstatus | No | No |
> | transfers | No | No |
> | transfers/accepttransfer | No | No |
> | transfers/declinetransfer | No | No |
> | transfers/operationstatus | No | No |
> | transfers/validatetransfer | No | No |
> | validateaddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | No | No |
> | mapapis | No | No |
> | Operazioni | No | No |
> | updatecommunicationpreference | No | No |

## <a name="microsoftbiztalkservices"></a>MICROSOFT. BIZTALKSERVICES

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | No | No |
> | cordamembers | No | No |
> | locations | No | No |
> | locations/blockchainmemberoperationresults | No | No |
> | locations/checknameavailability | No | No |
> | locations/listconsortiums | No | No |
> | locations/watcheroperationresults | No | No |
> | Operazioni | No | No |
> | watchers | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |
> | tokenservices | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |
> | blueprintassignments/assignmentoperations | No | No |
> | blueprintassignments/operations | No | No |
> | blueprints | No | No |
> | blueprints/artifacts | No | No |
> | blueprints/versions | No | No |
> | blueprints/versions/artifacts | No | No |
> | Operazioni | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | No | No |
> | botservices/channels | No | No |
> | botservices/connections | No | No |
> | checknameavailability | No | No |
> | listauthserviceproviders | No | No |
> | Operazioni | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/operationresults | No | No |
> | locations/operationsstatus | No | No |
> | Operazioni | No | No |
> | redis | Sì | Sì |
> | Redis/eventgridfilters | No | No |
> | redis/privatelinkresources | No | No |
> | redisenterprise | Sì | Sì |

> [!IMPORTANT]
> Se l'istanza di cache di Azure per Redis è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Vedere [Limitazioni di spostamento della rete](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | No | No |
> | calculateexchange | No | No |
> | calculateprice | No | No |
> | calculatepurchaseprice | No | No |
> | catalogs | No | No |
> | checkoffers | No | No |
> | checkpurchasestatus | No | No |
> | checkscopes | No | No |
> | commercialreservationorders | No | No |
> | exchange | No | No |
> | listbenefits | No | No |
> | Operazioni | No | No |
> | placepurchaseorder | No | No |
> | reservationorders | No | No |
> | reservationorders/availablescopes | No | No |
> | reservationorders/calculaterefund | No | No |
> | reservationorders/merge | No | No |
> | reservationorders/reservations | No | No |
> | reservationorders/reservations/availablescopes | No | No |
> | reservationorders/reservations/revisions | No | No |
> | reservationorders/return | No | No |
> | reservationorders/split | No | No |
> | reservationorders/swap | No | No |
> | reservations | No | No |
> | resources | No | No |
> | validatereservationorder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | No | No |
> | cdnwebapplicationfirewallpolicies | No | No |
> | checknameavailability | No | No |
> | checkresourceusage | No | No |
> | edgenodes | No | No |
> | operationresults | No | No |
> | operationresults/profileresults | No | No |
> | operationresults/profileresults/endpointresults | No | No |
> | operationresults/profileresults/endpointresults/customdomainresults | No | No |
> | operationresults/profileresults/endpointresults/origingroupresults | No | No |
> | operationresults/profileresults/endpointresults/originresults | No | No |
> | Operazioni | No | No |
> | Profili | Sì | Sì |
> | profiles/endpoints | No | No |
> | profiles/endpoints/customdomains | No | No |
> | profiles/endpoints/origingroups | No | No |
> | profiles/endpoints/origins | No | No |
> | validateprobe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sì | Sì |
> | certificateorders/certificates | No | No |
> | Operazioni | No | No |
> | validatecertificateregistrationinformation | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | No | No |
> | checkdomainnameavailability | No | No |
> | domainnames | No | No |
> | domainnames/capabilities | No | No |
> | domainnames/internalloadbalancers | No | No |
> | domainnames/servicecertificates | No | No |
> | domainnames/slots | No | No |
> | domainnames/slots/roles | No | No |
> | domainnames/slots/roles/metricdefinitions | No | No |
> | domainnames/slots/roles/metrics | No | No |
> | movesubscriptionresources | No | No |
> | operatingsystemfamilies | No | No |
> | operatingsystems | No | No |
> | Operazioni | No | No |
> | operationstatuses | No | No |
> | quotas | No | No |
> | resourcetypes | No | No |
> | validatesubscriptionmoveavailability | No | No |
> | virtualmachines | No | No |
> | virtualmachines/diagnosticsettings | No | No |
> | virtualmachines/metricdefinitions | No | No |
> | virtualmachines/metrics | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutecrossconnections/peerings | No | No |
> | gatewaysupporteddevices | No | No |
> | networksecuritygroups | No | No |
> | Operazioni | No | No |
> | quotas | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | No | No |
> | virtualnetworks/virtualnetworkpeerings | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | No | No |
> | checkstorageaccountavailability | No | No |
> | disks | No | No |
> | images | No | No |
> | Operazioni | No | No |
> | osimages | No | No |
> | osplatformimages | No | No |
> | publicimages | No | No |
> | quotas | No | No |
> | storageaccounts | No | No |
> | storageaccounts/blobservices | No | No |
> | storageaccounts/fileservices | No | No |
> | storageaccounts/metricdefinitions | No | No |
> | storageaccounts/metrics | No | No |
> | storageaccounts/queueservices | No | No |
> | storageaccounts/services | No | No |
> | storageaccounts/services/diagnosticsettings | No | No |
> | storageaccounts/services/metricdefinitions | No | No |
> | storageaccounts/services/metrics | No | No |
> | storageaccounts/tableservices | No | No |
> | storageaccounts/vmimages | No | No |
> | vmimages | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | checkdomainavailability | No | No |
> | locations | No | No |
> | locations/checkskuavailability | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |
> | ratecard | No | No |
> | usageaggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sì | Sì |
> | CloudServices | No | No |
> | diskaccesses | No | No |
> | diskencryptionsets | No | No |
> | disks | Sì | Sì |
> | galleries | No | No |
> | galleries/images | No | No |
> | galleries/images/versions | No | No |
> | hostgroups | No | No |
> | hostgroups/hosts | No | No |
> | images | Sì | Sì |
> | locations | No | No |
> | locations/artifactpublishers | No | No |
> | locations/capsoperations | No | No |
> | locations/diskoperations | No | No |
> | locations/loganalytics | No | No |
> | locations/operations | No | No |
> | locations/publishers | No | No |
> | locations/runcommands | No | No |
> | locations/usages | No | No |
> | locations/virtualmachines | No | No |
> | locations/vmsizes | No | No |
> | locations/vsmoperations | No | No |
> | Operazioni | No | No |
> | proximityplacementgroups | Sì | Sì |
> | restorepointcollections | No | No |
> | restorepointcollections/restorepoints | No | No |
> | sharedvmextensions | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/versions | No | No |
> | snapshots | Sì | Sì |
> | sshpublickeys | No | No |
> | swiftlets | No | No |
> | virtualmachines | Sì | Sì |
> | virtualmachines/extensions | Sì | Sì |
> | virtualmachines/metricdefinitions | No | No |
> | virtualmachines/runcommands | Sì | Sì |
> | virtualmachinescalesets | Sì | Sì |
> | virtualmachinescalesets/extensions | No | No |
> | virtualmachinescalesets/networkinterfaces | No | No |
> | virtualmachinescalesets/publicipaddresses | No | No |
> | virtualmachinescalesets/virtualmachines | No | No |
> | virtualmachinescalesets/virtualmachines/networkinterfaces | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento di macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cachenodes | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | No | No |
> | balances | No | No |
> | budgets | No | No |
> | charges | No | No |
> | costtags | No | No |
> | credits | No | No |
> | eventi | No | No |
> | forecasts | No | No |
> | lots | No | No |
> | marketplaces | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | operationstatus | No | No |
> | pricesheets | No | No |
> | products | No | No |
> | reservationdetails | No | No |
> | reservationrecommendationdetails | No | No |
> | reservationrecommendations | No | No |
> | reservationsummaries | No | No |
> | reservationtransactions | No | No |
> | tags | No | No |
> | tenants | No | No |
> | terms | No | No |
> | usagedetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |
> | locations | No | No |
> | locations/cachedimages | No | No |
> | locations/capabilities | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operations | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |
> | serviceassociationlinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/authorize | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
> | locations/setupauth | No | No |
> | Operazioni | No | No |
> | registries | Sì | Sì |
> | registries/agentpools | No | No |
> | registries/agentpools/listqueuestatus | No | No |
> | registries/builds | No | No |
> | registries/builds/cancel | No | No |
> | registries/builds/getloglink | No | No |
> | registries/buildtasks | No | No |
> | registries/buildtasks/listsourcerepositoryproperties | No | No |
> | registries/buildtasks/steps | No | No |
> | registries/buildtasks/steps/listbuildarguments | No | No |
> | registries/eventgridfilters | No | No |
> | registries/exportpipelines | No | No |
> | registries/generatecredentials | No | No |
> | registries/getbuildsourceuploadurl | No | No |
> | registries/getcredentials | No | No |
> | registries/importimage | No | No |
> | registries/importpipelines | No | No |
> | registries/listbuildsourceuploadurl | No | No |
> | registries/listcredentials | No | No |
> | registries/listpolicies | No | No |
> | registries/listusages | No | No |
> | registries/pipelineruns | No | No |
> | registries/privateendpointconnectionproxies | No | No |
> | registries/privateendpointconnectionproxies/validate | No | No |
> | registries/privateendpointconnections | No | No |
> | registries/privatelinkresources | No | No |
> | registries/queuebuild | No | No |
> | registries/regeneratecredential | No | No |
> | registries/regeneratecredentials | No | No |
> | registries/replications | No | No |
> | registries/runs | No | No |
> | registries/runs/cancel | No | No |
> | registries/runs/listlogsasurl | No | No |
> | registries/schedulerun | No | No |
> | registries/scopemaps | No | No |
> | registries/taskruns | No | No |
> | registries/taskruns/listdetails | No | No |
> | registries/tasks | No | No |
> | registries/tasks/listdetails | No | No |
> | registries/tokens | No | No |
> | registries/updatepolicies | No | No |
> | registries/webhooks | No | No |
> | registries/webhooks/getcallbackconfig | No | No |
> | registries/webhooks/listevents | No | No |
> | registries/webhooks/ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | No | No |
> | locations | No | No |
> | locations/openshiftclusters | No | No |
> | locations/operationresults | No | No |
> | locations/operations | No | No |
> | locations/orchestrators | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |
> | Operazioni | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft. CONTENTMODERATOR

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CORTANAANALYTICS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alerts | No | No |
> | billingaccounts | No | No |
> | budgets | No | No |
> | cloudconnectors | No | No |
> | dell'account di integrazione | No | No |
> | costallocationrules | No | No |
> | departments | No | No |
> | dimensions | No | No |
> | enrollmentaccounts | No | No |
> | exports | No | No |
> | externalbillingaccounts | No | No |
> | externalbillingaccounts/alerts | No | No |
> | externalbillingaccounts/dimensions | No | No |
> | externalbillingaccounts/forecast | No | No |
> | externalbillingaccounts/query | No | No |
> | externalsubscriptions | No | No |
> | externalsubscriptions/alerts | No | No |
> | externalsubscriptions/dimensions | No | No |
> | externalsubscriptions/forecast | No | No |
> | externalsubscriptions/query | No | No |
> | previsione | No | No |
> | Operazioni | No | No |
> | query | No | No |
> | register | No | No |
> | reportconfigs | No | No |
> | reports | No | No |
> | Scheda Impostazioni | No | No |
> | showbackrules | No | No |
> | Viste | No | No |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft. CUSTOMERINSIGHTS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |
> | requests | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | resourceproviders | No | No |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | No | No |
> | locations | No | No |
> | locations/availableskus | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/regionconfiguration | No | No |
> | locations/validateaddress | No | No |
> | locations/validateinputs | No | No |
> | Operazioni | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | No | No |
> | databoxedgedevices | Sì | Sì |
> | databoxedgedevices/checknameavailability | No | No |
> | Operazioni | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/getnetworkpolicies | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | aree di lavoro | No | No |
> | workspaces/dbworkspaces | No | No |
> | workspaces/virtualnetworkpeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | No | No |
> | checknameavailability | No | No |
> | datacatalogs | No | No |
> | locations | No | No |
> | locations/jobs | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftdataconnect"></a>Microsoft. DATACONNECT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft. DATAEXCHANGE

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | No | No |
> | plans | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | No | No |
> | checkdatafactorynameavailability | No | No |
> | datafactories | No | No |
> | datafactories/diagnosticsettings | No | No |
> | datafactories/metricdefinitions | No | No |
> | datafactoryschema | No | No |
> | factories | Sì | Sì |
> | factories/integrationruntimes | No | No |
> | locations | No | No |
> | locations/configurefactoryrepo | No | No |
> | locations/getfeaturevalue | No | No |
> | Operazioni | No | No |

## <a name="microsoftdatalake"></a>Microsoft. DATALAKE

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | accounts/datalakestoreaccounts | No | No |
> | accounts/storageaccounts | No | No |
> | accounts/storageaccounts/containers | No | No |
> | accounts/storageaccounts/containers/listsastokens | No | No |
> | locations | No | No |
> | locations/capability | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | accounts/eventgridfilters | No | No |
> | accounts/firewallrules | No | No |
> | locations | No | No |
> | locations/capability | No | No |
> | locations/checknameavailability | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | services | No | No |
> | services/projects | No | No |
> | slot | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | No | No |
> | locations | No | No |
> | Operazioni | No | No |
> | resourceoperationgatekeepers | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |
> | accounts/shares | No | No |
> | accounts/shares/datasets | No | No |
> | accounts/shares/invitations | No | No |
> | accounts/shares/providersharesubscriptions | No | No |
> | accounts/shares/synchronizationsettings | No | No |
> | accounts/sharesubscriptions | No | No |
> | accounts/sharesubscriptions/consumersourcedatasets | No | No |
> | accounts/sharesubscriptions/datasetmappings | No | No |
> | accounts/sharesubscriptions/triggers | No | No |
> | listinvitations | No | No |
> | locations | No | No |
> | locations/consumerinvitations | No | No |
> | locations/operationresults | No | No |
> | locations/rejectinvitation | No | No |
> | Operazioni | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/azureasyncoperation | No | No |
> | locations/operationresults | No | No |
> | locations/performancetiers | No | No |
> | locations/privateendpointconnectionazureasyncoperation | No | No |
> | locations/privateendpointconnectionoperationresults | No | No |
> | locations/privateendpointconnectionproxyazureasyncoperation | No | No |
> | locations/privateendpointconnectionproxyoperationresults | No | No |
> | locations/recommendedactionsessionsazureasyncoperation | No | No |
> | locations/recommendedactionsessionsoperationresults | No | No |
> | locations/securityalertpoliciesazureasyncoperation | No | No |
> | locations/securityalertpoliciesoperationresults | No | No |
> | locations/serverkeyazureasyncoperation | No | No |
> | locations/serverkeyoperationresults | No | No |
> | Operazioni | No | No |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/privateendpointconnectionproxies | No | No |
> | servers/privateendpointconnections | No | No |
> | servers/privatelinkresources | No | No |
> | servers/querytexts | No | No |
> | servers/recoverableservers | No | No |
> | servers/topquerystatistics | No | No |
> | servers/virtualnetworkrules | No | No |
> | servers/waitstatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/administratorazureasyncoperation | No | No |
> | locations/administratoroperationresults | No | No |
> | locations/azureasyncoperation | No | No |
> | locations/operationresults | No | No |
> | locations/performancetiers | No | No |
> | locations/privateendpointconnectionazureasyncoperation | No | No |
> | locations/privateendpointconnectionoperationresults | No | No |
> | locations/privateendpointconnectionproxyazureasyncoperation | No | No |
> | locations/privateendpointconnectionproxyoperationresults | No | No |
> | locations/recommendedactionsessionsazureasyncoperation | No | No |
> | locations/recommendedactionsessionsoperationresults | No | No |
> | locations/securityalertpoliciesazureasyncoperation | No | No |
> | locations/securityalertpoliciesoperationresults | No | No |
> | locations/serverkeyazureasyncoperation | No | No |
> | locations/serverkeyoperationresults | No | No |
> | Operazioni | No | No |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | servers/privateendpointconnectionproxies | No | No |
> | servers/privateendpointconnections | No | No |
> | servers/privatelinkresources | No | No |
> | servers/querytexts | No | No |
> | servers/recoverableservers | No | No |
> | servers/topquerystatistics | No | No |
> | servers/virtualnetworkrules | No | No |
> | servers/waitstatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/administratorazureasyncoperation | No | No |
> | locations/administratoroperationresults | No | No |
> | locations/azureasyncoperation | No | No |
> | locations/operationresults | No | No |
> | locations/performancetiers | No | No |
> | locations/privateendpointconnectionazureasyncoperation | No | No |
> | locations/privateendpointconnectionoperationresults | No | No |
> | locations/privateendpointconnectionproxyazureasyncoperation | No | No |
> | locations/privateendpointconnectionproxyoperationresults | No | No |
> | locations/recommendedactionsessionsazureasyncoperation | No | No |
> | locations/recommendedactionsessionsoperationresults | No | No |
> | locations/securityalertpoliciesazureasyncoperation | No | No |
> | locations/securityalertpoliciesoperationresults | No | No |
> | locations/serverkeyazureasyncoperation | No | No |
> | locations/serverkeyoperationresults | No | No |
> | Operazioni | No | No |
> | servergroups | No | No |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | servers/privateendpointconnectionproxies | No | No |
> | servers/privateendpointconnections | No | No |
> | servers/privatelinkresources | No | No |
> | servers/querytexts | No | No |
> | servers/recoverableservers | No | No |
> | servers/topquerystatistics | No | No |
> | servers/virtualnetworkrules | No | No |
> | servers/waitstatistics | No | No |
> | serversv2 | Sì | Sì |
> | singleservers | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | rollouts | No | No |
> | servicetopologies | No | No |
> | servicetopologies/services | No | No |
> | servicetopologies/services/serviceunits | No | No |
> | steps | No | No |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | No | No |
> | applicationgroups/applications | No | No |
> | applicationgroups/desktops | No | No |
> | applicationgroups/startmenuitems | No | No |
> | hostpools | No | No |
> | hostpools/sessionhosts | No | No |
> | hostpools/sessionhosts/usersessions | No | No |
> | hostpools/usersessions | No | No |
> | Operazioni | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checkprovisioningservicenameavailability | No | No |
> | elasticpools | Sì | Sì |
> | elasticpools/iothubtenants | Sì | Sì |
> | iothubs | Sì | Sì |
> | iothubs/eventgridfilters | No | No |
> | iothubs/SecuritySettings | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | provisioningservices | Sì | Sì |
> | usages | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | No | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | No | No |
> | controllers/listconnectiondetails | No | No |
> | locations | No | No |
> | locations/checkcontainerhostmapping | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | labs | No | No |
> | labs/environments | No | No |
> | labs/servicerunners | No | No |
> | labs/virtualmachines | No | No |
> | locations | No | No |
> | locations/operations | No | No |
> | Operazioni | No | No |
> | schedules | No | No |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | No | No |
> | digitaltwinsinstances/endpoint | No | No |
> | digitaltwinsinstances/OperationResult | No | No |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | Operazioni | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | No | No |
> | databaseaccounts | Sì | Sì |
> | locations | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
> | locations/operationsstatus | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | No | No |
> | domains | Sì | Sì |
> | domains/domainownershipidentifiers | No | No |
> | generatessorequest | No | No |
> | listdomainrecommendations | No | No |
> | Operazioni | No | No |
> | topleveldomains | No | No |
> | validatedomainregistrationinformation | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |
> | services | No | No |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | No | No |
> | domains/topics | No | No |
> | eventsubscriptions | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. |
> | extensiontopics | No | No |
> | locations | No | No |
> | locations/eventsubscriptions | No | No |
> | locations/operationresults | No | No |
> | locations/operationsstatus | No | No |
> | locations/topictypes | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | operationsstatus | No | No |
> | partnernamespaces | No | No |
> | partnernamespaces/eventchannels | No | No |
> | partnerregistrations | No | No |
> | partnertopics | No | No |
> | partnertopics/eventsubscriptions | No | No |
> | systemtopics | No | No |
> | systemtopics/eventsubscriptions | No | No |
> | topics | No | No |
> | topictypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | No | No |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | clusters | No | No |
> | locations | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | spazi dei nomi | No | No |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/disasterrecoveryconfigs/checknameavailability | No | No |
> | namespaces/eventhubs | No | No |
> | namespaces/eventhubs/authorizationrules | No | No |
> | namespaces/eventhubs/consumergroups | No | No |
> | namespaces/networkrulesets | No | No |
> | Operazioni | No | No |
> | sku | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | No | No |
> | locations | No | No |
> | locations/operations | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | No | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | No | No |
> | funzionalità | No | No |
> | Operazioni | No | No |
> | provider | No | No |
> | subscriptionfeatureregistrations | No | No |

## <a name="microsoftgenomics"></a>Microsoft. GENOMICA

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | No | No |
> | automanagedvmconfigurationprofiles | No | No |
> | guestconfigurationassignments | No | No |
> | Operazioni | No | No |
> | software | No | No |
> | softwareupdateprofile | No | No |
> | softwareupdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | locations | No | No |
> | locations/operations | No | No |
> | locations/operationsstatus | No | No |
> | Operazioni | No | No |
> | sapmonitors | No | No |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | No | No |
> | locations | No | No |
> | Operazioni | No | No |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |
> | clusters/applications | No | No |
> | clusters/operationresults | No | No |
> | locations | No | No |
> | locations/azureasyncoperations | No | No |
> | locations/billingspecs | No | No |
> | locations/capabilities | No | No |
> | locations/operationresults | No | No |
> | locations/usages | No | No |
> | locations/validatecreaterequest | No | No |
> | Operazioni | No | No |

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |
> | services | No | No |
> | services/privateendpointconnections | No | No |
> | services/privatelinkresources | No | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatus | No | No |
> | machines | No | No |
> | machines/extensions | Sì | Sì |
> | Operazioni | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | No | No |
> | Operazioni | No | No |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | vnfs | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | No | No |
> | locations | No | No |
> | networkscopes | No | No |
> | Operazioni | No | No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sì | Sì |
> | locations | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | No | No |
> | activitylogalerts | No | No |
> | alertrules | Sì | Sì |
> | autoscalesettings | Sì | Sì |
> | baseline | No | No |
> | calculatebaseline | No | No |
> | components | Sì | Sì |
> | components/events | No | No |
> | components/linkedstorageaccounts | No | No |
> | componenti/metadati | No | No |
> | components/metrics | No | No |
> | components/pricingplans | No | No |
> | components/query | No | No |
> | datacollectionrules | No | No |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | eventcategories | No | No |
> | eventtypes | No | No |
> | extendeddiagnosticsettings | No | No |
> | guestdiagnosticsettings | No | No |
> | listmigrationdate | No | No |
> | locations | No | No |
> | locations/operationresults | No | No |
> | logdefinitions | No | No |
> | logprofiles | No | No |
> | log | No | No |
> | metricalerts | No | No |
> | metricbaselines | No | No |
> | metricbatch | No | No |
> | metricdefinitions | No | No |
> | metricnamespaces | No | No |
> | Metriche | No | No |
> | migratealertrules | No | No |
> | migratetonewpricingmodel | No | No |
> | myworkbooks | No | No |
> | notificationgroups | No | No |
> | Operazioni | No | No |
> | privatelinkscopeoperationstatuses | No | No |
> | privatelinkscopes | No | No |
> | privatelinkscopes/privateendpointconnectionproxies | No | No |
> | privatelinkscopes/privateendpointconnections | No | No |
> | privatelinkscopes/scopedresources | No | No |
> | rollbacktolegacypricingmodel | No | No |
> | scheduledqueryrules | Sì | Sì |
> | Topologia | No | No |
> | transazioni | No | No |
> | vminsightsonboardingstatuses | No | No |
> | webtests | Sì | Sì |
> | test Web/gettestresultfile | No | No |
> | workbooks | No | No |
> | workbooktemplates | No | No |

> [!IMPORTANT]
> Verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | No | No |
> | checknameavailability | No | No |
> | checksubdomainavailability | No | No |
> | iotapps | No | No |
> | Operazioni | No | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | graph | No | No |
> | Operazioni | No | No |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | deletedvaults | No | No |
> | hsmpools | Sì | Sì |
> | locations | No | No |
> | locations/deletedvaults | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
> | managedhsms | No | No |
> | Operazioni | No | No |
> | insiemi di credenziali | Sì | Sì |
> | vaults/accesspolicies | No | No |
> | vaults/eventgridfilters | No | No |
> | vaults/secrets | No | No |

> [!IMPORTANT]
> Gli insiemi di credenziali delle chiavi usati per la crittografia dei dischi non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o in sottoscrizioni diverse.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | registeredsubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | clusters/attacheddatabaseconfigurations | No | No |
> | clusters/databases | No | No |
> | clusters/databases/dataconnections | No | No |
> | clusters/databases/eventhubconnections | No | No |
> | clusters/databases/principalassignments | No | No |
> | clusters/principalassignments | No | No |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |
> | locations | No | No |
> | locations/operations | No | No |
> | Operazioni | No | No |
> | users | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LOCATIONBASEDSERVICES

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlocationservices"></a>Microsoft. LOCATIONSERVICES

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | Sì | Sì |
> | integrationserviceenvironments | No | No |
> | integrationserviceenvironments/managedapis | No | No |
> | isolatedenvironments | No | No |
> | locations | No | No |
> | locations/workflows | No | No |
> | Operazioni | No | No |
> | flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | No | No |
> | locations | No | No |
> | locations/operations | No | No |
> | locations/operationsstatus | No | No |
> | Operazioni | No | No |
> | webservices | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MACHINELEARNINGCOMPUTE

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MACHINELEARNINGEXPERIMENTATION

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | accounts/workspaces | No | No |
> | accounts/workspaces/projects | No | No |
> | teamaccounts | No | No |
> | teamaccounts/workspaces | No | No |
> | teamaccounts/workspaces/projects | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MACHINELEARNINGMODELMANAGEMENT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/computeoperationsstatus | No | No |
> | locations/quotas | No | No |
> | locations/updatequotas | No | No |
> | locations/usages | No | No |
> | locations/vmsizes | No | No |
> | locations/workspaceoperationsstatus | No | No |
> | Operazioni | No | No |
> | aree di lavoro | No | No |
> | workspaces/computes | No | No |
> | workspaces/eventgridfilters | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applyupdates | No | No |
> | configurationassignments | No | No |
> | maintenanceconfigurations | No | No |
> | updates | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identità | No | No |
> | Operazioni | No | No |
> | userassignedidentities | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft. MANAGEDNETWORK

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | No | No |
> | managednetworks/managednetworkgroups | No | No |
> | managednetworks/managednetworkpeeringpolicies | No | No |
> | notifica | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | No | No |
> | Operazioni | No | No |
> | operationstatuses | No | No |
> | registrationassignments | No | No |
> | registrationdefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | getentities | No | No |
> | managementgroups | No | No |
> | managementgroups/settings | No | No |
> | operationresults | No | No |
> | operationresults/asyncoperation | No | No |
> | Operazioni | No | No |
> | resources | No | No |
> | starttenantbackfill | No | No |
> | tenantbackfillstatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | accounts/eventgridfilters | No | No |
> | accounts/privateatlases | No | No |
> | Operazioni | No | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | No | No |
> | offers | No | No |
> | offertypes | No | No |
> | offertypes/publishers | No | No |
> | offertypes/publishers/offers | No | No |
> | offertypes/publishers/offers/plans | No | No |
> | offertypes/publishers/offers/plans/agreements | No | No |
> | offertypes/publishers/offers/plans/configs | No | No |
> | offertypes/publishers/offers/plans/configs/importimage | No | No |
> | Operazioni | No | No |
> | privategalleryitems | No | No |
> | privatestoreclient | No | No |
> | privatestores | No | No |
> | privatestores/offers | No | No |
> | products | No | No |
> | publishers | No | No |
> | publishers/offers | No | No |
> | publishers/offers/amendments | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |
> | listcommunicationpreference | No | No |
> | Operazioni | No | No |
> | updatecommunicationpreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agreements | No | No |
> | offertypes | No | No |
> | Operazioni | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | mediaservices | No | No |
> | mediaservices/accountfilters | No | No |
> | mediaservices/assets | No | No |
> | mediaservices/assets/assetfilters | No | No |
> | mediaservices/contentkeypolicies | No | No |
> | mediaservices/eventgridfilters | No | No |
> | mediaservices/liveeventoperations | No | No |
> | mediaservices/liveevents | No | No |
> | mediaservices/liveevents/liveoutputs | No | No |
> | mediaservices/liveoutputoperations | No | No |
> | mediaservices/streamingendpointoperations | No | No |
> | mediaservices/streamingendpoints | No | No |
> | mediaservices/streaminglocators | No | No |
> | mediaservices/streamingpolicies | No | No |
> | mediaservices/transforms | No | No |
> | mediaservices/transforms/jobs | No | No |
> | Operazioni | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. MICROSERVICES4SPRING

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | No | No |
> | locations | No | No |
> | locations/assessmentoptions | No | No |
> | locations/checknameavailability | No | No |
> | migrateprojects | No | No |
> | movecollections | No | No |
> | Operazioni | No | No |
> | projects | No | No |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | No |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | objectunderstandingaccounts | No | No |
> | Operazioni | No | No |
> | remoterenderingaccounts | No | No |
> | spatialanchorsaccounts | No | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts/backuppolicies | No | No |
> | netappaccounts/capacitypools | No | No |
> | netappaccounts/capacitypools/volumes | No | No |
> | netappaccounts/capacitypools/volumes/mounttargets | No | No |
> | netappaccounts/capacitypools/volumes/snapshots | No | No |
> | Operazioni | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | No | No |
> | applicationgatewayavailableresponseheaders | No | No |
> | applicationgatewayavailableservervariables | No | No |
> | applicationgatewayavailablessloptions | No | No |
> | applicationgatewayavailablewafrulesets | No | No |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | No | No |
> | azurefirewallfqdntags | No | No |
> | azurefirewalls | No | No |
> | bastionhosts | No | No |
> | bgpservicecommunities | No | No |
> | checkfrontdoornameavailability | No | No |
> | checktrafficmanagernameavailability | No | No |
> | connections | No | No |
> | ddoscustompolicies | No | No |
> | ddosprotectionplans | No | No |
> | dnsoperationresults | No | No |
> | dnsoperationstatuses | No | No |
> | dnszones | No | No |
> | dnszones/a | No | No |
> | dnszones/aaaa | No | No |
> | dnszones/all | No | No |
> | dnszones/caa | No | No |
> | dnszones/cname | No | No |
> | dnszones/mx | No | No |
> | dnszones/ns | No | No |
> | dnszones/ptr | No | No |
> | dnszones/recordsets | No | No |
> | dnszones/soa | No | No |
> | dnszones/srv | No | No |
> | dnszones/txt | No | No |
> | expressroutecircuits | No | No |
> | expressroutegateways | No | No |
> | expressrouteserviceproviders | No | No |
> | firewallpolicies | No | No |
> | frontdooroperationresults | No | No |
> | frontdoors | No | No |
> | frontdoors / frontendendpoints | No | No |
> | frontdoorwebapplicationfirewallmanagedrulesets | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | getdnsresourcereference | No | No |
> | internalnotify | No | No |
> | ipallocations | No | No |
> | ipgroups | No | No |
> | loadbalancers | Sì - SKU di base<br>No - SKU standard | Sì - SKU di base<br>No - SKU standard |
> | localnetworkgateways | No | No |
> | locations | No | No |
> | locations/autoapprovedprivatelinkservices | No | No |
> | locations/availabledelegations | No | No |
> | locations/availableprivateendpointtypes | No | No |
> | locations/availableservicealiases | No | No |
> | locations/baremetaltenants | No | No |
> | locations/batchnotifyprivateendpointsforresourcemove | No | No |
> | locations/batchvalidateprivateendpointsforresourcemove | No | No |
> | locations/checkacceleratednetworkingsupport | No | No |
> | locations/checkdnsnameavailability | No | No |
> | locations/checkprivatelinkservicevisibility | No | No |
> | locations/commitinternalazurenetworkmanagerconfiguration | No | No |
> | locations/effectiveresourceownership | No | No |
> | locations/nfvoperationresults | No | No |
> | locations/nfvoperations | No | No |
> | locations/operationresults | No | No |
> | locations/operations | No | No |
> | locations/servicetags | No | No |
> | locations/setresourceownership | No | No |
> | locations/supportedvirtualmachinesizes | No | No |
> | locations/usages | No | No |
> | locations/validateresourceownership | No | No |
> | locations/virtualnetworkavailableendpointservices | No | No |
> | natgateways | No | No |
> | networkexperimentprofiles | No | No |
> | networkintentpolicies | No | No |
> | networkinterfaces | No | No |
> | networkprofiles | No | No |
> | networksecuritygroups | No | No |
> | networkwatchers | Sì | No |
> | networkwatchers/connectionmonitors | Sì | No |
> | networkwatchers/flowlogs | Sì | No |
> | networkwatchers/pingmeshes | Sì | No |
> | Operazioni | No | No |
> | p2svpngateways | No | No |
> | privatednsoperationresults | No | No |
> | privatednsoperationstatuses | No | No |
> | privatednszones | No | No |
> | privatednszones/a | No | No |
> | privatednszones/aaaa | No | No |
> | privatednszones/all | No | No |
> | privatednszones/cname | No | No |
> | privatednszones/mx | No | No |
> | privatednszones/ptr | No | No |
> | privatednszones/soa | No | No |
> | privatednszones/srv | No | No |
> | privatednszones/txt | No | No |
> | privatednszones/virtualnetworklinks | No | No |
> | privatednszonesinternal | No | No |
> | privateendpointredirectmaps | No | No |
> | privateendpoints | No | No |
> | privatelinkservices | No | No |
> | publicipaddresses | Sì - SKU di base<br>No - SKU standard | Sì - SKU di base<br>No - SKU standard |
> | publicipprefixes | No | No |
> | routefilters | No | No |
> | routetables | No | No |
> | securitypartnerproviders | No | No |
> | serviceendpointpolicies | No | No |
> | trafficmanagergeographichierarchies | No | No |
> | trafficmanagerprofiles | No | No |
> | trafficmanagerprofiles/heatmaps | No | No |
> | trafficmanagerusermetricskeys | No | No |
> | virtualhubs | No | No |
> | virtualnetworkgateways | No | No |
> | virtualnetworks | No | No |
> | virtualnetworktaps | No | No |
> | virtualrouters | No | No |
> | virtualwans | No | No |
> | vpngateways (rete WAN virtuale) | No | No |
> | vpnserverconfigurations | No | No |
> | vpnsites (rete WAN virtuale) | No | No |

> [!IMPORTANT]
> Vedere [Linee guida di spostamento della rete](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | spazi dei nomi | No | No |
> | namespaces/notificationhubs | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | No | No |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | No | No |
> | importsites | No | No |
> | Operazioni | No | No |
> | serversites | No | No |
> | vmwaresites | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |
> | deletedworkspaces | No | No |
> | linktargets | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | storageinsightconfigs | No | No |
> | aree di lavoro | Sì | Sì |
> | workspaces/datasources | No | No |
> | workspaces/linkedservices | No | No |
> | workspaces/linkedstorageaccounts | No | No |
> | workspaces/metadata | No | No |
> | workspaces/query | No | No |
> | workspaces/scopedprivatelinkproxies | No | No |

> [!IMPORTANT]
> Verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Non è possibile spostare le aree di lavoro con un account di automazione collegato. Prima di iniziare un'operazione di spostamento, assicurarsi di scollegare tutti gli account di automazione.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | No | No |
> | managementconfigurations | No | No |
> | Operazioni | No | No |
> | solutions | No | No |
> | Viste | No | No |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | No | No |
> | legacypeerings | No | No |
> | Operazioni | No | No |
> | peerasns | No | No |
> | peeringlocations | No | No |
> | peerings | No | No |
> | peeringservicecountries | No | No |
> | peeringservicelocations | No | No |
> | peeringserviceproviders | No | No |
> | peeringservices | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | No | No |
> | Operazioni | No | No |
> | policyevents | No | No |
> | policystates | No | No |
> | policytrackedresources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | No | No |
> | dashboards | No | No |
> | locations | No | No |
> | locations/consoles | No | No |
> | locations/usersettings | No | No |
> | Operazioni | No | No |
> | usersettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | Operazioni | No | No |
> | privatelinkservicesforpowerbi | No | No |
> | privatelinkservicesforpowerbi/OperationResult | No | No |
> | tenants | No | No |
> | workspacecollections | No | No |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sì | Sì |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | checknameavailability | No | No |
> | Operazioni | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | No | No |
> | providerregistrations | No | No |
> | providerregistrations / defaultrollouts | No | No |
> | providerregistrations/resourcetyperegistrations | No | No |
> | rollouts | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | No | No |
> | locations | No | No |
> | locations/allocatedstamp | No | No |
> | locations/allocatestamp | No | No |
> | locations/backupaadproperties | No | No |
> | locations/backupcrossregionrestore | No | No |
> | locations/backupcrrjob | No | No |
> | locations/backupcrrjobs | No | No |
> | locations/backupcrroperationresults | No | No |
> | locations/backupcrroperationsstatus | No | No |
> | locations/backupprevalidateprotection | No | No |
> | locations/backupstatus | No | No |
> | locations/backupvalidatefeatures | No | No |
> | locations/checknameavailability | No | No |
> | Operazioni | No | No |
> | replicationeligibilityresults | No | No |
> | insiemi di credenziali | Sì | Sì |

> [!IMPORTANT]
> Vedere [Linee guida di spostamento di Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationresults | No | No |
> | locations/operationsstatus | No | No |
> | openshiftclusters | No | No |
> | Operazioni | No | No |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | spazi dei nomi | No | No |
> | namespaces/authorizationrules | No | No |
> | namespaces/hybridconnections | No | No |
> | namespaces/hybridconnections/authorizationrules | No | No |
> | namespaces/privateendpointconnections | No | No |
> | namespaces/wcfrelays | No | No |
> | namespaces/wcfrelays/authorizationrules | No | No |
> | Operazioni | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Operazioni | No | No |
> | query | Sì | Sì |
> | resourcechangedetails | No | No |
> | resourcechanges | No | No |
> | resources | No | No |
> | resourceshistory | No | No |
> | subscriptionsstatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | No | No |
> | childavailabilitystatuses | No | No |
> | childresources | No | No |
> | emergingissues | No | No |
> | eventi | No | No |
> | metadata | No | No |
> | Notifiche | No | No |
> | Operazioni | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | No | No |
> | checkpolicycompliance | No | No |
> | checkresourcename | No | No |
> | deployments | No | No |
> | deployments/operations | No | No |
> | deploymentscripts | No | No |
> | deploymentscripts/logs | No | No |
> | collegamenti | No | No |
> | locations | No | No |
> | locations/deploymentscriptoperationresults | No | No |
> | notifyresourcejobs | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | provider | No | No |
> | resourcegroups | No | No |
> | resources | No | No |
> | subscriptions | No | No |
> | subscriptions/locations | No | No |
> | subscriptions/operationresults | No | No |
> | subscriptions/providers | No | No |
> | subscriptions/resourcegroups | No | No |
> | subscriptions/resourcegroups/resources | No | No |
> | subscriptions/resources | No | No |
> | subscriptions/tagnames | No | No |
> | subscriptions/tagnames/tagvalues | No | No |
> | tags | No | No |
> | templatespecs | No | No |
> | templatespecs/versions | No | No |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |
> | checkmoderneligibility | No | No |
> | checknameavailability | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checkservicenameavailability | No | No |
> | Operazioni | No | No |
> | resourcehealthmetadata | No | No |
> | searchservices | Sì | Sì |

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | No | No |
> | advancedthreatprotectionsettings | No | No |
> | alerts | No | No |
> | alertssuppressionrules | No | No |
> | allowedconnections | No | No |
> | applicationwhitelistings | No | No |
> | assessmentmetadata | No | No |
> | assessments | No | No |
> | autodismissalertsrules | No | No |
> | automations | No | No |
> | autoprovisioningsettings | No | No |
> | complianceresults | No | No |
> | compliances | No | No |
> | datacollectionagents | No | No |
> | devicesecuritygroups | No | No |
> | discoveredsecuritysolutions | No | No |
> | externalsecuritysolutions | No | No |
> | informationprotectionpolicies | No | No |
> | iotsecuritysolutions | No | No |
> | iotsecuritysolutions/analyticsmodels | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | No | No |
> | iotsecuritysolutions / iotalerts | No | No |
> | iotsecuritysolutions / iotalerttypes | No | No |
> | jitnetworkaccesspolicies | No | No |
> | locations | No | No |
> | locations/alerts | No | No |
> | locations/allowedconnections | No | No |
> | locations/applicationwhitelistings | No | No |
> | locations/discoveredsecuritysolutions | No | No |
> | locations/externalsecuritysolutions | No | No |
> | locations/jitnetworkaccesspolicies | No | No |
> | locations/securitysolutions | No | No |
> | locations/securitysolutionsreferencedata | No | No |
> | locations/tasks | No | No |
> | locations/topologies | No | No |
> | Operazioni | No | No |
> | criteri | No | No |
> | pricings | No | No |
> | regulatorycompliancestandards | No | No |
> | regulatorycompliancestandards/regulatorycompliancecontrols | No | No |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | No | No |
> | securescorecontroldefinitions | No | No |
> | securescorecontrols | No | No |
> | securescores | No | No |
> | securescores / securescorecontrols | No | No |
> | securitycontacts | No | No |
> | securitysolutions | No | No |
> | securitysolutionsreferencedata | No | No |
> | securitystatuses | No | No |
> | securitystatusessummaries | No | No |
> | servervulnerabilityassessments | No | No |
> | Scheda Impostazioni | No | No |
> | subassessments | No | No |
> | attività | No | No |
> | topologies | No | No |
> | workspacesettings | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | No | No |
> | alertrules | No | No |
> | alertruletemplates | No | No |
> | automationrules | No | No |
> | bookmarks | No | No |
> | cases | No | No |
> | dataconnectors | No | No |
> | dataconnectorscheckrequirements | No | No |
> | entities | No | No |
> | entityqueries | No | No |
> | incidents | No | No |
> | officeconsents | No | No |
> | Operazioni | No | No |
> | Scheda Impostazioni | No | No |
> | threatintelligence | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | No | No |
> | locations | No | No |
> | locations/consoleservices | No | No |
> | Operazioni | No | No |

## <a name="microsoftservermanagement"></a>Microsoft. SERVERMANAGEMENT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | No | No |
> | nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | locations | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | spazi dei nomi | No | No |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/disasterrecoveryconfigs/checknameavailability | No | No |
> | namespaces/eventgridfilters | No | No |
> | namespaces/networkrulesets | No | No |
> | namespaces/queues | No | No |
> | namespaces/queues/authorizationrules | No | No |
> | namespaces/topics | No | No |
> | namespaces/topics/authorizationrules | No | No |
> | namespaces/topics/subscriptions | No | No |
> | namespaces/topics/subscriptions/rules | No | No |
> | Operazioni | No | No |
> | premiummessagingregions | No | No |
> | sku | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |
> | clusters | No | No |
> | clusters/applications | No | No |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | locations | No | No |
> | locations/clusterversions | No | No |
> | locations/environments | No | No |
> | locations/operationresults | No | No |
> | locations/operations | No | No |
> | managedclusters | No | No |
> | networks | No | No |
> | Operazioni | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |
> | containergroups | No | No |
> | gateways | No | No |
> | locations | No | No |
> | locations/applicationoperations | No | No |
> | locations/gatewayoperations | No | No |
> | locations/networkoperations | No | No |
> | locations/secretoperations | No | No |
> | locations/volumeoperations | No | No |
> | networks | No | No |
> | Operazioni | No | No |
> | chiavi private | No | No |
> | volumes | No | No |

## <a name="microsoftservices"></a>Microsoft. Servizi

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operationstatuses | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |
> | signalr | Sì | Sì |
> | signalr/eventgridfilters | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | No | No |
> | Operazioni | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | No | No |
> | scala Web | No | No |
> | jitrequests | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | instancepools | No | No |
> | locations | Sì | Sì |
> | locations/administratorazureasyncoperation | No | No |
> | locations/administratoroperationresults | No | No |
> | locations/auditingsettingsazureasyncoperation | No | No |
> | locations/auditingsettingsoperationresults | No | No |
> | locations/capabilities | No | No |
> | locations/databaseazureasyncoperation | No | No |
> | locations/databaseoperationresults | No | No |
> | locations/databaserestoreazureasyncoperation | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/deletevirtualnetworkorsubnetsazureasyncoperation | No | No |
> | locations/deletevirtualnetworkorsubnetsoperationresults | No | No |
> | locations/dnsaliasasyncoperation | No | No |
> | locations/dnsaliasoperationresults | No | No |
> | locations/elasticpoolazureasyncoperation | No | No |
> | locations/elasticpooloperationresults | No | No |
> | locations/encryptionprotectorazureasyncoperation | No | No |
> | locations/encryptionprotectoroperationresults | No | No |
> | locations/extendedauditingsettingsazureasyncoperation | No | No |
> | locations/extendedauditingsettingsoperationresults | No | No |
> | locations/failovergroupazureasyncoperation | No | No |
> | locations/failovergroupoperationresults | No | No |
> | locations/firewallrulesazureasyncoperation | No | No |
> | locations/firewallrulesoperationresults | No | No |
> | locations/instancefailovergroupazureasyncoperation | No | No |
> | locations/instancefailovergroupoperationresults | No | No |
> | locations/instancefailovergroups | No | No |
> | locations/instancepoolazureasyncoperation | No | No |
> | locations/instancepooloperationresults | No | No |
> | locations/jobagentazureasyncoperation | No | No |
> | locations/jobagentoperationresults | No | No |
> | locations/longtermretentionbackupazureasyncoperation | No | No |
> | locations/longtermretentionbackupoperationresults | No | No |
> | locations/longtermretentionbackups | No | No |
> | locations/longtermretentionmanagedinstancebackupazureasyncoperation | No | No |
> | locations/longtermretentionmanagedinstancebackupoperationresults | No | No |
> | locations/longtermretentionmanagedinstancebackups | No | No |
> | locations/longtermretentionmanagedinstances | No | No |
> | locations/longtermretentionpolicyazureasyncoperation | No | No |
> | locations/longtermretentionpolicyoperationresults | No | No |
> | locations/longtermretentionservers | No | No |
> | locations/manageddatabaseazureasyncoperation | No | No |
> | locations/manageddatabasecompleterestoreazureasyncoperation | No | No |
> | locations/manageddatabasecompleterestoreoperationresults | No | No |
> | locations/manageddatabaseoperationresults | No | No |
> | locations/manageddatabaserestoreazureasyncoperation | No | No |
> | locations/manageddatabaserestoreoperationresults | No | No |
> | locations/managedinstanceazureasyncoperation | No | No |
> | locations/managedinstanceencryptionprotectorazureasyncoperation | No | No |
> | locations/managedinstanceencryptionprotectoroperationresults | No | No |
> | locations/managedinstancekeyazureasyncoperation | No | No |
> | locations/managedinstancekeyoperationresults | No | No |
> | locations/managedinstancelongtermretentionpolicyazureasyncoperation | No | No |
> | locations/managedinstancelongtermretentionpolicyoperationresults | No | No |
> | locations/managedinstanceoperationresults | No | No |
> | locations/managedinstancetdecertazureasyncoperation | No | No |
> | locations/managedinstancetdecertoperationresults | No | No |
> | locations/managedserversecurityalertpoliciesazureasyncoperation | No | No |
> | locations/managedserversecurityalertpoliciesoperationresults | No | No |
> | locations/managedshorttermretentionpolicyazureasyncoperation | No | No |
> | locations/managedshorttermretentionpolicyoperationresults | No | No |
> | locations/notifyazureasyncoperation | No | No |
> | locations/privateendpointconnectionazureasyncoperation | No | No |
> | locations/privateendpointconnectionoperationresults | No | No |
> | locations/privateendpointconnectionproxyazureasyncoperation | No | No |
> | locations/privateendpointconnectionproxyoperationresults | No | No |
> | locations/securityalertpoliciesazureasyncoperation | No | No |
> | locations/securityalertpoliciesoperationresults | No | No |
> | locations/serveradministratorazureasyncoperation | No | No |
> | locations/serveradministratoroperationresults | No | No |
> | locations/serverazureasyncoperation | No | No |
> | locations/serverkeyazureasyncoperation | No | No |
> | locations/serverkeyoperationresults | No | No |
> | locations/serveroperationresults | No | No |
> | locations/shorttermretentionpolicyazureasyncoperation | No | No |
> | locations/shorttermretentionpolicyoperationresults | No | No |
> | locations/syncagentoperationresults | No | No |
> | locations/syncdatabaseids | No | No |
> | locations/syncgroupoperationresults | No | No |
> | locations/syncmemberoperationresults | No | No |
> | locations/tdecertazureasyncoperation | No | No |
> | locations/tdecertoperationresults | No | No |
> | locations/usages | No | No |
> | locations/virtualclusterazureasyncoperation | No | No |
> | locations/virtualclusteroperationresults | No | No |
> | locations/virtualnetworkrulesazureasyncoperation | No | No |
> | locations/virtualnetworkrulesoperationresults | No | No |
> | locations/vulnerabilityassessmentscanazureasyncoperation | No | No |
> | locations/vulnerabilityassessmentscanoperationresults | No | No |
> | managedinstances | No | No |
> | managedinstances/administrators | No | No |
> | managedinstances/databases | No | No |
> | managedinstances/databases/backuplongtermretentionpolicies | No | No |
> | managedinstances/databases/vulnerabilityassessments | No | No |
> | managedinstances/metricdefinitions | No | No |
> | managedinstances/metrics | No | No |
> | managedinstances/recoverabledatabases | No | No |
> | managedinstances/tdecertificates | No | No |
> | managedinstances/vulnerabilityassessments | No | No |
> | Operazioni | No | No |
> | servers | No | No |
> | servers/administratoroperationresults | No | No |
> | servers/administrators | No | No |
> | servers/advisors | No | No |
> | servers/aggregateddatabasemetrics | No | No |
> | servers/auditingsettings | No | No |
> | servers/automatictuning | No | No |
> | servers/communicationlinks | No | No |
> | servers/databases | No | No |
> | servers/databases/advisors | No | No |
> | servers/databases/auditingsettings | No | No |
> | servers/databases/auditrecords | No | No |
> | servers/databases/automatictuning | No | No |
> | servers/databases/backuplongtermretentionpolicies | No | No |
> | servers/databases/backupshorttermretentionpolicies | No | No |
> | servers/databases/datamaskingpolicies | No | No |
> | servers/databases/datamaskingpolicies/rules | No | No |
> | servers/databases/extensions | No | No |
> | servers/databases/geobackuppolicies | No | No |
> | servers/databases/metricdefinitions | No | No |
> | servers/databases/metrics | No | No |
> | servers/databases/recommendedsensitivitylabels | No | No |
> | servers/databases/securityalertpolicies | No | No |
> | servers/databases/syncgroups | No | No |
> | servers/databases/syncgroups/syncmembers | No | No |
> | servers/databases/topqueries | No | No |
> | servers/databases/topqueries/querytext | No | No |
> | servers/databases/transparentdataencryption | No | No |
> | servers/databases/vulnerabilityassessment | No | No |
> | servers/databases/vulnerabilityassessments | No | No |
> | servers/databases/vulnerabilityassessmentscans | No | No |
> | servers/databases/vulnerabilityassessmentsettings | No | No |
> | servers/databases/workloadgroups | No | No |
> | servers/databasesecuritypolicies | No | No |
> | servers/disasterrecoveryconfiguration | No | No |
> | servers/dnsaliases | No | No |
> | servers/elasticpoolestimates | No | No |
> | servers/elasticpools | No | No |
> | servers/elasticpools/advisors | No | No |
> | servers/elasticpools/metricdefinitions | No | No |
> | servers/elasticpools/metrics | No | No |
> | servers/encryptionprotector | No | No |
> | servers/extendedauditingsettings | No | No |
> | servers/failovergroups | No | No |
> | servers/import | No | No |
> | servers/importexportoperationresults | No | No |
> | servers/jobaccounts | No | No |
> | servers/jobagents | No | No |
> | servers/jobagents/jobs | No | No |
> | servers/jobagents/jobs/executions | No | No |
> | servers/jobagents/jobs/steps | No | No |
> | servers/keys | No | No |
> | servers/operationresults | No | No |
> | servers/recommendedelasticpools | No | No |
> | servers/recoverabledatabases | No | No |
> | servers/restorabledroppeddatabases | No | No |
> | servers/securityalertpolicies | No | No |
> | servers/serviceobjectives | No | No |
> | servers/syncagents | No | No |
> | servers/tdecertificates | No | No |
> | servers/usages | No | No |
> | servers/virtualnetworkrules | No | No |
> | servers/vulnerabilityassessments | No | No |
> | virtualclusters | No | No |

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/availabilitygrouplisteneroperationresults | No | No |
> | locations/operationtypes | No | No |
> | locations/sqlvirtualmachinegroupoperationresults | No | No |
> | locations/sqlvirtualmachineoperationresults | No | No |
> | Operazioni | No | No |
> | sqlvirtualmachinegroups | Sì | Sì |
> | sqlvirtualmachinegroups/availabilitygrouplisteners | No | No |
> | sqlvirtualmachines | Sì | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | locations | No | No |
> | locations/asyncoperations | No | No |
> | locations/checknameavailability | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |
> | storageaccounts | Sì | Sì |
> | storageaccounts/blobservices | No | No |
> | storageaccounts/fileservices | No | No |
> | storageaccounts/listaccountsas | No | No |
> | storageaccounts/listservicesas | No | No |
> | storageaccounts/queueservices | No | No |
> | storageaccounts/services | No | No |
> | storageaccounts/services/metricdefinitions | No | No |
> | storageaccounts/tableservices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>Microsoft. STORAGECACHE

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | locations/operationresults | No | No |
> | locations/operations | No | No |
> | locations/workflows | No | No |
> | Operazioni | No | No |
> | storagesyncservices | No | No |
> | storagesyncservices/registeredservers | No | No |
> | storagesyncservices/syncgroups | No | No |
> | storagesyncservices/syncgroups/cloudendpoints | No | No |
> | storagesyncservices/syncgroups/serverendpoints | No | No |
> | storagesyncservices/workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. STORAGESYNCDEV

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. STORAGESYNCINT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | No | No |
> | Operazioni | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |
> | locations | No | No |
> | locations/quotas | No | No |
> | Operazioni | No | No |
> | streamingjobs | Sì | Sì |

> [!IMPORTANT]
> I processi di analisi di flusso non possono essere spostati durante l'esecuzione.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. STREAMANALYTICSEXPLORER

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | No | No |
> | environments/eventsources | No | No |
> | instances | No | No |
> | instances/environments | No | No |
> | instances/environments/eventsources | No | No |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cancel | No | No |
> | createsubscription | No | No |
> | abilitare | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | ridenominazione | No | No |
> | subscriptiondefinitions | No | No |
> | subscriptionoperations | No | No |
> | subscriptions | No | No |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | operationsstatus | No | No |
> | services | No | No |
> | services/problemclassifications | No | No |
> | supporttickets | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | Operazioni | No | No |
> | privatelinkhubs | No | No |
> | aree di lavoro | No | No |
> | workspaces/bigdatapools | No | No |
> | workspaces/operationresults | No | No |
> | workspaces/operationstatuses | No | No |
> | workspaces/sqlpools | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | No | No |
> | environments/accesspolicies | No | No |
> | environments/eventsources | No | No |
> | environments/referencedatasets | No | No |
> | Operazioni | No | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | No | No |
> | stores/accesspolicies | No | No |
> | stores/services | No | No |
> | stores/services/tokens | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |
> | imagetemplates / runoutputs | No | No |
> | locations | No | No |
> | locations/operations | No | No |
> | Operazioni | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | account/extension | No | No |
> | account/project | No | No |
> | checknameavailability | No | No |
> | Operazioni | No | No |

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [Modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | resourcepools | No | No |
> | vcenters | No | No |
> | virtualmachines | No | No |
> | virtualmachinetemplates | No | No |
> | virtualnetworks | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | No | No |
> | dedicatedcloudservices | No | No |
> | locations | No | No |
> | locations/availabilities | No | No |
> | locations/operationresults | No | No |
> | locations/privateclouds | No | No |
> | locations/privateclouds/resourcepools | No | No |
> | locations/privateclouds/virtualmachinetemplates | No | No |
> | locations/privateclouds/virtualnetworks | No | No |
> | locations/usages | No | No |
> | Operazioni | No | No |
> | virtualmachines | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | No | No |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | vnfs | No | No |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | Operazioni | No | No |
> | plans | No | No |
> | registeredsubscriptions | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | No | No |
> | billingmeters | No | No |
> | certificates | No | No |
> | checknameavailability | No | No |
> | connectiongateways | Sì | Sì |
> | connections | Sì | Sì |
> | customapis | Sì | Sì |
> | deletedsites | No | No |
> | deploymentlocations | No | No |
> | georegions | No | No |
> | hostingenvironments | No | No |
> | hostingenvironments/eventgridfilters | No | No |
> | hostingenvironments/multirolepools | No | No |
> | hostingenvironments/workerpools | No | No |
> | ishostingenvironmentnameavailable | No | No |
> | ishostnameavailable | No | No |
> | isusernameavailable | No | No |
> | kubeenvironments | No | No |
> | listsitesassignedtohostname | No | No |
> | locations | No | No |
> | locations/apioperations | No | No |
> | locations/connectiongatewayinstallations | No | No |
> | locations/deletedsites | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/extractapidefinitionfromwsdl | No | No |
> | locations/getnetworkpolicies | No | No |
> | locations/listwsdlinterfaces | No | No |
> | locations/managedapis | No | No |
> | locations/operationresults | No | No |
> | locations/operations | No | No |
> | locations/runtimes | No | No |
> | Operazioni | No | No |
> | publishingusers | No | No |
> | raccomandazioni di film | No | No |
> | resourcehealthmetadata | No | No |
> | runtimes | No | No |
> | serverfarms | Sì | Sì |
> | serverfarms/eventgridfilters | No | No |
> | siti | Sì | Sì |
> | sites/eventgridfilters | No | No |
> | sites/hostnamebindings | No | No |
> | sites/networkconfig | No | No |
> | sites/premieraddons | No | No |
> | sites/slots | No | No |
> | sites/slots/eventgridfilters | No | No |
> | sites/slots/hostnamebindings | No | No |
> | sites/slots/networkconfig | No | No |
> | sourcecontrols | No | No |
> | staticsites | No | No |
> | validate | No | No |
> | verifyhostingenvironmentvnet | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | multipleactivationkeys | No | No |
> | Operazioni | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |
> | Operazioni | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/operationstatuses | No | No |
> | Operazioni | No | No |
> | carichi di lavoro | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | No | No |
> | componentssummary | No | No |
> | monitorinstances | No | No |
> | monitorinstancessummary | No | No |
> | monitors | No | No |
> | notificationsettings | No | No |
> | Operazioni | No | No |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
