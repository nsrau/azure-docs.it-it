---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438469"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Anche se un tipo di risorsa supporta l'operazione di spostamento, potrebbero esservi condizioni che impediscano alla risorsa di essere spostata. Per informazioni dettagliate sulle condizioni che influiscono sulle operazioni di spostamento, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainservices | No  | No  |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| tenants | No  | No  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| service | Sì | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| apiapps | No  | No  |
| appidentities | No  | No  |
| gateways | No  | No  |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| policyassignments | No  | No  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| automationaccounts | Sì | Sì |
| automationaccounts/configurations | Sì | Sì |
| automationaccounts/runbooks | Sì | Sì |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sì | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registrations | Sì | Sì |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| backupvault | No  | No  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| batchaccounts | Sì | Sì |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | No  | No  |
| fileservers | No  | No  |
| jobs | No  | No  |
| aree di lavoro | No  | No  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mapapis | No  | No  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| biztalk | Sì | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blockchainmembers | No  | No  |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blueprintassignments | No  | No  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| botservices | Sì | Sì |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| redis | Sì | Sì |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Profili | Sì | Sì |
| profiles/endpoints | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificateorders | Sì | Sì |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainnames | Sì | No  |
| virtualmachines | Sì | No  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| networksecuritygroups | No  | No  |
| reservedips | No  | No  |
| virtualnetworks | No  | No  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | No  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| availabilitysets | Sì | Sì |
| disks | Sì | Sì |
| galleries | No  | No  |
| galleries/images | No  | No  |
| galleries/images/versions | No  | No  |
| images | Sì | Sì |
| proximityplacementgroups | No  | No  |
| restorepointcollections | No  | No  |
| sharedvmimages | No  | No  |
| sharedvmimages/versions | No  | No  |
| snapshots | Sì | Sì |
| virtualmachines | Sì | Sì |
| virtualmachines/extensions | Sì | Sì |
| virtualmachinescalesets | Sì | Sì |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | No  | No  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | No  | No  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registries | Sì | Sì |
| registries/buildtasks | Sì | Sì |
| registries/replications | No  | No  |
| registries/tasks | Sì | Sì |
| registries/webhooks | Sì | Sì |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containerservices | No  | No  |
| managedclusters | No  | No  |
| openshiftmanagedclusters | No  | No  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Sì | Sì |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No  | No  |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dell'account di integrazione | Sì | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hubs | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| jobs | No  | No  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| databoxedgedevices | No  | No  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | No  | No  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| catalogs | Sì | Sì |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| connectionmanagers | No  | No  |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| packages | No  | No  |
| plans | No  | No  |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datafactories | Sì | Sì |
| factories | Sì | Sì |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datalakeaccounts | No  | No  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | No  | No  |
| services/projects | No  | No  |
| slot | No  | No  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Sì | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Sì | Sì |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servergroups | No  | No  |
| servers | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| artifactsources | No  | No  |
| rollouts | No  | No  |
| servicetopologies | No  | No  |
| servicetopologies/services | No  | No  |
| servicetopologies/services/serviceunits | No  | No  |
| steps | No  | No  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| elasticpools | No  | No  |
| elasticpools/iothubtenants | No  | No  |
| iothubs | Sì | Sì |
| provisioningservices | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| controllers | No  | No  |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labcenters | No  | No  |
| labs | Sì | No  |
| labs/servicerunners | Sì | Sì |
| labs/virtualmachines | Sì | No  |
| schedules | No  | No  |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
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
| ------------- | ----------- | ---------- |
| databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |
| topics | Sì | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No  | No  |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hanainstances | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datamanagers | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| jobs | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No  | No  |
| actiongroups | Sì | Sì |
| activitylogalerts | No  | No  |
| alertrules | Sì | Sì |
| autoscalesettings | Sì | Sì |
| components | Sì | Sì |
| guestdiagnosticsettings | No  | No  |
| metricalerts | No  | No  |
| notificationgroups | No  | No  |
| notificationrules | No  | No  |
| scheduledqueryrules | No  | No  |
| webtests | Sì | Sì |
| workbooks | Sì | Sì |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotapps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| checknameavailability | Sì | Sì |
| graph | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hsmpools | No  | No  |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labaccounts | No  | No  |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingenvironments | No  | No  |
| integrationaccounts | Sì | Sì |
| integrationserviceenvironments | No  | No  |
| isolatedenvironments | No  | No  |
| flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| commitmentplans | Sì | Sì |
| webservices | Sì | No  |
| aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sì | Sì |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |
| accounts/workspaces | Sì | Sì |
| accounts/workspaces/projects | Sì | Sì |
| teamaccounts | Sì | Sì |
| teamaccounts/workspaces | Sì | Sì |
| teamaccounts/workspaces/projects | Sì | Sì |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingaccounts | No  | No  |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | No  | No  |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| userassignedidentities | Sì | Sì |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| classicdevservices | No  | No  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mediaservices | Sì | Sì |
| mediaservices/liveevents | Sì | Sì |
| mediaservices/streamingendpoints | Sì | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| assessmentprojects | No  | No  |
| migrateprojects | No  | No  |
| projects | No  | No  |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| netappaccounts | No  | No  |
| netappaccounts/capacitypools | No  | No  |
| netappaccounts/capacitypools/volumes | No  | No  |
| netappaccounts/capacitypools/volumes/mounttargets | No  | No  |
| netappaccounts/capacitypools/volumes/snapshots | No  | No  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicationgateways | No  | No  |
| applicationsecuritygroups | Sì | Sì |
| azurefirewalls | Sì | Sì |
| bastionhosts | No  | No  |
| connections | Sì | Sì |
| ddoscustompolicies | Sì | Sì |
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
| natgateways | Sì | Sì |
| networkintentpolicies | Sì | Sì |
| networkinterfaces | Sì | Sì |
| networkprofiles | No  | No  |
| networksecuritygroups | Sì | Sì |
| networkwatchers | Sì | Sì |
| networkwatchers/connectionmonitors | Sì | Sì |
| networkwatchers/lenses | Sì | Sì |
| networkwatchers/pingmeshes | Sì | Sì |
| p2svpngateways | No  | No  |
| privatelinkservices | No  | No  |
| publicipaddresses | Sì | Sì |
| publicipprefixes | Sì | Sì |
| routefilters | No  | No  |
| routetables | Sì | Sì |
| securegateways | No  | No  |
| serviceendpointpolicies | Sì | Sì |
| trafficmanagerprofiles | Sì | Sì |
| virtualhubs | No  | No  |
| virtualnetworkgateways | Sì | Sì |
| virtualnetworks | Sì | Sì |
| virtualnetworktaps | No  | No  |
| virtualwans | No  | No  |
| vpngateways | No  | No  |
| vpnsites | Sì | Sì |
| webapplicationfirewallpolicies | Sì | Sì |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |
| namespaces/notificationhubs | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | Sì | Sì |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sì | Sì |
| solutions | Sì | Sì |
| Viste | Sì | Sì |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dashboards | Sì | Sì |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| rootresources | No  | No  |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| workspacecollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| capacities | Sì | Sì |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No  | No  |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Sì | No  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| flows | Sì | Sì |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| searchservices | Sì | Sì |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| gateways | No  | No  |
| nodes | No  | No  |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | No  | No  |
| clusters | Sì | Sì |
| containergroups | No  | No  |
| containergroupsets | No  | No  |
| edgeclusters | No  | No  |
| networks | No  | No  |
| secretstores | No  | No  |
| volumes | No  | No  |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Sì | Sì |
| containergroups | No  | No  |
| gateways | Sì | Sì |
| networks | Sì | Sì |
| chiavi private | Sì | Sì |
| volumes | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| signalr | Sì | Sì |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| siterecoveryvault | No  | No  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| appliancedefinitions | No  | No  |
| appliances | No  | No  |
| applicationdefinitions | No  | No  |
| scala Web | No  | No  |
| jitrequests | No  | No  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managedinstances | Sì | Sì |
| managedinstances/databases | Sì | Sì |
| servers | Sì | Sì |
| servers/databases | Sì | Sì |
| servers/elasticpools | Sì | Sì |
| servers/jobaccounts | No  | No  |
| servers/jobagents | No  | No  |
| virtualclusters | Sì | Sì |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sì | Sì |
| sqlvirtualmachines | Sì | Sì |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dwvm | No  | No  |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | Sì |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sì | Sì |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | No  | No  |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | No  | No  |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managers | No  | No  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| streamingjobs | Sì | Sì |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | No  | No  |
| environments/eventsources | No  | No  |
| instances | No  | No  |
| instances/environments | No  | No  |
| instances/environments/eventsources | No  | No  |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| providerregistrations | No  | No  |
| resources | No  | No  |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | Sì | Sì |
| environments/eventsources | Sì | Sì |
| environments/referencedatasets | Sì | Sì |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| imagetemplates | No  | No  |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificates | No  | Sì |
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
| ------------- | ----------- | ---------- |
| deviceservices | Sì | Sì |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
