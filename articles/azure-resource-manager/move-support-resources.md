---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 093c20407cb6210125106189f36566f539de0dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721109"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Fornisce inoltre informazioni sulle condizioni speciali da considerare durante lo spostamento di una risorsa.

Passa a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
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
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
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
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

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
| actionrules | Sì | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servizio | Yes | Sì |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| configurationstores | Sì | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| apiapps | No | No |
| appidentities | No | No |
| gateways | No | No |

> [!IMPORTANT]
> Visualizzare [servizio App di passare informazioni aggiuntive](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| policyassignments | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| automationaccounts | Sì | Sì |
| automationaccounts/configurations | Sì | Sì |
| automationaccounts/runbooks | Sì | Sì |

> [!IMPORTANT]
> I runbook devono esistere nello stesso gruppo di risorse dell'Account di automazione.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sì | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| registrations | Yes | Sì |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| backupvault | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| batchaccounts | Sì | Sì |

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
| biztalk | Sì | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blockchainmembers | Sì | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| blueprintassignments | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| botservices | Sì | Sì |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| redis | Yes | Sì |

> [!IMPORTANT]
> Se la Cache di Azure per l'istanza di Redis viene configurata con una rete virtuale, l'istanza può essere spostato in un'altra sottoscrizione. Visualizzare [reti virtuali spostare limitazioni](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| profiles | Sì | Sì |
| profiles/endpoints | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificateorders | Sì | Sì |

> [!IMPORTANT]
> Visualizzare [servizio App di passare informazioni aggiuntive](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainnames | Yes | No |
| virtualmachines | Yes | No |

> [!IMPORTANT]
> Visualizzare [di distribuzione classica spostare indicazioni](./move-limitations/classic-model-move-limitations.md). Risorse di distribuzione classico possono essere spostate tra le sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| networksecuritygroups | No | No |
| reservedips | No | No |
| virtualnetworks | No | No |

> [!IMPORTANT]
> Visualizzare [di distribuzione classica spostare indicazioni](./move-limitations/classic-model-move-limitations.md). Risorse di distribuzione classico possono essere spostate tra le sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | No |

> [!IMPORTANT]
> Visualizzare [di distribuzione classica spostare indicazioni](./move-limitations/classic-model-move-limitations.md). Risorse di distribuzione classico possono essere spostate tra le sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| availabilitysets | Sì | Sì |
| disks | Sì | Sì |
| galleries | No | No |
| galleries/images | No | No |
| galleries/images/versions | No | No |
| hostgroups | No | No |
| hostgroups/hosts | No | No |
| images | Sì | Sì |
| proximityplacementgroups | No | No |
| restorepointcollections | No | No |
| sharedvmimages | No | No |
| sharedvmimages/versions | No | No |
| snapshots | Sì | Sì |
| virtualmachines | Sì | Sì |
| virtualmachines/extensions | Yes | Sì |
| virtualmachinescalesets | Sì | Yes |

> [!IMPORTANT]
> Visualizzare [materiale sussidiario di spostare le macchine virtuali](./move-limitations/virtual-machines-move-limitations.md).

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
| registries | Yes | Sì |
| registries/buildtasks | Sì | Sì |
| registries/replications | Sì | Sì |
| registries/tasks | Sì | Yes |
| registries/webhooks | Sì | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| containerservices | No | No |
| managedclusters | No | No |
| openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Sì | Sì |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dell'account di integrazione | Yes | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hubs | Sì | Sì |

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
| catalogs | Sì | Yes |
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
| datafactories | Yes | Sì |
| factories | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datalakeaccounts | No | No |

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
| services | No | No |
| services/projects | No | No |
| slot | No | No |

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
| servergroups | No | No |
| servers | Sì | Sì |
| serversv2 | Yes | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| artifactsources | Yes | Sì |
| rollouts | Sì | Sì |
| servicetopologies | Yes | Sì |
| servicetopologies/services | Sì | Sì |
| servicetopologies/services/serviceunits | Sì | Sì |
| steps | Sì | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| elasticpools | No | No |
| elasticpools/iothubtenants | No | No |
| iothubs | Sì | Sì |
| provisioningservices | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| controllers | No | No |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labcenters | No | No |
| labs | Sì | No |
| / ambienti lab | Sì | Sì |
| labs/servicerunners | Yes | Sì |
| labs/virtualmachines | Yes | No |
| schedules | Sì | Yes |

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
| databaseaccounts | Sì | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Yes | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |
| topics | Yes | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |
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

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Sì | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Macchine | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datamanagers | Sì | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| jobs | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |
| actiongroups | Yes | Sì |
| activitylogalerts | No | No |
| alertrules | Sì | Sì |
| autoscalesettings | Sì | Sì |
| components | Yes | Sì |
| guestdiagnosticsettings | No | No |
| metricalerts | No | No |
| notificationgroups | No | No |
| notificationrules | No | No |
| scheduledqueryrules | Sì | Sì |
| webtests | Sì | Sì |
| workbooks | Yes | Sì |

> [!IMPORTANT]
> Assicurarsi che lo spostamento alla nuova sottoscrizione non superano [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotapps | Yes | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| checknameavailability | Sì | Sì |
| graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hsmpools | No | No |
| insiemi di credenziali | Yes | Sì |

> [!IMPORTANT]
> Insiemi di credenziali chiave utilizzata per la crittografia del disco non è possibile spostare un gruppo di risorse nella stessa sottoscrizione o tra sottoscrizioni.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| labaccounts | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Yes |

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
| flussi di lavoro | Sì | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| commitmentplans | Sì | Yes |
| webservices | Yes | No |
| aree di lavoro | Sì | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Sì |

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
| account | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mediaservices | Sì | Sì |
| mediaservices/liveevents | Yes | Sì |
| mediaservices/streamingendpoints | Sì | Yes |

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
| applicationsecuritygroups | Yes | Sì |
| azurefirewalls | Sì | Yes |
| bastionhosts | No | No |
| connections | Sì | Sì |
| ddoscustompolicies | Sì | Yes |
| ddosprotectionplans | No | No |
| dnszones | Yes | Sì |
| expressroutecircuits | No | No |
| expressroutecrossconnections | No | No |
| expressroutegateways | No | No |
| expressrouteports | No | No |
| frontdoors | No | No |
| frontdoorwebapplicationfirewallpolicies | No | No |
| loadbalancers | Sì - SKU Basic<br>No - SKU Standard | Sì - SKU Basic<br>No - SKU Standard |
| localnetworkgateways | Sì | Sì |
| natgateways | Sì | Sì |
| networkintentpolicies | Sì | Yes |
| networkinterfaces | Sì | Sì |
| networkprofiles | No | No |
| networksecuritygroups | Sì | Sì |
| networkwatchers | Sì | Yes |
| networkwatchers/connectionmonitors | Sì | Yes |
| networkwatchers/lenses | Yes | Sì |
| networkwatchers/pingmeshes | Sì | Sì |
| p2svpngateways | No | No |
| privatednszones | Sì | Yes |
| privatednszones/virtualnetworklinks | Sì | Sì |
| privateendpoints | No | No |
| privatelinkservices | No | No |
| publicipaddresses | Sì - SKU Basic<br>No - SKU Standard | Sì - SKU Basic<br>No - SKU Standard |
| publicipprefixes | Sì | Sì |
| routefilters | No | No |
| routetables | Yes | Sì |
| securegateways | Sì | Sì |
| serviceendpointpolicies | Sì | Sì |
| trafficmanagerprofiles | Sì | Sì |
| virtualhubs | No | No |
| virtualnetworkgateways | Sì | Yes |
| virtualnetworks | Yes | Sì |
| virtualnetworktaps | No | No |
| virtualwans | No | No |
| vpngateways | No | No |
| vpnsites | No | No |
| webapplicationfirewallpolicies | Sì | Sì |

> [!IMPORTANT]
> Visualizzare [reti virtuali spostare indicazioni](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |
| namespaces/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | Sì | Sì |

> [!IMPORTANT]
> Assicurarsi che lo spostamento alla nuova sottoscrizione non superano [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sì | Yes |
| solutions | Yes | Yes |
| Viste | Sì | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| peering | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dashboards | Yes | Sì |

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
| insiemi di credenziali | Sì | Sì |

> [!IMPORTANT]
> Visualizzare [servizi di ripristino spostare indicazioni](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Yes | Sì |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | Sì | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| flows | Sì | Sì |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| searchservices | Sì | Yes |

> [!IMPORTANT]
> È possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Sì | Sì |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| gateways | No | No |
| nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| scala Web | No | No |
| clusters | Sì | Sì |
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
| gateways | Sì | Sì |
| networks | Yes | Yes |
| chiavi private | Sì | Sì |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| signalr | Sì | Sì |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| siterecoveryvault | No | No |

> [!IMPORTANT]
> Visualizzare [servizi di ripristino spostare indicazioni](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

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
| instancepools | Sì | Yes |
| managedinstances | Sì | Yes |
| managedinstances/databases | Sì | Yes |
| servers | Yes | Sì |
| servers/databases | Sì | Yes |
| servers/elasticpools | Sì | Yes |
| virtualclusters | Sì | Sì |

> [!IMPORTANT]
> Un database e server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Yes | Sì |
| sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Sì |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| Cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | Yes | Sì |

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
| streamingjobs | Sì | Yes |

> [!IMPORTANT]
> Stream Analitica i processi non possono essere spostati durante l'esecuzione dello stato.

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
| environments | Sì | Yes |
| environments/eventsources | Sì | Sì |
| environments/referencedatasets | Sì | Sì |

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
| account | Sì | Yes |
| account/extension | Sì | Sì |
| account/project | Sì | Sì |

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sì | Yes |
| dedicatedcloudservices | Sì | Sì |
| virtualmachines | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificates | No | Sì |
| connectiongateways | Sì | Yes |
| connections | Sì | Yes |
| customapis | Sì | Sì |
| hostingenvironments | No | No |
| serverfarms | Yes | Yes |
| siti | Yes | Sì |
| sites/premieraddons | Sì | Sì |
| sites/slots | Sì | Sì |

> [!IMPORTANT]
> Visualizzare [servizio App di passare informazioni aggiuntive](./move-limitations/app-service-move-limitations.md).

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

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
