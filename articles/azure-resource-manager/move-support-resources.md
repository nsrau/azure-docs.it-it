---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 6/6/2019
ms.author: tomfitz
ms.openlocfilehash: 314b28edbd5770186d96fb2a2b203f26ff27bda0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752361"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Anche se un tipo di risorsa supporta l'operazione di spostamento, potrebbero esservi condizioni che impediscano alla risorsa di essere spostata. Per informazioni dettagliate sulle condizioni che influiscono sulle operazioni di spostamento, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainservices | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| tenants | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| actionrules | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| service | Yes | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| configurationstores | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| apiapps | No | No |
| appidentities | No | No |
| gateways | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| policyassignments | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/runbooks | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registrations | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| backupvault | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | No | No |
| fileservers | No | No |
| jobs | No | No |
| aree di lavoro | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| biztalk | Yes | Yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blockchainmembers | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blueprintassignments | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| redis | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Profili | Yes | Yes |
| profiles/endpoints | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificateorders | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainnames | Yes | No |
| virtualmachines | Yes | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| networksecuritygroups | No | No |
| reservedips | No | No |
| virtualnetworks | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| availabilitysets | Yes | Yes |
| disks | Yes | Yes |
| galleries | No | No |
| galleries/images | No | No |
| galleries/images/versions | No | No |
| hostgroups | No | No |
| hostgroups/hosts | No | No |
| images | Yes | Yes |
| proximityplacementgroups | No | No |
| restorepointcollections | No | No |
| sharedvmimages | No | No |
| sharedvmimages/versions | No | No |
| snapshots | Yes | Yes |
| virtualmachines | Yes | Yes |
| virtualmachines/extensions | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registries | Yes | Yes |
| registries/buildtasks | Yes | Yes |
| registries/replications | Yes | Yes |
| registries/tasks | Yes | Yes |
| registries/webhooks | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containerservices | No | No |
| managedclusters | No | No |
| openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dell'account di integrazione | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hubs | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| jobs | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| databoxedgedevices | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| catalogs | Yes | Yes |
| datacatalogs | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| packages | No | No |
| plans | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datafactories | Yes | Yes |
| factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | No | No |
| services/projects | No | No |
| slot | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servergroups | No | No |
| servers | Yes | Yes |
| serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| artifactsources | Yes | Yes |
| rollouts | Yes | Yes |
| servicetopologies | Yes | Yes |
| servicetopologies/services | Yes | Yes |
| servicetopologies/services/serviceunits | Yes | Yes |
| steps | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| elasticpools | No | No |
| elasticpools/iothubtenants | No | No |
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| controllers | No | No |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labcenters | No | No |
| labs | Yes | No |
| / ambienti lab | Yes | Yes |
| labs/servicerunners | Yes | Yes |
| labs/virtualmachines | Yes | No |
| schedules | Yes | Yes |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dnszones | No | No |
| dnszones/a | No | No |
| dnszones/aaaa | No | No |
| dnszones/cname | No | No |
| dnszones/mx | No | No |
| dnszones/ptr | No | No |
| dnszones/srv | No | No |
| dnszones/txt | No | No |
| trafficmanagerprofiles | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Yes | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Yes | Yes |
| topics | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hanainstances | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Macchine | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| jobs | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |
| actiongroups | Yes | Yes |
| activitylogalerts | No | No |
| alertrules | Yes | Yes |
| autoscalesettings | Yes | Yes |
| components | Yes | Yes |
| guestdiagnosticsettings | No | No |
| metricalerts | No | No |
| notificationgroups | No | No |
| notificationrules | No | No |
| scheduledqueryrules | Yes | Yes |
| webtests | Yes | Yes |
| workbooks | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| checknameavailability | Yes | Yes |
| graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hsmpools | No | No |
| insiemi di credenziali | Yes | Yes |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labaccounts | Yes | Yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingenvironments | No | No |
| integrationaccounts | Yes | Yes |
| integrationserviceenvironments | No | No |
| isolatedenvironments | No | No |
| flussi di lavoro | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| commitmentplans | Yes | Yes |
| webservices | Yes | No |
| aree di lavoro | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |
| accounts/workspaces | No | No |
| accounts/workspaces/projects | No | No |
| teamaccounts | No | No |
| teamaccounts/workspaces | No | No |
| teamaccounts/workspaces/projects | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingaccounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| userassignedidentities | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| assessmentprojects | No | No |
| migrateprojects | No | No |
| projects | No | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| netappaccounts | No | No |
| netappaccounts/capacitypools | No | No |
| netappaccounts/capacitypools/volumes | No | No |
| netappaccounts/capacitypools/volumes/mounttargets | No | No |
| netappaccounts/capacitypools/volumes/snapshots | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicationgateways | No | No |
| applicationgatewaywebapplicationfirewallpolicies | No | No |
| applicationsecuritygroups | Yes | Yes |
| azurefirewalls | Yes | Yes |
| bastionhosts | No | No |
| connections | Yes | Yes |
| ddoscustompolicies | Yes | Yes |
| ddosprotectionplans | No | No |
| dnszones | Yes | Yes |
| expressroutecircuits | No | No |
| expressroutecrossconnections | No | No |
| expressroutegateways | No | No |
| expressrouteports | No | No |
| frontdoors | Yes | Yes |
| frontdoorwebapplicationfirewallpolicies | Yes | Yes |
| loadbalancers | Yes | Yes |
| localnetworkgateways | Yes | Yes |
| natgateways | Yes | Yes |
| networkintentpolicies | Yes | Yes |
| networkinterfaces | Yes | Yes |
| networkprofiles | No | No |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/lenses | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| p2svpngateways | No | No |
| privatednszones | Yes | Yes |
| privatednszones/virtualnetworklinks | Yes | Yes |
| privateendpoints | No | No |
| privatelinkservices | No | No |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | No | No |
| routetables | Yes | Yes |
| securegateways | Yes | Yes |
| serviceendpointpolicies | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| virtualhubs | No | No |
| virtualnetworkgateways | Yes | Yes |
| virtualnetworks | Yes | Yes |
| virtualnetworktaps | No | No |
| virtualwans | No | No |
| vpngateways | No | No |
| vpnsites | No | No |
| webapplicationfirewallpolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Yes | Yes |
| namespaces/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managementconfigurations | Yes | Yes |
| solutions | Yes | Yes |
| Viste | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| peering | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dashboards | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| capacities | Yes | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| insiemi di credenziali | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Yes | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| flows | Yes | Yes |
| jobcollections | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| searchservices | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Yes | Yes |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| gateways | No | No |
| nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | No | No |
| clusters | Yes | Yes |
| containergroups | No | No |
| containergroupsets | No | No |
| edgeclusters | No | No |
| networks | No | No |
| secretstores | No | No |
| volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Yes | Yes |
| containergroups | No | No |
| gateways | Yes | Yes |
| networks | Yes | Yes |
| chiavi private | Yes | Yes |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| siterecoveryvault | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| appliancedefinitions | No | No |
| appliances | No | No |
| applicationdefinitions | No | No |
| scala Web | No | No |
| jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| instancepools | Yes | Yes |
| managedinstances | Yes | Yes |
| managedinstances/databases | Yes | Yes |
| servers | Yes | Yes |
| servers/databases | Yes | Yes |
| servers/elasticpools | Yes | Yes |
| virtualclusters | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Yes | Yes |
| sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managers | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| streamingjobs | Yes | Yes |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | No | No |
| environments/eventsources | No | No |
| instances | No | No |
| instances/environments | No | No |
| instances/environments/eventsources | No | No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| providerregistrations | No | No |
| resources | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | Yes | Yes |
| environments/eventsources | Yes | Yes |
| environments/referencedatasets | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft.Token
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Archivia | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| imagetemplates | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |
| account/extension | Yes | Yes |
| account/project | Yes | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Yes | Yes |
| dedicatedcloudservices | Yes | Yes |
| virtualmachines | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificates | No | Yes |
| connectiongateways | Yes | Yes |
| connections | Yes | Yes |
| customapis | Yes | Yes |
| hostingenvironments | No | No |
| serverfarms | Yes | Yes |
| siti | Yes | Yes |
| sites/premieraddons | Yes | Yes |
| sites/slots | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| deviceservices | No | No |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicationgroups | No | No |
| hostpools | No | No |
| aree di lavoro | No | No |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
