---
title: Supporto per lo trasferimento di risorse di Azure tra aree
description: Elenca i tipi di risorse di Azure che possono essere spostati tra le aree di Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/21/2020
ms.author: raynew
ms.openlocfilehash: 70f981f2763dd36f0f417faec6c81e168e9856e7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040953"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Supporto per lo trasferimento di risorse di Azure tra aree

Questo articolo conferma se un tipo di risorsa di Azure è supportato per il passaggio a un'altra area di Azure. 

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
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
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
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
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
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | domainservices | No | 
> | DomainServices/ReplicaSets | No | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | tenants | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | actionrules | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | service |  Sì (utilizzo del modello) <br/><br/> [Spostare gestione API tra le aree](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | configurationstores | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | apiapps | Sì (utilizzo del modello)<br/><br/> [Spostare un'app del servizio app in un'altra area](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | gateways | No | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | policyassignments | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | automationaccounts | Sì (utilizzo del modello) <br/><br/> [Uso della replica geografica](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | No | 
> | automationaccounts/runbooks | No | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | b2cdirectories | No | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | registrations | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | batchaccounts |  Gli account batch non possono essere spostati direttamente da un'area a un'altra, ma è possibile usare un modello per esportare un modello, modificarlo e distribuire il modello nella nuova area. <br/><br/> Informazioni sullo [trasferimento di un account batch tra le aree](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No <br/><br/> Il servizio Azure Batch AI è stato [ritirato](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | No | 
> | jobs | No | 
> | aree di lavoro | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> La rete blockchain non può contenere nodi in aree diverse. 
> | watchers | No | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | blueprintassignments | No | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | redis | No | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | Profili | No | 
> | profiles/endpoints | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | certificateorders | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domainnames | Non è previsto alcun lavoro per i servizi classici.
> | virtualmachines | No | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | networksecuritygroups | Non è previsto alcun lavoro per i servizi classici.
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storageaccounts | Sì |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 
> | Ricerca cognitiva | Supportato con i passaggi manuali.<br/><br/> Informazioni su [come trasferire il servizio ricerca cognitiva di Azure in un'altra area](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | availabilitysets | No | 
> | diskencryptionsets | No | 
> | disks | No | 
> | galleries | No | 
> | galleries/images | No | 
> | galleries/images/versions | No | 
> | hostgroups | No | 
> | hostgroups/hosts | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/versions | No | 
> | snapshots | No | 
> | virtualmachines | Sì | 
> | virtualmachines/extensions | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | registries | No |  
> | registries/buildtasks | No |  
> | registries/replications | No | 
> | registries/tasks | No |  
> | registries/webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | containerservices | No.<br/><br/> Il servizio è stato [ritirato](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | dell'account di integrazione | No |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | jobs | No | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | aree di lavoro | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | catalogs | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | packages | No | 
> | plans | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datafactories | No | 
> | factories | No |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No | 
> | services/projects | No | 
> | slot | No | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../postgresql/howto-move-regions-portal.md)<br/><br/> Se viene eseguito il provisioning del servizio con archiviazione di backup con ridondanza geografica, è possibile usare il ripristino geografico per eseguire il ripristino in altre aree. [Altre informazioni](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servergroups | No | 
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | rollouts | No |  
> | servicetopologies | No | 
> | servicetopologies/services | No |  
> | servicetopologies/services/serviceunits | No | 
> | steps | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | elasticpools | No. La risorsa non è esposta.
> | elasticpools/iothubtenants | No. La risorsa non è esposta.
> | iothubs | Sì. [Scopri di più](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | controllers | No | 
> | Cluster AKS | No<br/><br/> [Altre](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) informazioni sul passaggio a un'altra area.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | labcenters | No | 
> | labs | No | 
> | labs/environments | No |  
> | labs/servicerunners | No | 
> | labs/virtualmachines | No |  
> | schedules | No |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domains | No | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domains | No |  
> | topics | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No |  
> | spazi dei nomi | Sì (con modello)<br/><br/> [Spostare uno spazio dei nomi dell'hub eventi in un'altra area](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | machines | No | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | jobs |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | components |  No |  
> | guestdiagnosticsettings | No | 
> | metricalerts | No | 
> | notificationgroups | No | 
> | notificationrules | No | 
> | scheduledqueryrules |  No | 
> | webtests |  No | 
> | workbooks |  No |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | checknameavailability |  No.<br/><br/> IoT Central funziona con le aree geografiche e non con le aree.
> | graph | No

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> |  iothub |  Sì (clone Hub) <br/><br/> [Clonare un hub Internet delle cose in un'altra area](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | checknameavailability |  No |  
> | graph |  No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hsmpools | No | 
> | insiemi di credenziali |  No | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters |  No |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | labaccounts | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No, si tratta di un servizio globale.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | isolatedenvironments | No | 
> | flussi di lavoro |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | webservices |  No | 
> | aree di lavoro |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 
> | accounts/workspaces | No | 
> | accounts/workspaces/projects | No | 
> | teamaccounts | No | 
> | teamaccounts/workspaces | No | 
> | teamaccounts/workspaces/projects | No | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hostingaccounts | No | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | aree di lavoro | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | userassignedidentities | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account |  No, Azure Maps è un servizio geospaziale. 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | classicdevservices | Nessun lavoro pianificato per i servizi classici 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | mediaservices |  No | 
> | mediaservices/liveevents |  No | 
> | mediaservices/streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | projects | No | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts/capacitypools | No | 
> | netappaccounts/capacitypools/volumes | No | 
> | netappaccounts/capacitypools/volumes/mounttargets | No | 
> | netappaccounts/capacitypools/volumes/snapshots | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | applicationgateways | No | 
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | connections |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutecrossconnections | No | 
> | expressroutegateways | No | 
> | expressrouteports | No | 
> | frontdoors | No | 
> | frontdoorwebapplicationfirewallpolicies | No | 
> | loadbalancers | Sì <br/><br/> È possibile esportare la configurazione esistente come modello e distribuire il modello nella nuova area. Informazioni su come spostare un servizio di bilanciamento del carico [esterno](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) o [interno](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) . |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkintentpolicies |  No | 
> | networkinterfaces | Sì | 
> | networkprofiles | No | 
> | networksecuritygroups | Sì | 
> | networkwatchers |  No |  
> | networkwatchers/connectionmonitors |  No | 
> | networkwatchers/lenti |  No | 
> | networkwatchers/pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones/virtualnetworklinks |  No |  
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Sì<br/><br/> È possibile esportare la configurazione degli indirizzi IP pubblici esistente come modello e distribuire il modello nella nuova area. [Altre](../../virtual-network/move-across-regions-publicip-portal.md) informazioni sullo stato di trasferimento di un indirizzo IP pubblico. |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | serviceendpointpolicies |  No | 
> | trafficmanagerprofiles |  No | 
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (rete WAN virtuale) | No | 
> | vpnsites (rete WAN virtuale) | No | 
> | webapplicationfirewallpolicies |  No | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 
> | namespaces/notificationhubs |  No |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | aree di lavoro |  No | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | managementconfigurations |  No | 
> | Viste |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | peerings | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | dashboards | No | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | rootresources | No | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | capacities |  No | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | insiemi di credenziali | No.<br/><br/> Lo stato di un insieme di credenziali di servizi di ripristino per backup di Azure in aree di Azure non è supportato<br/><br/> Negli insiemi di credenziali dei servizi di ripristino per Azure Site Recovery è possibile [disabilitare e ricreare l'](../../site-recovery/move-vaults-across-regions.md) insieme di credenziali nell'area di destinazione. | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | query |  No |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web |  No | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | flows |  No |  
> | jobcollections |  No | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  No | 
> | playbookconfigurations | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | gateways | No | 
> | nodes | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web | No | 
> | clusters |  No | 
> | clusters/applications | No | 
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | networks | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web |  No | 
> | containergroups | No | 
> | gateways |  No | 
> | networks |  No | 
> | chiavi private |  No | 
> | volumes |  No |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | appliances | No | 
> | applicationdefinitions | No | 
> | scala Web | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | instancepools | No | 
> | managedinstances | Sì | 
> | managedinstances/databases | Sì | 
> | servers | Sì | 
> | servers/databases | Sì | 
> | servers/elasticpools | Sì | 
> | virtualclusters | Sì | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | dwvm | No | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storageaccounts | Sì<br/><br/> [Spostare un account di archiviazione di Azure in un'altra area](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | caches | No | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | managers | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | environments | No | 
> | environments/eventsources | No | 
> | instances | No | 
> | instances/environments | No | 
> | instances/environments/eventsources | No | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | providerregistrations | No | 
> | resources | No | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | environments |  No | 
> | environments/eventsources |  No |  
> | environments/referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | stores | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account |  No | 
> | account/extension |  No | 
> | account/project |  No | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | certificates | No | 
> | connectiongateways |  No |  
> | connections |  No |  
> | customapis |  No | 
> | hostingenvironments | No | 
> | serverfarms |  No |  
> | siti |  No | 
> | sites/premieraddons |  No |  
> | sites/slots |  No |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | applicationgroups | No | 
> | hostpools | No | 
> | aree di lavoro | No | 

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.
