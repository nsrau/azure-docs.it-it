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
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 253cfd7ddeb04a12a3609ab5e14a37365015a568
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790878"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse

Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Anche se un tipo di risorsa supporta l'operazione di spostamento, potrebbero esservi condizioni che impediscano alla risorsa di essere spostata. Per informazioni dettagliate sulle condizioni che influiscono sulle operazioni di spostamento, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="find-resource-provider-and-resource-type"></a>Trovare i provider di risorse e il tipo di risorsa

Per determinare se una risorsa può essere spostata, è necessario trovare il provider di risorse e il tipo di risorsa.

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
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
| servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | --------------- | ----------- |
| service | Yes | Yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | --------------- | ----------- |
| policyassignments | No  | No  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/runbooks | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| registrations | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| backupvault | No  | No  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| batchaccounts | Yes | Yes |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| mapapis | No  | No  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| biztalk | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| blueprintassignments | No  | No  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| redis | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| Profili | Yes | Yes |
| profiles/endpoints | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| certificateorders | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| domainnames | Yes | No  |
| virtualmachines | Yes | No  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| networksecuritygroups | No  | No  |
| reservedips | No  | No  |
| virtualnetworks | No  | No  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | No  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| availabilitysets | Yes | Yes |
| disks | Yes | Yes |
| galleries | No  | No  |
| galleries/images | No  | No  |
| galleries/images/versions | No  | No  |
| images | Yes | Yes |
| restorepointcollections | No  | No  |
| sharedvmimages | No  | No  |
| sharedvmimages/versions | No  | No  |
| snapshots | Yes | Yes |
| virtualmachines | Yes | Yes |
| virtualmachines/extensions | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

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
| registries | Yes | Yes |
| registries/buildtasks | Yes | Yes |
| registries/replications | No  | No  |
| registries/tasks | Yes | Yes |
| registries/webhooks | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| containerservices | No  | No  |
| managedclusters | No  | No  |
| openshiftmanagedclusters | No  | No  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dell'account di integrazione | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| hubs | Yes | Yes |

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
| catalogs | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| datafactories | Yes | Yes |
| factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| datalakeaccounts | No  | No  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

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
| servers | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| servergroups | No  | No  |
| servers | Yes | Yes |

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
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| labcenters | No  | No  |
| labs | Yes | No  |
| labs/servicerunners | Yes | Yes |
| labs/virtualmachines | Yes | No  |
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
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| domains | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| topics | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |
| spazi dei nomi | Yes | Yes |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| hanainstances | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dedicatedhsms | No  | No  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| jobs | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| actiongroups | Yes | Yes |
| activitylogalerts | No  | No  |
| alertrules | Yes | Yes |
| autoscalesettings | Yes | Yes |
| components | Yes | Yes |
| metricalerts | No  | No  |
| scheduledqueryrules | Yes | Yes |
| webtests | Yes | Yes |
| workbooks | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| iotapps | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| insiemi di credenziali | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| labaccounts | Yes | Yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| integrationaccounts | Yes | Yes |
| flussi di lavoro | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| commitmentplans | Yes | Yes |
| webservices | Yes | No  |
| aree di lavoro | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |
| accounts/workspaces | Yes | Yes |
| accounts/workspaces/projects | Yes | Yes |
| teamaccounts | Yes | Yes |
| teamaccounts/workspaces | Yes | Yes |
| teamaccounts/workspaces/projects | Yes | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| aree di lavoro | Yes | Yes |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| userassignedidentities | Yes | Yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| classicdevservices | No  | No  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| projects | No  | No  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| applicationgateways | No  | No  |
| applicationsecuritygroups | Yes | Yes |
| azurefirewalls | No  | No  |
| connections | Yes | Yes |
| ddosprotectionplans | No  | No  |
| dnszones | Yes | Yes |
| expressroutecircuits | No  | No  |
| expressroutecrossconnections | No  | No  |
| expressroutegateways | No  | No  |
| expressrouteports | No  | No  |
| frontdoors | Yes | Yes |
| frontdoorwebapplicationfirewallpolicies | Yes | Yes |
| interfaceendpoints | No  | No  |
| loadbalancers | Yes | Yes |
| localnetworkgateways | Yes | Yes |
| networkintentpolicies | Yes | Yes |
| networkinterfaces | Yes | Yes |
| networkprofiles | No  | No  |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/lenses | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | No  | No  |
| routetables | Yes | Yes |
| serviceendpointpolicies | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| virtualhubs | Yes | Yes |
| virtualnetworkgateways | Yes | Yes |
| virtualnetworks | Yes | Yes |
| virtualnetworktaps | No  | No  |
| virtualwans | Yes | Yes |
| vpngateways | Yes | Yes |
| vpnsites | Yes | Yes |
| webapplicationfirewallpolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Yes | Yes |
| namespaces/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| aree di lavoro | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| managementconfigurations | Yes | Yes |
| solutions | Yes | Yes |
| Viste | Yes | Yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| dashboards | Yes | Yes |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| capacities | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| insiemi di credenziali | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Yes | No  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| flows | Yes | Yes |
| jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| searchservices | Yes | Yes |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| clusters | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| scala Web | Yes | Yes |
| networks | Yes | Yes |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| signalr | Yes | Yes |

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
| managedinstances | Yes | Yes |
| managedinstances/databases | Yes | Yes |
| servers | Yes | Yes |
| servers/databases | Yes | Yes |
| servers/elasticpools | Yes | Yes |
| virtualclusters | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| managers | No  | No  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| streamingjobs | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| environments | Yes | Yes |
| environments/eventsources | Yes | Yes |
| environments/referencedatasets | Yes | Yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |
| account/extension | Yes | Yes |
| account/project | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| certificates | No  | Yes |
| classicmobileservices | No  | No  |
| connectiongateways | Yes | Yes |
| connections | Yes | Yes |
| customapis | Yes | Yes |
| hostingenvironments | No  | No  |
| serverfarms | Yes | Yes |
| siti | Yes | Yes |
| sites/premieraddons | Yes | Yes |
| sites/slots | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | -------------- | ------------ |
| deviceservices | Yes | Yes |


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
