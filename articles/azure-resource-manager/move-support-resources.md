---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 73f4b6fe4714d21c12d2c7bd387cd30f6f711d5a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624314"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Vengono inoltre fornite informazioni sulle condizioni speciali da considerare quando si trasferisce una risorsa.

Passare a uno spazio dei nomi del provider di risorse:
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
> - [Microsoft. blockchain](#microsoftblockchain)
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
| actionrules | Sì | Sì |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| server | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servizio | Yes | Yes |

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
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| policyassignments | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| automationaccounts | Sì | Sì |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/runbooks | Sì | Sì |

> [!IMPORTANT]
> Manuali operativi deve esistere nello stesso gruppo di risorse dell'account di automazione.

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
| processi | No | No |
| aree di lavoro | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| biztalk | Sì | Yes |

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
| botservices | Sì | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| redis | Sì | Yes |

> [!IMPORTANT]
> Se la cache di Azure per l'istanza di redis è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Vedere [limitazioni di spostamento delle reti virtuali](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| profiles | Yes | Yes |
| profiles/endpoints | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificateorders | Sì | Yes |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domainnames | Yes | No |
| virtualmachines | Yes | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| networksecuritygroups | No | No |
| reservedips | No | No |
| virtualnetworks | No | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storageaccounts | Sì | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | Sì | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| availabilitysets | Sì | Yes |
| dischi | Sì | Sì |
| galleries | No | No |
| galleries/images | No | No |
| galleries/images/versions | No | No |
| gruppi host | No | No |
| gruppi host/host | No | No |
| immagini | Sì | Sì |
| proximityplacementgroups | No | No |
| restorepointcollections | No | No |
| sharedvmimages | No | No |
| sharedvmimages/versions | No | No |
| snapshot | Yes | Sì |
| virtualmachines | Yes | Yes |
| virtualmachines/extensions | Yes | Sì |
| virtualmachinescalesets | Yes | Sì |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento di macchine virtuali](./move-limitations/virtual-machines-move-limitations.md).

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
| registries/tasks | Yes | Yes |
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
| applicazioni | Yes | Sì |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| connettori | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hubs | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| processi | No | No |

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
| catalogs | Sì | Sì |
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
| factories | Sì | Yes |

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
| server | Sì | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| server | Sì | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| servergroups | No | No |
| server | Sì | Sì |
| serversv2 | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| artifactsources | Sì | Sì |
| rollouts | Yes | Yes |
| servicetopologies | Yes | Sì |
| servicetopologies/services | Sì | Sì |
| servicetopologies/services/serviceunits | Sì | Yes |
| steps | Yes | Sì |

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
| labs | Yes | No |
| Lab/ambienti | Sì | Sì |
| labs/servicerunners | Sì | Yes |
| labs/virtualmachines | Sì | No |
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
| databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Sì | Sì |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Sì | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| domains | Yes | Sì |
| topics | Sì | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hanainstances | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Yes | Sì |

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| services | Sì | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| computer | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| processi | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |
| actiongroups | Sì | Yes |
| activitylogalerts | No | No |
| alertrules | Sì | Yes |
| autoscalesettings | Sì | Sì |
| components | Sì | Sì |
| guestdiagnosticsettings | No | No |
| metricalerts | No | No |
| notificationgroups | No | No |
| notificationrules | No | No |
| scheduledqueryrules | Sì | Yes |
| webtests | Sì | Sì |
| cartelle di lavoro | Sì | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotapps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| checknameavailability | Sì | Yes |
| graph | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| hsmpools | No | No |
| insiemi di credenziali | Sì | Yes |

> [!IMPORTANT]
> Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o tra sottoscrizioni.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| clusters | Sì | Sì |

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
| integrationaccounts | Yes | Sì |
| integrationserviceenvironments | No | No |
| isolatedenvironments | No | No |
| flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| commitmentplans | Sì | Yes |
| webservices | Sì | No |
| aree di lavoro | Yes | Sì |

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
| account | Sì | Sì |

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
| account | Sì | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| mediaservices | Yes | Sì |
| mediaservices/liveevents | Sì | Sì |
| mediaservices/streamingendpoints | Yes | Sì |

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
| applicationsecuritygroups | Sì | Yes |
| azurefirewalls | Sì | Sì |
| bastionhosts | No | No |
| connessioni | Sì | Yes |
| ddoscustompolicies | Sì | Sì |
| ddosprotectionplans | No | No |
| dnszones | Yes | Sì |
| expressroutecircuits | No | No |
| expressroutecrossconnections | No | No |
| expressroutegateways | No | No |
| expressrouteports | No | No |
| frontdoor | No | No |
| frontdoorwebapplicationfirewallpolicies | No | No |
| loadbalancers | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
| localnetworkgateways | Sì | Yes |
| natgateways | Yes | Yes |
| networkintentpolicies | Yes | Sì |
| networkinterfaces | Sì | Sì |
| networkprofiles | No | No |
| networksecuritygroups | Sì | Sì |
| networkwatchers | Sì | Sì |
| networkwatchers/connectionmonitors | Sì | Sì |
| networkwatchers/lenses | Sì | Yes |
| networkwatchers/pingmeshes | Sì | Sì |
| p2svpngateways | No | No |
| privatednszones | Sì | Sì |
| privatednszones/virtualnetworklinks | Yes | Sì |
| privateendpoints | No | No |
| privatelinkservices | No | No |
| publicipaddresses | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
| publicipprefixes | Yes | Sì |
| routefilters | No | No |
| routetables | Sì | Yes |
| securegateways | Sì | Sì |
| serviceendpointpolicies | Yes | Sì |
| trafficmanagerprofiles | Sì | Yes |
| virtualhubs | No | No |
| virtualnetworkgateways | Sì | Sì |
| virtualnetworks | Sì | Sì |
| virtualnetworktaps | No | No |
| virtualwans | No | No |
| vpngateways | No | No |
| vpnsites | No | No |
| webapplicationfirewallpolicies | Sì | Yes |

> [!IMPORTANT]
> Vedere [linee guida](./move-limitations/networking-move-limitations.md)per lo spostamento di rete.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Yes |
| namespaces/notificationhubs | Sì | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| aree di lavoro | Yes | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sì | Yes |
| solutions | Sì | Sì |
| visualizzazioni | Yes | Sì |

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
| workspacecollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| capacities | Yes | Sì |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| account | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| insiemi di credenziali | Sì | Yes |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | Yes | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| flussi | Sì | Yes |
| jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| searchservices | Sì | Yes |

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Yes | Sì |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| gateways | No | No |
| nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| spazi dei nomi | Sì | Sì |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | No | No |
| clusters | Yes | Yes |
| containergroups | No | No |
| containergroupsets | No | No |
| edgeclusters | No | No |
| Reti | No | No |
| secretstores | No | No |
| volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| applicazioni | Sì | Yes |
| containergroups | No | No |
| gateways | Yes | Sì |
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
| siterecoveryvault | No | No |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| appliancedefinitions | No | No |
| appliances | No | No |
| applicationdefinitions | No | No |
| applicazioni | No | No |
| jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| instancepools | No | No |
| managedinstances | No | No |
| managedinstances/databases | No | No |
| server | Yes | Sì |
| servers/databases | Sì | Yes |
| servers/elasticpools | Sì | Sì |
| virtualclusters | Sì | Sì |

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sì | Sì |
| sqlvirtualmachines | Sì | Sì |

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
| cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sì | Yes |

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
| streamingjobs | Sì | Sì |

> [!IMPORTANT]
> I processi di analisi di flusso non possono essere spostati in stato di esecuzione.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | No | No |
| environments/eventsources | No | No |
| istanze | No | No |
| instances/environments | No | No |
| instances/environments/eventsources | No | No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| providerregistrations | No | No |
| risorse | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| environments | Sì | Sì |
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
| account | Sì | Sì |
| account/extension | Sì | Sì |
| account/project | Sì | Yes |

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sì | Yes |
| dedicatedcloudservices | Yes | Sì |
| virtualmachines | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
| ------------- | ----------- | ---------- |
| certificati | No | Sì |
| connectiongateways | Sì | Sì |
| connessioni | Sì | Sì |
| customapis | Sì | Sì |
| hostingenvironments | No | No |
| serverfarms | Sì | Sì |
| siti | Yes | Sì |
| sites/premieraddons | Sì | Sì |
| sites/slots | Sì | Sì |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

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
