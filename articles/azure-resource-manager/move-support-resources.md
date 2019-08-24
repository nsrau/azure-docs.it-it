---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/23/2019
ms.author: tomfitz
ms.openlocfilehash: a588d4e4b1978b572488dcd5d53698cbb8f7d311
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997280"
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
> - [Microsoft. AzureData](#microsoftazuredata)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
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

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | domainservices | No | No |
> | DomainServices/ReplicaSets | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | tenants | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | actionrules | Sì | Sì |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | server | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | servizio | Sì | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | configurationstores | Yes | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentities | No | No |
> | gateways | No | No |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | policyassignments | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sì | Sì |
> | automationaccounts/configurazioni | Yes | Yes |
> | automationaccounts/manuali operativi | Sì | Yes |

> [!IMPORTANT]
> Manuali operativi deve esistere nello stesso gruppo di risorse dell'account di automazione.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sì | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | SqlServerRegistrations | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | registrations | Sì | Sì |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | backupvault | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Sì |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | fileservers | No | No |
> | processi | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | biztalk | Yes | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Yes | Yes |
> | visualizzazioni | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | botservices | Sì | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | redis | Sì | Sì |

> [!IMPORTANT]
> Se la cache di Azure per l'istanza di redis è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Vedere [limitazioni dello spostamento di rete](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | No | No |
> | profiles | Sì | Sì |
> | profili/endpoint | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sì | Sì |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | domainnames | Sì | No |
> | virtualmachines | Sì | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | No |

> [!IMPORTANT]
> Vedere [linee guida di spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | Yes | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sì | Yes |
> | diskencryptionsets | No | No |
> | dischi | Yes | Sì |
> | galleries | No | No |
> | raccolte/immagini | No | No |
> | raccolte/immagini/versioni | No | No |
> | gruppi host | No | No |
> | gruppi host/host | No | No |
> | immagini | Sì | Sì |
> | proximityplacementgroups | No | No |
> | restorepointcollections | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/versioni | No | No |
> | snapshot | Yes | Sì |
> | virtualmachines | Sì | Sì |
> | VirtualMachines/estensioni | Sì | Sì |
> | virtualmachinescalesets | Sì | Sì |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento di macchine virtuali](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | registries | Sì | Yes |
> | registri/BuildTasks | Sì | Sì |
> | registri/repliche | Yes | Sì |
> | registri/attività | Sì | Yes |
> | registri/webhook | Sì | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | containerservices | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | Sì | Sì |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | connettori | Yes | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hubs | Sì | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | processi | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | catalogs | Sì | Sì |
> | datacatalogs | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | packages | No | No |
> | plans | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | datafactories | Yes | Sì |
> | factories | Sì | Sì |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | Yes | Sì |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | services | No | No |
> | Servizi/progetti | No | No |
> | slot | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | server | Yes | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | server | Sì | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | servergroups | No | No |
> | server | Yes | Yes |
> | serversv2 | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sì | Sì |
> | rollouts | Yes | Sì |
> | servicetopologies | Sì | Sì |
> | servicetopologies/servizi | Sì | Sì |
> | servicetopologies/Services/serviceunits | Yes | Sì |
> | steps | Sì | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | elasticpools | No | No |
> | elasticpools / iothubtenants | No | No |
> | iothubs | Sì | Sì |
> | provisioningservices | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | controllers | Sì | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | labs | Yes | No |
> | Lab/ambienti | Sì | Yes |
> | Lab/servicerunners | Sì | Sì |
> | Lab/VirtualMachines | Yes | No |
> | schedules | Sì | Yes |

## <a name="microsoftdns"></a>microsoft.dns

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dnszones | No | No |
> | dnszones/a | No | No |
> | dnszones/aaaa | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/PTR | No | No |
> | dnszones/SRV | No | No |
> | dnszones/txt | No | No |
> | trafficmanagerprofiles | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | domains | Yes | Sì |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | services | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | domains | Sì | Sì |
> | topics | Sì | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | sapmonitors | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | services | Sì | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | computer | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | processi | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | actiongroups | Yes | Sì |
> | activitylogalerts | No | No |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | components | Sì | Yes |
> | guestdiagnosticsettings | No | No |
> | metricalerts | No | No |
> | notificationgroups | No | No |
> | notificationrules | No | No |
> | scheduledqueryrules | Sì | Sì |
> | webtests | Yes | Sì |
> | cartelle di lavoro | Sì | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sì | Sì |
> | graph | Sì | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hsmpools | No | No |
> | insiemi di credenziali | Sì | Sì |

> [!IMPORTANT]
> Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o tra sottoscrizioni.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | Sì | Sì |
> | integrationserviceenvironments | No | No |
> | isolatedenvironments | No | No |
> | flussi di lavoro | Yes | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Sì | Yes |
> | webservices | Sì | No |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Sì | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | account/aree di lavoro | No | No |
> | account/aree di lavoro/progetti | No | No |
> | teamaccounts | No | No |
> | teamaccounts/aree di lavoro | No | No |
> | teamaccounts/aree di lavoro/progetti | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sì | Sì |
> | MediaServices/liveevents | Sì | Sì |
> | MediaServices/le entità streamingendpoint | Sì | Sì |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | No | No |
> | migrateprojects | No | No |
> | projects | No | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts / capacitypools | No | No |
> | netappaccounts/capacitypools/volumi | No | No |
> | netappaccounts/capacitypools/Volumes/mounttargets | No | No |
> | netappaccounts/capacitypools/volumi/snapshot | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | Sì | Yes |
> | azurefirewalls | Sì | Sì |
> | bastionhosts | No | No |
> | connessioni | Yes | Sì |
> | ddoscustompolicies | Sì | Yes |
> | ddosprotectionplans | No | No |
> | dnszones | Sì | Yes |
> | expressroutecircuits | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutegateways | No | No |
> | expressrouteports | No | No |
> | frontdoor | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | loadbalancers | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
> | localnetworkgateways | Yes | Yes |
> | natgateways | Sì | Sì |
> | networkintentpolicies | Yes | Yes |
> | networkinterfaces | Yes | Sì |
> | networkprofiles | No | No |
> | networksecuritygroups | Yes | Sì |
> | networkwatchers | Yes | Sì |
> | networkwatchers / connectionmonitors | Yes | Sì |
> | networkwatchers/lenti | Yes | Yes |
> | networkwatchers / pingmeshes | Sì | Sì |
> | p2svpngateways | No | No |
> | privatednszones | Sì | Yes |
> | privatednszones / virtualnetworklinks | Yes | Sì |
> | privateendpoints | No | No |
> | privatelinkservices | No | No |
> | publicipaddresses | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
> | publicipprefixes | Sì | Sì |
> | routefilters | No | No |
> | routetables | Sì | Sì |
> | securegateways | Sì | Sì |
> | serviceendpointpolicies | Sì | Sì |
> | trafficmanagerprofiles | Sì | Yes |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Yes | Sì |
> | virtualnetworks | Sì | Sì |
> | virtualnetworktaps | No | No |
> | virtualwans | No | No |
> | vpngateways (WAN virtuale) | No | No |
> | vpnsites (WAN virtuale) | No | No |
> | webapplicationfirewallpolicies | Yes | Sì |

> [!IMPORTANT]
> Vedere [linee guida](./move-limitations/networking-move-limitations.md)per lo spostamento di rete.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Yes | Yes |
> | spazi dei nomi/notificationhubs | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | Sì | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Sì | Sì |
> | solutions | Sì | Sì |
> | visualizzazioni | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | peering | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dashboards | Sì | Sì |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | capacities | Yes | Sì |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | insiemi di credenziali | Yes | Sì |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | query | Sì | Sì |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | Yes | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | flussi | Sì | Sì |
> | jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | searchservices | Yes | Sì |

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Sì | Yes |
> | playbookconfigurations | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | gateways | No | No |
> | nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | No | No |
> | clusters | Sì | Sì |
> | cluster/applicazioni | No | No |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | Reti | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | Sì | Sì |
> | containergroups | No | No |
> | gateways | Sì | Sì |
> | Reti | Sì | Sì |
> | segreti | Sì | Sì |
> | volumes | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | signalr | Sì | Sì |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | No | No |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | No | No |
> | appliances | No | No |
> | applicationdefinitions | No | No |
> | applicazioni | No | No |
> | jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | instancepools | No | No |
> | managedinstances | No | No |
> | ManagedInstances/database | No | No |
> | server | Sì | Sì |
> | server/database | Sì | Sì |
> | Server/elasticpools | Yes | Sì |
> | virtualclusters | Sì | Sì |

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sì | Yes |
> | sqlvirtualmachines | Sì | Sì |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sì | Sì |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sì | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | managers | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sì | Sì |

> [!IMPORTANT]
> I processi di analisi di flusso non possono essere spostati in stato di esecuzione.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | environments | No | No |
> | ambienti/EventSources | No | No |
> | istanze | No | No |
> | istanze/ambienti | No | No |
> | istanze/ambienti/EventSources | No | No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | providerregistrations | No | No |
> | risorse | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | environments | Sì | Sì |
> | ambienti/EventSources | Sì | Sì |
> | ambienti/referencedatasets | Sì | Sì |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | Archivia | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | Yes | Sì |
> | account/estensione | Sì | Sì |
> | account/progetto | Yes | Yes |

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Sì | Sì |
> | dedicatedcloudservices | Sì | Sì |
> | virtualmachines | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | certificati | No | Sì |
> | connectiongateways | Sì | Yes |
> | connessioni | Sì | Sì |
> | customapis | Yes | Sì |
> | hostingenvironments | No | No |
> | serverfarms | Sì | Sì |
> | siti | Sì | Sì |
> | siti/premieraddons | Sì | Yes |
> | siti/slot | Sì | Sì |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicationgroups | No | No |
> | hostpools | No | No |
> | aree di lavoro | No | No |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
