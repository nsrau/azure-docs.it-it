---
title: Supporto per lo spostamento per tipo di risorsa
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780336"
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
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
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
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
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
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
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
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
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
> | actionrules | Sì | Sì |
> | alerts | No | No |
> | alertslist | No | No |
> | alertsmetadata | No | No |
> | alertssummary | No | No |
> | alertssummarylist | No | No |
> | Operazioni | No | No |
> | smartdetectoralertrules | Sì | Sì |
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
> | servers | Sì | Sì |

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
> | configurationstores | Sì | Sì |
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
> | spring | Sì | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService

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
> | attestationproviders | Sì | Sì |
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
> | autorizzazioni | No | No |
> | policyassignments | No | No |
> | policydefinitions | No | No |
> | policysetdefinitions | No | No |
> | provideroperations | No | No |
> | roleassignments | No | No |
> | roleassignmentsusagemetrics | No | No |
> | roledefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sì | Sì |
> | automationaccounts/configurations | Sì | Sì |
> | automationaccounts/jobs | No | No |
> | automationaccounts/privateendpointconnectionproxies | No | No |
> | automationaccounts/privateendpointconnections | No | No |
> | automationaccounts/privatelinkresources | No | No |
> | automationaccounts/runbooks | Sì | Sì |
> | automationaccounts/softwareupdateconfigurations | No | No |
> | automationaccounts/webhooks | No | No |
> | Operazioni | No | No |

> [!IMPORTANT]
> I runbook devono esistere nello stesso gruppo di risorse dell'account di automazione.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checkquotaavailability | No | No |
> | Operazioni | No | No |
> | privateclouds | Sì | Sì |
> | privateclouds/clusters | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sì | Sì |
> | b2ctenants | No | No |
> | checknameavailability | No | No |
> | Operazioni | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | No | No |
> | hybriddatamanagers | No | No |
> | Operazioni | No | No |
> | postgresinstances | No | No |
> | sqlinstances | No | No |
> | sqlmanagedinstances | No | No |
> | sqlserverinstances | No | No |
> | sqlserverregistrations | Sì | Sì |
> | sqlserverregistrations/sqlservers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | No | No |
> | Operazioni | No | No |
> | registrations | Sì | Sì |
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

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | fileservers | No | No |
> | jobs | No | No |
> | Operazioni | No | No |
> | aree di lavoro | No | No |
> | workspaces/clusters | No | No |
> | workspaces/experiments | No | No |
> | workspaces/experiments/jobs | No | No |
> | workspaces/fileservers | No | No |

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
> | billingaccounts/enrollmentaccounts | No | No |
> | billingaccounts/invoices | No | No |
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

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

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
> | botservices | Sì | Sì |
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
> | redis/privateendpointconnections | No | No |
> | redis/privatelinkresources | No | No |
> | redisenterprise | No | No |

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
> | cdnwebapplicationfirewallpolicies | Sì | Sì |
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
> | profiles/endpoints | Sì | Sì |
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
> | domainnames | Sì | No |
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
> | virtualmachines | Sì | No |
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
> | storageaccounts | Sì | No |
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

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |
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
> | virtualmachines | Sì | Sì |
> | virtualmachines/extensions | Sì | Sì |
> | virtualmachines/metricdefinitions | No | No |
> | virtualmachines/runcommands | No | No |
> | virtualmachinescalesets | Sì | Sì |
> | virtualmachinescalesets/extensions | No | No |
> | virtualmachinescalesets/networkinterfaces | No | No |
> | virtualmachinescalesets/publicipaddresses | No | No |
> | virtualmachinescalesets/virtualmachines | No | No |
> | virtualmachinescalesets/virtualmachines/networkinterfaces | No | No |

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento di macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

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

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

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
> | registries/agentpools | Sì | Sì |
> | registries/agentpools/listqueuestatus | No | No |
> | registries/builds | No | No |
> | registries/builds/cancel | No | No |
> | registries/builds/getloglink | No | No |
> | registries/buildtasks | Sì | Sì |
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
> | registries/replications | Sì | Sì |
> | registries/runs | No | No |
> | registries/runs/cancel | No | No |
> | registries/runs/listlogsasurl | No | No |
> | registries/schedulerun | No | No |
> | registries/scopemaps | No | No |
> | registries/taskruns | No | No |
> | registries/taskruns/listdetails | No | No |
> | registries/tasks | Sì | Sì |
> | registries/tasks/listdetails | No | No |
> | registries/tokens | No | No |
> | registries/updatepolicies | No | No |
> | registries/webhooks | Sì | Sì |
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

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

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
> | dell'account di integrazione | Sì | Sì |
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

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

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
> | resourceproviders | Sì | Sì |

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
> | databoxedgedevices | No | No |
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
> | catalogs | Sì | Sì |
> | checknameavailability | No | No |
> | datacatalogs | No | No |
> | locations | No | No |
> | locations/jobs | No | No |
> | locations/operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

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
> | datafactories | Sì | Sì |
> | datafactories/diagnosticsettings | No | No |
> | datafactories/metricdefinitions | No | No |
> | datafactoryschema | No | No |
> | factories | Sì | Sì |
> | factories/integrationruntimes | No | No |
> | locations | No | No |
> | locations/configurefactoryrepo | No | No |
> | locations/getfeaturevalue | No | No |
> | Operazioni | No | No |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |
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
> | account | Sì | Sì |
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
> | artifactsources | Sì | Sì |
> | operationresults | No | No |
> | Operazioni | No | No |
> | rollouts | Sì | Sì |
> | servicetopologies | Sì | Sì |
> | servicetopologies/services | Sì | Sì |
> | servicetopologies/services/serviceunits | Sì | Sì |
> | steps | Sì | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Sì | Sì |
> | applicationgroups/applications | No | No |
> | applicationgroups/desktops | No | No |
> | applicationgroups/startmenuitems | No | No |
> | hostpools | Sì | Sì |
> | hostpools/sessionhosts | No | No |
> | hostpools/sessionhosts/usersessions | No | No |
> | hostpools/usersessions | No | No |
> | Operazioni | No | No |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checkprovisioningservicenameavailability | No | No |
> | elasticpools | No | No |
> | elasticpools/iothubtenants | No | No |
> | iothubs | Sì | Sì |
> | iothubs/eventgridfilters | No | No |
> | operationresults | No | No |
> | Operazioni | No | No |
> | provisioningservices | Sì | Sì |
> | usages | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | Sì | Sì |
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
> | labs | Sì | No |
> | labs/environments | Sì | Sì |
> | labs/servicerunners | Sì | Sì |
> | labs/virtualmachines | Sì | No |
> | locations | No | No |
> | locations/operations | No | No |
> | Operazioni | No | No |
> | schedules | Sì | Sì |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | No | No |
> | locations | No | No |
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
> | services | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sì | Sì |
> | eventSubscriptions | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. |
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
> | partnernamespaces | Sì | Sì |
> | partnernamespaces/eventchannels | No | No |
> | partnerregistrations | No | No |
> | partnertopics | Sì | Sì |
> | partnertopics/eventsubscriptions | No | No |
> | systemtopics | Sì | Sì |
> | systemtopics/eventsubscriptions | No | No |
> | topics | Sì | Sì |
> | topictypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | No | No |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | clusters | Sì | Sì |
> | locations | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | spazi dei nomi | Sì | Sì |
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
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | No | No |
> | funzionalità | No | No |
> | Operazioni | No | No |
> | provider | No | No |
> | subscriptionfeatureregistrations | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

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
> | sapmonitors | Sì | Sì |

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
> | services | Sì | Sì |
> | services/privateendpointconnections | No | No |
> | services/privatelinkresources | No | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | Sì | Sì |
> | machines/extensions | No | No |
> | Operazioni | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sì | Sì |
> | Operazioni | No | No |

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
> | actiongroups | Sì | Sì |
> | activitylogalerts | No | No |
> | alertrules | Sì | Sì |
> | autoscalesettings | Sì | Sì |
> | baseline | No | No |
> | calculatebaseline | No | No |
> | components | Sì | Sì |
> | components/events | No | No |
> | components/linkedstorageaccounts | No | No |
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
> | workbooks | Sì | Sì |
> | workbooktemplates | Sì | Sì |

> [!IMPORTANT]
> Verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | No | No |
> | checknameavailability | No | No |
> | checksubdomainavailability | No | No |
> | iotapps | Sì | Sì |
> | Operazioni | No | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sì | Sì |
> | graph | Sì | Sì |
> | Operazioni | No | No |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | deletedvaults | No | No |
> | hsmpools | No | No |
> | locations | No | No |
> | locations/deletedvaults | No | No |
> | locations/deletevirtualnetworkorsubnets | No | No |
> | locations/operationresults | No | No |
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
> | connectedclusters | Sì | Sì |
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
> | clusters | Sì | Sì |
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

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

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
> | integrationserviceenvironments | Sì | No |
> | integrationserviceenvironments/managedapis | Sì | No |
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
> | webservices | Sì | No |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | accounts/workspaces | No | No |
> | accounts/workspaces/projects | No | No |
> | teamaccounts | No | No |
> | teamaccounts/workspaces | No | No |
> | teamaccounts/workspaces/projects | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | No | No |

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
> | maintenanceconfigurations | Sì | Sì |
> | updates | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identità | No | No |
> | Operazioni | No | No |
> | userassignedidentities | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

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
> | account | Sì | Sì |
> | accounts/eventgridfilters | No | No |
> | accounts/privateatlases | Sì | Sì |
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
> | mediaservices | Sì | Sì |
> | mediaservices/accountfilters | No | No |
> | mediaservices/assets | No | No |
> | mediaservices/assets/assetfilters | No | No |
> | mediaservices/contentkeypolicies | No | No |
> | mediaservices/eventgridfilters | No | No |
> | mediaservices/liveeventoperations | No | No |
> | mediaservices/liveevents | Sì | Sì |
> | mediaservices/liveevents/liveoutputs | No | No |
> | mediaservices/liveoutputoperations | No | No |
> | mediaservices/streamingendpointoperations | No | No |
> | mediaservices/streamingendpoints | Sì | Sì |
> | mediaservices/streaminglocators | No | No |
> | mediaservices/streamingpolicies | No | No |
> | mediaservices/transforms | No | No |
> | mediaservices/transforms/jobs | No | No |
> | Operazioni | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Sì | Sì |
> | locations | No | No |
> | locations/assessmentoptions | No | No |
> | locations/checknameavailability | No | No |
> | migrateprojects | Sì | Sì |
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
> | remoterenderingaccounts | Sì | Sì |
> | spatialanchorsaccounts | Sì | Sì |

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
> | applicationsecuritygroups | Sì | Sì |
> | azurefirewallfqdntags | No | No |
> | azurefirewalls | No | No |
> | bastionhosts | No | No |
> | bgpservicecommunities | No | No |
> | checkfrontdoornameavailability | No | No |
> | checktrafficmanagernameavailability | No | No |
> | connections | Sì | Sì |
> | ddoscustompolicies | Sì | Sì |
> | ddosprotectionplans | No | No |
> | dnsoperationresults | No | No |
> | dnsoperationstatuses | No | No |
> | dnszones | Sì | Sì |
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
> | firewallpolicies | Sì | Sì |
> | frontdooroperationresults | No | No |
> | frontdoors | No | No |
> | frontdoorwebapplicationfirewallmanagedrulesets | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | getdnsresourcereference | No | No |
> | internalnotify | No | No |
> | ipgroups | Sì | Sì |
> | loadbalancers | Sì - SKU di base<br>No - SKU standard | Sì - SKU di base<br>No - SKU standard |
> | localnetworkgateways | Sì | Sì |
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
> | natgateways | Sì | Sì |
> | networkexperimentprofiles | No | No |
> | networkintentpolicies | Sì | Sì |
> | networkinterfaces | Sì | Sì |
> | networkprofiles | No | No |
> | networksecuritygroups | Sì | Sì |
> | networkwatchers | Sì | No |
> | networkwatchers/connectionmonitors | Sì | No |
> | networkwatchers/flowlogs | Sì | No |
> | networkwatchers/pingmeshes | Sì | No |
> | Operazioni | No | No |
> | p2svpngateways | No | No |
> | privatednsoperationresults | No | No |
> | privatednsoperationstatuses | No | No |
> | privatednszones | Sì | Sì |
> | privatednszones/a | No | No |
> | privatednszones/aaaa | No | No |
> | privatednszones/all | No | No |
> | privatednszones/cname | No | No |
> | privatednszones/mx | No | No |
> | privatednszones/ptr | No | No |
> | privatednszones/soa | No | No |
> | privatednszones/srv | No | No |
> | privatednszones/txt | No | No |
> | privatednszones/virtualnetworklinks | Sì | Sì |
> | privatednszonesinternal | No | No |
> | privateendpointredirectmaps | No | No |
> | privateendpoints | Sì | Sì |
> | privatelinkservices | No | No |
> | publicipaddresses | Sì - SKU di base<br>No - SKU standard | Sì - SKU di base<br>No - SKU standard |
> | publicipprefixes | Sì | Sì |
> | routefilters | No | No |
> | routetables | Sì | Sì |
> | securitypartnerproviders | Sì | Sì |
> | serviceendpointpolicies | Sì | Sì |
> | trafficmanagergeographichierarchies | No | No |
> | trafficmanagerprofiles | Sì | Sì |
> | trafficmanagerprofiles/heatmaps | No | No |
> | trafficmanagerusermetricskeys | No | No |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Sì | Sì |
> | virtualnetworks | Sì | Sì |
> | virtualnetworktaps | No | No |
> | virtualrouters | Sì | Sì |
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
> | spazi dei nomi | Sì | Sì |
> | namespaces/notificationhubs | Sì | Sì |
> | operationresults | No | No |
> | Operazioni | No | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Sì | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | Sì | Sì |
> | importsites | Sì | Sì |
> | Operazioni | No | No |
> | serversites | Sì | Sì |
> | vmwaresites | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
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
> | managementconfigurations | Sì | Sì |
> | Operazioni | No | No |
> | solutions | Sì | Sì |
> | Viste | Sì | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | No | No |
> | legacypeerings | No | No |
> | Operazioni | No | No |
> | peerasns | No | No |
> | peeringlocations | No | No |
> | peerings | Sì | Sì |
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
> | dashboards | Sì | Sì |
> | locations | No | No |
> | locations/consoles | No | No |
> | locations/usersettings | No | No |
> | Operazioni | No | No |
> | usersettings | No | No |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | locations | No | No |
> | locations/checknameavailability | No | No |
> | workspacecollections | Sì | Sì |

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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | checknameavailability | No | No |
> | Operazioni | No | No |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | No | No |
> | providerregistrations | No | No |
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
> | spazi dei nomi | Sì | Sì |
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
> | scala Web | Sì | No |
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
> | allowedconnections | No | No |
> | applicationwhitelistings | No | No |
> | assessmentmetadata | No | No |
> | assessments | No | No |
> | autodismissalertsrules | No | No |
> | automations | Sì | Sì |
> | autoprovisioningsettings | No | No |
> | complianceresults | No | No |
> | compliances | No | No |
> | datacollectionagents | No | No |
> | devicesecuritygroups | No | No |
> | discoveredsecuritysolutions | No | No |
> | externalsecuritysolutions | No | No |
> | informationprotectionpolicies | No | No |
> | iotsecuritysolutions | Sì | Sì |
> | iotsecuritysolutions/analyticsmodels | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | No | No |
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

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

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
> | spazi dei nomi | Sì | Sì |
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
> | clusters | Sì | Sì |
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
> | scala Web | Sì | Sì |
> | containergroups | No | No |
> | gateways | Sì | Sì |
> | locations | No | No |
> | locations/applicationoperations | No | No |
> | locations/gatewayoperations | No | No |
> | locations/networkoperations | No | No |
> | locations/secretoperations | No | No |
> | locations/volumeoperations | No | No |
> | networks | Sì | Sì |
> | Operazioni | No | No |
> | chiavi private | Sì | Sì |
> | volumes | Sì | Sì |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | No | No |
> | providerregistrations/resourcetyperegistrations | No | No |
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
> | locations | No | No |
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
> | servers | Sì | Sì |
> | servers/administratoroperationresults | No | No |
> | servers/administrators | No | No |
> | servers/advisors | No | No |
> | servers/aggregateddatabasemetrics | No | No |
> | servers/auditingpolicies | No | No |
> | servers/auditingsettings | No | No |
> | servers/automatictuning | No | No |
> | servers/communicationlinks | No | No |
> | servers/connectionpolicies | No | No |
> | servers/databases | Sì | Sì |
> | servers/databases/advisors | No | No |
> | servers/databases/auditingpolicies | No | No |
> | servers/databases/auditingsettings | No | No |
> | servers/databases/auditrecords | No | No |
> | servers/databases/automatictuning | No | No |
> | servers/databases/backuplongtermretentionpolicies | No | No |
> | servers/databases/backupshorttermretentionpolicies | No | No |
> | servers/databases/connectionpolicies | No | No |
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
> | servers/elasticpools | Sì | Sì |
> | servers/elasticpools/advisors | No | No |
> | servers/elasticpools/metricdefinitions | No | No |
> | servers/elasticpools/metrics | No | No |
> | servers/encryptionprotector | No | No |
> | servers/extendedauditingsettings | No | No |
> | servers/failovergroups | No | No |
> | servers/import | No | No |
> | servers/importexportoperationresults | No | No |
> | servers/jobaccounts | Sì | Sì |
> | servers/jobagents | Sì | Sì |
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
> | virtualclusters | Sì | Sì |

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

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | No | No |

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

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

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
> | locations/workflows | No | No |
> | Operazioni | No | No |
> | storagesyncservices | Sì | Sì |
> | storagesyncservices/registeredservers | No | No |
> | storagesyncservices/syncgroups | No | No |
> | storagesyncservices/syncgroups/cloudendpoints | No | No |
> | storagesyncservices/syncgroups/serverendpoints | No | No |
> | storagesyncservices/workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

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
> | clusters | No | No |
> | locations | No | No |
> | locations/quotas | No | No |
> | Operazioni | No | No |
> | streamingjobs | Sì | Sì |

> [!IMPORTANT]
> I processi di analisi di flusso non possono essere spostati durante l'esecuzione.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

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
> | aree di lavoro | Sì | Sì |
> | workspaces/bigdatapools | Sì | Sì |
> | workspaces/operationresults | No | No |
> | workspaces/operationstatuses | No | No |
> | workspaces/sqlpools | Sì | Sì |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | No | No |
> | resources | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Sì | Sì |
> | environments/accesspolicies | No | No |
> | environments/eventsources | Sì | Sì |
> | environments/referencedatasets | Sì | Sì |
> | Operazioni | No | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Sì | Sì |
> | stores/accesspolicies | No | No |
> | stores/services | No | No |
> | stores/services/tokens | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |
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

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | No | No |
> | billingmeters | No | No |
> | certificates | No | Sì |
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
> | kubeenvironments | Sì | Sì |
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
> | sites/premieraddons | Sì | Sì |
> | sites/slots | Sì | Sì |
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
