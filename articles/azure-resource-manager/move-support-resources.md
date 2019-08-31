---
title: Supporto per lo spostamento in base al tipo di risorsa di Azure
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: tomfitz
ms.openlocfilehash: 3100ce897a09c2747c2b339662c699d197ba73d7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194835"
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
> | actionrules | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | server | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | servizio | Sì | Sì |

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
> | automationaccounts/configurazioni | Sì | Sì |
> | automationaccounts/manuali operativi | Yes | Sì |

> [!IMPORTANT]
> Manuali operativi deve esistere nello stesso gruppo di risorse dell'account di automazione.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sì | Sì |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | SqlServerRegistrations | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | registrations | Sì | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | backupvault | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sì | Sì |

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
> | biztalk | Sì | Sì |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Sì | Yes |
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
> | botservices | Sì | Sì |

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
> | profiles | Yes | Yes |
> | profili/endpoint | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sì | Yes |

> [!IMPORTANT]
> Vedere le [indicazioni sullo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | domainnames | Yes | No |
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
> | account | Sì | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | diskencryptionsets | No | No |
> | dischi | Sì | Sì |
> | galleries | No | No |
> | raccolte/immagini | No | No |
> | raccolte/immagini/versioni | No | No |
> | gruppi host | No | No |
> | gruppi host/host | No | No |
> | immagini | Yes | Sì |
> | proximityplacementgroups | No | No |
> | restorepointcollections | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/versioni | No | No |
> | snapshot | Sì | Yes |
> | virtualmachines | Yes | Sì |
> | VirtualMachines/estensioni | Sì | Sì |
> | virtualmachinescalesets | Sì | Yes |

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
> | registries | Sì | Sì |
> | registri/BuildTasks | Sì | Sì |
> | registri/repliche | Yes | Yes |
> | registri/attività | Sì | Yes |
> | registri/webhook | Sì | Sì |

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
> | connettori | Sì | Sì |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hubs | Yes | Sì |

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
> | catalogs | Yes | Yes |
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
> | datafactories | Sì | Yes |
> | factories | Yes | Sì |

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
> | account | Yes | Yes |

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
> | server | Sì | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | server | Yes | Sì |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | servergroups | No | No |
> | server | Yes | Yes |
> | serversv2 | Sì | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | artifactsources | Yes | Sì |
> | rollouts | Yes | Sì |
> | servicetopologies | Yes | Yes |
> | servicetopologies/servizi | Yes | Sì |
> | servicetopologies/Services/serviceunits | Yes | Sì |
> | steps | Yes | Sì |

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
> | controllers | Yes | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | labs | Yes | No |
> | Lab/ambienti | Sì | Yes |
> | Lab/servicerunners | Sì | Sì |
> | Lab/VirtualMachines | Sì | No |
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
> | domains | Yes | Yes |

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
> | topics | Sì | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |
> | spazi dei nomi | Yes | Sì |

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
> | sapmonitors | Sì | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Sì |

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | services | Yes | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | computer | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | datamanagers | Yes | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | processi | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | actiongroups | Sì | Yes |
> | activitylogalerts | No | No |
> | alertrules | Sì | Sì |
> | autoscalesettings | Sì | Yes |
> | components | Sì | Yes |
> | guestdiagnosticsettings | No | No |
> | metricalerts | No | No |
> | notificationgroups | No | No |
> | notificationrules | No | No |
> | scheduledqueryrules | Sì | Yes |
> | webtests | Sì | Yes |
> | cartelle di lavoro | Yes | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | iotapps | Sì | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Yes | Yes |
> | graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | hsmpools | No | No |
> | insiemi di credenziali | Yes | Yes |

> [!IMPORTANT]
> Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o tra sottoscrizioni.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | clusters | Yes | Yes |

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
> | integrationaccounts | Sì | Yes |
> | integrationserviceenvironments | No | No |
> | isolatedenvironments | No | No |
> | flussi di lavoro | Yes | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Sì | Yes |
> | webservices | Sì | No |
> | aree di lavoro | Sì | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Yes | Yes |

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
> | account | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | mediaservices | Yes | Yes |
> | MediaServices/liveevents | Sì | Sì |
> | MediaServices/le entità streamingendpoint | Yes | Yes |

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
> | applicationsecuritygroups | Sì | Sì |
> | azurefirewalls | Sì | Sì |
> | bastionhosts | No | No |
> | connessioni | Yes | Sì |
> | ddoscustompolicies | Sì | Sì |
> | ddosprotectionplans | No | No |
> | dnszones | Sì | Sì |
> | expressroutecircuits | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutegateways | No | No |
> | expressrouteports | No | No |
> | frontdoor | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | loadbalancers | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
> | localnetworkgateways | Sì | Yes |
> | natgateways | Sì | Sì |
> | networkintentpolicies | Sì | Yes |
> | networkinterfaces | Yes | Sì |
> | networkprofiles | No | No |
> | networksecuritygroups | Sì | Yes |
> | networkwatchers | Sì | Yes |
> | networkwatchers / connectionmonitors | Yes | Yes |
> | networkwatchers/lenti | Sì | Sì |
> | networkwatchers / pingmeshes | Sì | Sì |
> | p2svpngateways | No | No |
> | privatednszones | Sì | Yes |
> | privatednszones / virtualnetworklinks | Yes | Sì |
> | privateendpoints | No | No |
> | privatelinkservices | No | No |
> | publicipaddresses | Sì, SKU Basic<br>SKU senza standard | Sì, SKU Basic<br>SKU senza standard |
> | publicipprefixes | Sì | Yes |
> | routefilters | No | No |
> | routetables | Sì | Yes |
> | securegateways | Sì | Sì |
> | serviceendpointpolicies | Sì | Sì |
> | trafficmanagerprofiles | Sì | Yes |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Sì | Sì |
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
> | spazi dei nomi | Yes | Sì |
> | spazi dei nomi/notificationhubs | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | Yes | Sì |

> [!IMPORTANT]
> Verificare che il passaggio alla nuova sottoscrizione non superi le [quote di sottoscrizione](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Sì | Sì |
> | solutions | Sì | Yes |
> | visualizzazioni | Sì | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | peering | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dashboards | Sì | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | capacities | Sì | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | insiemi di credenziali | Sì | Yes |

> [!IMPORTANT]
> Vedere [linee guida per lo spostamento dei servizi di ripristino](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | query | Sì | Sì |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | Sì | No |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | flussi | Yes | Yes |
> | jobcollections | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | searchservices | Sì | Sì |

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Sì | Sì |
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
> | spazi dei nomi | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | applicazioni | No | No |
> | clusters | Yes | Sì |
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
> | gateways | Yes | Sì |
> | Reti | Sì | Sì |
> | segreti | Yes | Yes |
> | volumes | Yes | Sì |

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
> | server | Yes | Sì |
> | server/database | Yes | Yes |
> | Server/elasticpools | Sì | Yes |
> | virtualclusters | Sì | Sì |

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Sì | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Sì |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sì | Sì |

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
> | streamingjobs | Yes | Yes |

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
> | ambienti/referencedatasets | Yes | Sì |

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
> | account/progetto | Sì | Yes |

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Sì | Sì |
> | dedicatedcloudservices | Sì | Sì |
> | virtualmachines | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Gruppo di risorse | Sottoscrizione |
> | ------------- | ----------- | ---------- |
> | certificati | No | Sì |
> | connectiongateways | Yes | Sì |
> | connessioni | Sì | Sì |
> | customapis | Sì | Yes |
> | hostingenvironments | No | No |
> | serverfarms | Sì | Sì |
> | siti | Yes | Yes |
> | siti/premieraddons | Yes | Yes |
> | siti/slot | Sì | Yes |

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
