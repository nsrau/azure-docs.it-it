---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure| Microsoft Docs
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: dfed8ce93a97b31380b44f0ac7057c0cff60901e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497282"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse

Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Anche se un tipo di risorsa supporta l'operazione di spostamento, potrebbero esservi condizioni che impediscano alla risorsa di essere spostata. Per informazioni dettagliate sulle condizioni che influiscono sulle operazioni di spostamento, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="find-resource-provider-and-resource-type"></a>Trovare i provider di risorse e il tipo di risorsa

Per determinare se una risorsa può essere spostata, è necessario trovare il provider di risorse e il tipo di risorsa.

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

Il tipo di risorsa viene restituito nel formato `<resource-provider>/<resource-type-name>`. In questo modo, il valore `Microsoft.OperationalInsights/workspaces` dispone di un provider di risorse **Microsoft.OperationalInsights** e il nome del tipo di risorsa **workspaces**.

Dopo aver trovato il provider di risorse e il tipo di risorsa usare le tabelle in questo articolo per stabilire se il tipo di risorsa supporta l'operazione di spostamento.

## <a name="microsoftaad"></a>Microsoft.AAD

| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | --------------- | ----------- |
| domainservices | No  | No  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | --------------- | ----------- |
| service | Sì | Sì |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | --------------- | ----------- |
| policyassignments | No  | No  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| automationaccounts | Sì | Sì |
| automationaccounts/configurations | Sì | Sì |
| automationaccounts/runbooks | Sì | Sì |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| b2cdirectories | Sì | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| registrations | Sì | Sì |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| backupvault | No  | No  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| batchaccounts | Sì | Sì |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| mapapis | No  | No  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| biztalk | Sì | Sì |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| blueprintassignments | No  | No  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| botservices | Sì | Sì |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| redis | Sì | Sì |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| Profili | Sì | Sì |
| profiles/endpoints | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| certificateorders | Sì | Sì |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| domainnames | Sì | No  |
| virtualmachines | Sì | No  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| networksecuritygroups | No  | No  |
| reservedips | No  | No  |
| virtualnetworks | No  | No  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storageaccounts | Sì | No  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| availabilitysets | Sì | Sì |
| disks | Sì | Sì |
| galleries | No  | No  |
| galleries/images | No  | No  |
| galleries/images/versions | No  | No  |
| images | Sì | Sì |
| restorepointcollections | No  | No  |
| sharedvmimages | No  | No  |
| sharedvmimages/versions | No  | No  |
| snapshots | Sì | Sì |
| virtualmachines | Sì | Sì |
| virtualmachines/extensions | Sì | Sì |
| virtualmachinescalesets | Sì | Sì |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| containergroups | No  | No  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| containergroups | No  | No  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| registries | Sì | Sì |
| registries/buildtasks | Sì | Sì |
| registries/replications | No  | No  |
| registries/tasks | Sì | Sì |
| registries/webhooks | Sì | Sì |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| containerservices | No  | No  |
| managedclusters | No  | No  |
| openshiftmanagedclusters | No  | No  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Sì | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dell'account di integrazione | Sì | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| hubs | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| jobs | No  | No  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| databoxedgedevices | No  | No  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| aree di lavoro | No  | No  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| catalogs | Sì | Sì |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| datafactories | Sì | Sì |
| factories | Sì | Sì |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| datalakeaccounts | No  | No  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| services | No  | No  |
| services/projects | No  | No  |
| slot | No  | No  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| servers | No  | No  |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| servers | Sì | Sì |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| servergroups | No  | No  |
| servers | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| artifactsources | No  | No  |
| rollouts | No  | No  |
| servicetopologies | No  | No  |
| servicetopologies/services | No  | No  |
| servicetopologies/services/serviceunits | No  | No  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| iothubs | Sì | Sì |
| provisioningservices | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| labcenters | No  | No  |
| labs | Sì | No  |
| labs/servicerunners | Sì | Sì |
| labs/virtualmachines | Sì | No  |
| schedules | No  | No  |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dnszones | No  | No  |
| dnszones/a | No  | No  |
| dnszones/aaaa | No  | No  |
| dnszones/cname | No  | No  |
| dnszones/mx | No  | No  |
| dnszones/ptr | No  | No  |
| dnszones/srv | No  | No  |
| dnszones/txt | No  | No  |
| trafficmanagerprofiles | No  | No  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| domains | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| topics | Sì | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Sì | Sì |
| spazi dei nomi | Sì | Sì |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| hanainstances | Sì | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dedicatedhsms | No  | No  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| jobs | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| actiongroups | Sì | Sì |
| activitylogalerts | No  | No  |
| alertrules | Sì | Sì |
| autoscalesettings | Sì | Sì |
| components | Sì | Sì |
| metricalerts | No  | No  |
| scheduledqueryrules | Sì | Sì |
| webtests | Sì | Sì |
| workbooks | Sì | Sì |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| iotapps | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Sì | Sì |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| labaccounts | Sì | Sì |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| integrationaccounts | Sì | Sì |
| flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| commitmentplans | Sì | Sì |
| webservices | Sì | No  |
| aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Sì | Sì |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |
| accounts/workspaces | Sì | Sì |
| accounts/workspaces/projects | Sì | Sì |
| teamaccounts | Sì | Sì |
| teamaccounts/workspaces | Sì | Sì |
| teamaccounts/workspaces/projects | Sì | Sì |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| aree di lavoro | Sì | Sì |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| userassignedidentities | Sì | Sì |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| classicdevservices | No  | No  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| mediaservices | Sì | Sì |
| mediaservices/liveevents | Sì | Sì |
| mediaservices/streamingendpoints | Sì | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| projects | No  | No  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| applicationgateways | No  | No  |
| applicationsecuritygroups | Sì | Sì |
| azurefirewalls | No  | No  |
| connections | Sì | Sì |
| ddosprotectionplans | No  | No  |
| dnszones | Sì | Sì |
| expressroutecircuits | No  | No  |
| expressroutecrossconnections | No  | No  |
| expressroutegateways | No  | No  |
| expressrouteports | No  | No  |
| frontdoors | Sì | Sì |
| frontdoorwebapplicationfirewallpolicies | Sì | Sì |
| interfaceendpoints | No  | No  |
| loadbalancers | Sì | Sì |
| localnetworkgateways | Sì | Sì |
| networkintentpolicies | Sì | Sì |
| networkinterfaces | Sì | Sì |
| networkprofiles | No  | No  |
| networksecuritygroups | Sì | Sì |
| networkwatchers | Sì | Sì |
| networkwatchers/connectionmonitors | Sì | Sì |
| networkwatchers/lenses | Sì | Sì |
| networkwatchers/pingmeshes | Sì | Sì |
| publicipaddresses | Sì | Sì |
| publicipprefixes | Sì | Sì |
| routefilters | No  | No  |
| routetables | Sì | Sì |
| serviceendpointpolicies | Sì | Sì |
| trafficmanagerprofiles | Sì | Sì |
| virtualhubs | Sì | Sì |
| virtualnetworkgateways | Sì | Sì |
| virtualnetworks | Sì | Sì |
| virtualnetworktaps | No  | No  |
| virtualwans | Sì | Sì |
| vpngateways | Sì | Sì |
| vpnsites | Sì | Sì |
| webapplicationfirewallpolicies | Sì | Sì |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Sì | Sì |
| namespaces/notificationhubs | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| aree di lavoro | Sì | Sì |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| managementconfigurations | Sì | Sì |
| solutions | Sì | Sì |
| Viste | Sì | Sì |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dashboards | Sì | Sì |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| workspacecollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| capacities | Sì | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Sì | No  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| flows | Sì | Sì |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| searchservices | Sì | Sì |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Sì | Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Sì | Sì |
| networks | Sì | Sì |
| volumes | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| signalr | Sì | Sì |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| siterecoveryvault | No  | No  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| appliancedefinitions | No  | No  |
| appliances | No  | No  |
| applicationdefinitions | No  | No  |
| scala Web | No  | No  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| managedinstances | Sì | Sì |
| managedinstances/databases | Sì | Sì |
| servers | Sì | Sì |
| servers/databases | Sì | Sì |
| servers/elasticpools | Sì | Sì |
| virtualclusters | Sì | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storageaccounts | Sì | Sì |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storagesyncservices | Sì | Sì |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| managers | No  | No  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| streamingjobs | Sì | Sì |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| environments | Sì | Sì |
| environments/eventsources | Sì | Sì |
| environments/referencedatasets | Sì | Sì |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| certificates | No  | Sì |
| classicmobileservices | No  | No  |
| connectiongateways | Sì | Sì |
| connections | Sì | Sì |
| customapis | Sì | Sì |
| hostingenvironments | No  | No  |
| serverfarms | Sì | Sì |
| siti | Sì | Sì |
| sites/premieraddons | Sì | Sì |
| sites/slots | Sì | Sì |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| deviceservices | Sì | Sì |


## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento. Questi provider di risorse sono:

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Passaggi successivi

* Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
