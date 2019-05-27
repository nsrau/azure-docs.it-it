---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 5/17/2019
ms.author: tomfitz
ms.openlocfilehash: dcac557f29a987ea732744c1b82f90f858623ec5
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872561"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Anche se un tipo di risorsa supporta l'operazione di spostamento, potrebbero esservi condizioni che impediscano alla risorsa di essere spostata. Per informazioni dettagliate sulle condizioni che influiscono sulle operazioni di spostamento, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainservices | N. | N. |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| tenants | N. | N. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| actionrules | Sì | Sì |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| server | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servizio | Sì | Sì |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| configurationstores | Sì | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| apiapps | N. | N. |
| appidentities | N. | N. |
| gateways | N. | N. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| policyassignments | N. | N. |

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
| backupvault | N. | N. |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| batchaccounts | Sì | Sì |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | N. | N. |
| fileservers | N. | N. |
| processi | N. | N. |
| aree di lavoro | N. | N. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mapapis | N. | N. |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| biztalk | Sì | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blockchainmembers | Sì | Sì |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blueprintassignments | N. | N. |

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
| domainnames | Sì | N. |
| virtualmachines | Sì | N. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| networksecuritygroups | N. | N. |
| reservedips | N. | N. |
| virtualnetworks | N. | N. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | N. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| availabilitysets | Sì | Sì |
| dischi | Sì | Sì |
| galleries | N. | N. |
| galleries/images | N. | N. |
| galleries/images/versions | N. | N. |
| hostgroups | N. | N. |
| hostgroups/hosts | N. | N. |
| immagini | Sì | Sì |
| proximityplacementgroups | N. | N. |
| restorepointcollections | N. | N. |
| sharedvmimages | N. | N. |
| sharedvmimages/versions | N. | N. |
| snapshot | Sì | Sì |
| virtualmachines | Sì | Sì |
| virtualmachines/extensions | Sì | Sì |
| virtualmachinescalesets | Sì | Sì |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | N. | N. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containergroups | N. | N. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registries | Sì | Sì |
| registries/buildtasks | Sì | Sì |
| registries/replications | Sì | Sì |
| registries/tasks | Sì | Sì |
| registries/webhooks | Sì | Sì |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containerservices | N. | N. |
| managedclusters | N. | N. |
| openshiftmanagedclusters | N. | N. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | Sì | Sì |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | N. | N. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| connettori | Sì | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hubs | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| processi | N. | N. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| databoxedgedevices | N. | N. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | N. | N. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| catalogs | Sì | Sì |
| datacatalogs | N. | N. |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| connectionmanagers | N. | N. |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| packages | N. | N. |
| plans | N. | N. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datafactories | Sì | Sì |
| factories | Sì | Sì |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datalakeaccounts | N. | N. |

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
| services | N. | N. |
| services/projects | N. | N. |
| slot | N. | N. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| server | Sì | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| server | Sì | Sì |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servergroups | N. | N. |
| server | Sì | Sì |
| serversv2 | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| artifactsources | Sì | Sì |
| rollouts | Sì | Sì |
| servicetopologies | Sì | Sì |
| servicetopologies/services | Sì | Sì |
| servicetopologies/services/serviceunits | Sì | Sì |
| steps | Sì | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| elasticpools | N. | N. |
| elasticpools/iothubtenants | N. | N. |
| iothubs | Sì | Sì |
| provisioningservices | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| controllers | N. | N. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labcenters | N. | N. |
| labs | Sì | N. |
| / ambienti lab | Sì | Sì |
| labs/servicerunners | Sì | Sì |
| labs/virtualmachines | Sì | N. |
| schedules | Sì | Sì |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dnszones | N. | N. |
| dnszones/a | N. | N. |
| dnszones/aaaa | N. | N. |
| dnszones/cname | N. | N. |
| dnszones/mx | N. | N. |
| dnszones/ptr | N. | N. |
| dnszones/srv | N. | N. |
| dnszones/txt | N. | N. |
| trafficmanagerprofiles | N. | N. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Sì | Sì |

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
| account | N. | N. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hanainstances | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Sì | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| computer | N. | N. |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datamanagers | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| processi | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | N. | N. |
| actiongroups | Sì | Sì |
| activitylogalerts | N. | N. |
| alertrules | Sì | Sì |
| autoscalesettings | Sì | Sì |
| components | Sì | Sì |
| guestdiagnosticsettings | N. | N. |
| metricalerts | N. | N. |
| notificationgroups | N. | N. |
| notificationrules | N. | N. |
| scheduledqueryrules | N. | N. |
| webtests | Sì | Sì |
| cartelle di lavoro | Sì | Sì |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotapps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| checknameavailability | Sì | Sì |
| grafo | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hsmpools | N. | N. |
| insiemi di credenziali | Sì | Sì |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labaccounts | Sì | Sì |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | N. | N. |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingenvironments | N. | N. |
| integrationaccounts | Sì | Sì |
| integrationserviceenvironments | N. | N. |
| isolatedenvironments | N. | N. |
| flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| commitmentplans | Sì | Sì |
| webservices | Sì | N. |
| aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sì | Sì |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | N. | N. |
| accounts/workspaces | N. | N. |
| accounts/workspaces/projects | N. | N. |
| teamaccounts | N. | N. |
| teamaccounts/workspaces | N. | N. |
| teamaccounts/workspaces/projects | N. | N. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hostingaccounts | N. | N. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | N. | N. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| userassignedidentities | N. | N. |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| classicdevservices | N. | N. |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mediaservices | Sì | Sì |
| mediaservices/liveevents | Sì | Sì |
| mediaservices/streamingendpoints | Sì | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| assessmentprojects | N. | N. |
| migrateprojects | N. | N. |
| projects | N. | N. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| netappaccounts | N. | N. |
| netappaccounts/capacitypools | N. | N. |
| netappaccounts/capacitypools/volumes | N. | N. |
| netappaccounts/capacitypools/volumes/mounttargets | N. | N. |
| netappaccounts/capacitypools/volumes/snapshots | N. | N. |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicationgateways | N. | N. |
| applicationgatewaywebapplicationfirewallpolicies | N. | N. |
| applicationsecuritygroups | Sì | Sì |
| azurefirewalls | Sì | Sì |
| bastionhosts | N. | N. |
| connessioni | Sì | Sì |
| ddoscustompolicies | Sì | Sì |
| ddosprotectionplans | N. | N. |
| dnszones | Sì | Sì |
| expressroutecircuits | N. | N. |
| expressroutecrossconnections | N. | N. |
| expressroutegateways | N. | N. |
| expressrouteports | N. | N. |
| frontdoor | Sì | Sì |
| frontdoorwebapplicationfirewallpolicies | Sì | Sì |
| loadbalancers | Sì | Sì |
| localnetworkgateways | Sì | Sì |
| natgateways | Sì | Sì |
| networkintentpolicies | Sì | Sì |
| networkinterfaces | Sì | Sì |
| networkprofiles | N. | N. |
| networksecuritygroups | Sì | Sì |
| networkwatchers | Sì | Sì |
| networkwatchers/connectionmonitors | Sì | Sì |
| networkwatchers/lenses | Sì | Sì |
| networkwatchers/pingmeshes | Sì | Sì |
| p2svpngateways | N. | N. |
| privatednszones | Sì | Sì |
| privatednszones/virtualnetworklinks | Sì | Sì |
| privateendpoints | N. | N. |
| privatelinkservices | N. | N. |
| publicipaddresses | Sì | Sì |
| publicipprefixes | Sì | Sì |
| routefilters | N. | N. |
| routetables | Sì | Sì |
| securegateways | Sì | Sì |
| serviceendpointpolicies | Sì | Sì |
| trafficmanagerprofiles | Sì | Sì |
| virtualhubs | N. | N. |
| virtualnetworkgateways | Sì | Sì |
| virtualnetworks | Sì | Sì |
| virtualnetworktaps | N. | N. |
| virtualwans | N. | N. |
| vpngateways | N. | N. |
| vpnsites | N. | N. |
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
| visualizzazioni | Sì | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| peering | N. | N. |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dashboards | Sì | Sì |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| rootresources | N. | N. |

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
| account | N. | N. |

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
| applicazioni | Sì | N. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| flussi | Sì | Sì |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| searchservices | Sì | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Sì | Sì |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| gateways | N. | N. |
| nodes | N. | N. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | N. | N. |
| clusters | Sì | Sì |
| containergroups | N. | N. |
| containergroupsets | N. | N. |
| edgeclusters | N. | N. |
| Reti | N. | N. |
| secretstores | N. | N. |
| volumes | N. | N. |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | Sì | Sì |
| containergroups | N. | N. |
| gateways | Sì | Sì |
| Reti | Sì | Sì |
| segreti | Sì | Sì |
| volumes | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| signalr | Sì | Sì |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| siterecoveryvault | N. | N. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| appliancedefinitions | N. | N. |
| appliances | N. | N. |
| applicationdefinitions | N. | N. |
| applicazioni | N. | N. |
| jitrequests | N. | N. |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| instancepools | Sì | Sì |
| managedinstances | Sì | Sì |
| managedinstances/databases | Sì | Sì |
| server | Sì | Sì |
| servers/databases | Sì | Sì |
| servers/elasticpools | Sì | Sì |
| virtualclusters | Sì | Sì |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sì | Sì |
| sqlvirtualmachines | Sì | Sì |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dwvm | N. | N. |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | Sì |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Cache | N. | N. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sì | Sì |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | N. | N. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | N. | N. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managers | N. | N. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| streamingjobs | Sì | Sì |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | N. | N. |
| environments/eventsources | N. | N. |
| istanze | N. | N. |
| instances/environments | N. | N. |
| instances/environments/eventsources | N. | N. |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| providerregistrations | N. | N. |
| risorse | N. | N. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | Sì | Sì |
| environments/eventsources | Sì | Sì |
| environments/referencedatasets | Sì | Sì |

## <a name="microsofttoken"></a>Microsoft.Token
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Archivia | N. | N. |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| imagetemplates | N. | N. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Sì | Sì |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sì | Sì |
| dedicatedcloudservices | Sì | Sì |
| virtualmachines | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificati | N. | Sì |
| connectiongateways | Sì | Sì |
| connessioni | Sì | Sì |
| customapis | Sì | Sì |
| hostingenvironments | N. | N. |
| serverfarms | Sì | Sì |
| siti | Sì | Sì |
| sites/premieraddons | Sì | Sì |
| sites/slots | Sì | Sì |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| deviceservices | N. | N. |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicationgroups | N. | N. |
| hostpools | N. | N. |
| aree di lavoro | N. | N. |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
