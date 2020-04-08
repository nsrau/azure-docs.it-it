---
title: Spostare il supporto dell'operazione in base al tipo di risorsaMove operation support by resource type
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse o una sottoscrizione.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804793"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse
Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Fornisce inoltre informazioni sulle condizioni speciali da considerare quando si sposta una risorsa.

Passare a uno spazio dei nomi del provider di risorse:Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration (Configurazione applicazioni)](#microsoftappconfiguration)
> - [Microsoft.AppPlatform (Piattaforma App)](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork (rete classica)](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumo](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders (informazioni in base ai provider personalizzati)](#microsoftcustomproviders)
> - [Informazioni su 200](#microsoftdatabox)
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
> - [Microsoft.DataProtection (Protezione dati)](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization (applicazione di virtualizzazione)](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps (informazioni in base al sistema operativo Microsoft.Dev](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.Manutenzione](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring (prodotti Microsoft.Microservices4Spring)](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Informazioni su Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Servizi](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan (piano)](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql (informazioni in lingua inglese)](#microsoftsql)
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
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support (informazioni in base al supporto tecnico)](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token (informazioni in linguaggio Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline (informazioni in stati di base)](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Informazioni su Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurazioni | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Sì | Sì |
> | alerts | No | No |
> | riepilogo degli avvisi | No | No |
> | smartdetectoralertrules | Sì | Sì |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | service | Sì | Sì |

> [!IMPORTANT]
> Non è possibile spostare un servizio Gestione API impostato per lo SKU di consumo.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration (Configurazione applicazioni)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Sì | Sì |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform (Piattaforma App)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Primavera | Sì | Sì |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentities | No | No |
> | gateways | No | No |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Checkaccess | No | No |
> | negare assegnazioni | No | No |
> | trovareorphanroleassegnazioni | No | No |
> | locks | No | No |
> | autorizzazioni | No | No |
> | policyassignments | No | No |
> | Policydefinitions | No | No |
> | definizioni di policyset | No | No |
> | assegnazioni di ruolo | No | No |
> | roleassignmentsusagemetrics | No | No |
> | definizioni di ruolo | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sì | Sì |
> | automationaccounts / configurazioni | Sì | Sì |
> | automationaccounts / runbook | Sì | Sì |

> [!IMPORTANT]
> I runbook devono esistere nello stesso gruppo di risorse dell'account di automazione.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sì | Sì |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | No | No |
> | postgresinstances | No | No |
> | sqlia | No | No |
> | Sqlserverregistrations | Sì | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Sì | Sì |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sì | Sì |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | fileservers | No | No |
> | jobs | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | periodi di fatturazione | No | No |
> | fatturazioneautorizzazioni | No | No |
> | fatturazioneroleassegnazioni | No | No |
> | billingroledefinitions (definizioni di fatturazione) | No | No |
> | crearebillingroleassegnazione | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | No | No |
> | cordamembri | No | No |
> | Osservatori | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tokenservices (servizi) | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |
> | blueprints | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Sì | Sì |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Sì | Sì |

> [!IMPORTANT]
> Se l'istanza Cache di Azure per Redis è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Consultate [Limitazioni dello spostamento della rete.](./move-limitations/networking-move-limitations.md)

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Sì | Sì |
> | Profili | Sì | Sì |
> | profili/endpoint | Sì | Sì |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sì | Sì |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Sì | No |
> | virtualmachines | Sì | No |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sì | No |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse di distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica per tale scenario.

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sinteticiaccount | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sì | Sì |
> | diskencryptionsets (crittografia di disco) | No | No |
> | disks | Sì | Sì |
> | galleries | No | No |
> | gallerie / immagini | No | No |
> | gallerie / immagini / versioni | No | No |
> | gruppi host | No | No |
> | hostgruppi/ host | No | No |
> | images | Sì | Sì |
> | proximityplacementgroups | Sì | Sì |
> | restorepointcollections | No | No |
> | sharedvmextensions | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages / versioni | No | No |
> | snapshots | Sì | Sì |
> | sshpublickeys | No | No |
> | virtualmachines | Sì | Sì |
> | macchine virtuali / estensioni | Sì | Sì |
> | virtualmachinescalesets | Sì | Sì |

> [!IMPORTANT]
> Vedere [Macchine virtuali per spostare le linee guida.](./move-limitations/virtual-machines-move-limitations.md)

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatocosto | No | No |
> | Saldi | No | No |
> | Budget | No | No |
> | Spese | No | No |
> | costtag | No | No |
> | credits | No | No |
> | eventi | No | No |
> | Previsioni | No | No |
> | lots | No | No |
> | Mercati | No | No |
> | operazionerisultati | No | No |
> | operationstatus (statooperazione) | No | No |
> | listini prezzi | No | No |
> | products | No | No |
> | prenotazionedettagli | No | No |
> | prenotazioniraccomandazioni | No | No |
> | reservationsummaries | No | No |
> | prenotazionitransazioni | No | No |
> | tags | No | No |
> | tenants | No | No |
> | terms | No | No |
> | usagedetails | No | No |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |
> | serviceassociationlinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Sì | Sì |
> | registri / agentpools | No | No |
> | registri / buildtasks | Sì | Sì |
> | registri / repliche | Sì | Sì |
> | registri / taskruns | Sì | Sì |
> | registri / attività | Sì | Sì |
> | registri / webhooks | Sì | Sì |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alerts | No | No |
> | Budget | No | No |
> | dell'account di integrazione | Sì | Sì |
> | dimensioni | No | No |
> | esportazioni | No | No |
> | sottoscrizioni esterne | No | No |
> | forecast | No | No |
> | query | No | No |
> | reportconfigs | No | No |
> | reports | No | No |
> | showbackrules | No | No |
> | Viste | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders (informazioni in base ai provider personalizzati)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associazioni | No | No |
> | fornitori di risorse | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Sì | Sì |
> | datacatalogs | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | No | No |
> | plans | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Sì | Sì |
> | factories | Sì | Sì |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | No | No |
> | servizi/ progetti | No | No |
> | slot | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection (Protezione dati)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sì | Sì |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sì | Sì |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | No | No |
> | servers | Sì | Sì |
> | serverv2 (serverv2) | Sì | Sì |
> | singoli server | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sì | Sì |
> | rollouts | Sì | Sì |
> | servicetopologies | Sì | Sì |
> | servicetopologies / servizi | Sì | Sì |
> | servicetopologies / servizi / unità di servizio | Sì | Sì |
> | steps | Sì | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization (applicazione di virtualizzazione)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups (gruppi di applicazioni) | No | No |
> | hostpools | No | No |
> | aree di lavoro | No | No |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | No | No |
> | elasticpools / iothubtenant | No | No |
> | iothubs | Sì | Sì |
> | provisioningservices | Sì | Sì |

## <a name="microsoftdevops"></a>Microsoft.DevOps (informazioni in base al sistema operativo Microsoft.Dev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipeline | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | labs | Sì | No |
> | laboratori / ambienti | Sì | Sì |
> | laboratori / servicerunners | Sì | Sì |
> | laboratori / macchine virtuali | Sì | No |
> | schedules | Sì | Sì |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaliie gemelle | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Sì | Sì |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sì | Sì |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sì | Sì |
> | eventSubscriptions | No- non può essere spostato in modo indipendente, ma automaticamente con la risorsa sottoscritta. | No- non può essere spostato in modo indipendente, ma automaticamente con la risorsa sottoscritta. |
> | eventsubscriptions (sottoscrizioni di eventi) | No- non può essere spostato in modo indipendente, ma automaticamente con la risorsa sottoscritta. | No- non può essere spostato in modo indipendente, ma automaticamente con la risorsa sottoscritta. |
> | estensioneargomenti | No | No |
> | partnernamespaces | Sì | Sì |
> | registrazioni dei partner | No | No |
> | partnertopics | Sì | Sì |
> | sistemaargomentii | Sì | Sì |
> | topics | Sì | Sì |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | esperimentieal di lavoro | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts (account gestiti automaticamente) | No | No |
> | automanagedvmconfigurationprofiles | No | No |
> | guestconfigurationassegnazioni | No | No |
> | software | No | No |
> | softwareupdateprofile (aggiornamento software) | No | No |
> | softwareaggiornamenti | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | sapmonitors | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sì | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Macchine | Sì | Sì |
> | macchine / estensioni | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sì | Sì |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | No | No |
> | ambiti di rete | No | No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sì | Sì |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Sì | Sì |
> | activitylogalerts | No | No |
> | alertrules | Sì | Sì |
> | autoscalesettings | Sì | Sì |
> | baseline | No | No |
> | calculatebaseline | No | No |
> | components | Sì | Sì |
> | regole di raccolta dati | No | No |
> | diagnosticiimpostazioni | No | No |
> | diagnosticiimpostazionicategorie | No | No |
> | eventtypes | No | No |
> | extendeddiagnosticsettings (impostazioni diagnostiche estese) | No | No |
> | guestdiagnosticsettings | No | No |
> | logdefinitions | No | No |
> | log | No | No |
> | metricalerts | No | No |
> | metrichedi base | No | No |
> | metriche | No | No |
> | metricnamespaces | No | No |
> | Metriche | No | No |
> | myworkbooks | No | No |
> | notificationgroups | No | No |
> | privatelinkscopes | Sì | Sì |
> | scheduledqueryrules | Sì | Sì |
> | Topologia | No | No |
> | transazioni | No | No |
> | vminsightsonboardingstatuses | No | No |
> | webtests | Sì | Sì |
> | workbooks | Sì | Sì |
> | workbooktemplates (modelli di cartella di lavoro | Sì | Sì |

> [!IMPORTANT]
> Assicurarsi che lo spostamento a una nuova sottoscrizione non [superi le quote di sottoscrizione.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Sì | Sì |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sì | Sì |
> | graph | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | No | No |
> | insiemi di credenziali | Sì | Sì |

> [!IMPORTANT]
> Gli archivi delle chiavi usati per la crittografia del disco non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o tra sottoscrizioni.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters (cluster connessi) | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sì | Sì |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | Sì | Sì |
> | integrationserviceenvironments | Sì | No |
> | integrazioneambienti di servizio / managedapis | Sì | No |
> | isolatedenvironments | No | No |
> | flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | No | No |
> | webservices | Sì | No |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | account/ spazi di lavoro | No | No |
> | account / spazi di lavoro / progetti | No | No |
> | teamaccounts | No | No |
> | account di squadra/ aree di lavoro | No | No |
> | account di squadra / spazi di lavoro / progetti | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |
> | aree di lavoro/ calcola | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Manutenzione

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicareaggiornamenti | No | No |
> | configurationassegnazioni | No | No |
> | manutenzioneconfigurazioni | Sì | Sì |
> | aggiornamenti | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identità | No | No |
> | userassignedidentities | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | No | No |
> | managednetworks / managednetworkgroups | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No |
> | notifica | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrazioneassegnazioni | No | No |
> | registrazionidefinizioni | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |
> | conti / privateatlases | Sì | Sì |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sì | Sì |
> | mediaservices / liveevents | Sì | Sì |
> | mediaservices / streamingendpoints | Sì | Sì |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring (prodotti Microsoft.Microservices4Spring)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Sì | Sì |
> | migrateprojects | Sì | Sì |
> | movecollections | No | No |
> | projects | No | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts / backuppolicies | No | No |
> | netappaccounts / capacitypools | No | No |
> | netappaccounts / capacitypools / volumi | No | No |
> | netappaccounts / capacitypools / volumi / montaobiettivi | No | No |
> | netappaccounts / capacitypools / volumi / istantanee | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | Sì | Sì |
> | azurefirewalls | Sì | Sì |
> | bastionhosts | No | No |
> | connections | Sì | Sì |
> | ddoscustompolicies | Sì | Sì |
> | ddosprotectionplans | No | No |
> | dnszones | Sì | Sì |
> | expressroutecircuits | No | No |
> | expressroutegateways | No | No |
> | criteri firewall | Sì | Sì |
> | frontdoors | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | indirizzi ipgroup | Sì | Sì |
> | loadbalancers | Sì - SKU di base<br>No - Standard SKU | Sì - SKU di base<br>No - Standard SKU |
> | localnetworkgateways | Sì | Sì |
> | natgateways | Sì | Sì |
> | networkexperimentprofiles | Sì | Sì |
> | networkintentpolicies | Sì | Sì |
> | networkinterfaces | Sì | Sì |
> | networkprofiles | No | No |
> | networksecuritygroups | Sì | Sì |
> | networkwatchers | Sì | No |
> | osservatori di rete / connectionmonitors | Sì | No |
> | osservatori di rete / flowlog | Sì | No |
> | networkwatchers / pingmeshes | Sì | No |
> | p2svpngateways | No | No |
> | privatednszones | Sì | Sì |
> | privatednszones / virtualnetworklinks | Sì | Sì |
> | privateendpointredirectmaps | No | No |
> | endpoint privati | Sì | Sì |
> | privatelinkservices | No | No |
> | publicipaddresses | Sì - SKU di base<br>No - Standard SKU | Sì - SKU di base<br>No - Standard SKU |
> | publicipprefixes | Sì | Sì |
> | routefilters | No | No |
> | routetables | Sì | Sì |
> | serviceendpointpolicies | Sì | Sì |
> | trafficmanagerprofiles | Sì | Sì |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Sì | Sì |
> | virtualnetworks | Sì | Sì |
> | virtualnetworktaps | No | No |
> | virtualrouters | Sì | Sì |
> | virtualwans | No | No |
> | vpngateway (WAN virtuale) | No | No |
> | vpnserverconfigurations | No | No |
> | vpnsites (WAN virtuale) | No | No |
> | webapplicationfirewallpolicies | Sì | Sì |

> [!IMPORTANT]
> Consultate Linee guida per [lo spostamento della rete](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Sì |
> | spazi dei nomi / notificationhub | Sì | Sì |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespace | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | No | No |
> | storageinsightconfigs | No | No |
> | aree di lavoro | Sì | Sì |

> [!IMPORTANT]
> Assicurarsi che lo spostamento a una nuova sottoscrizione non [superi le quote di sottoscrizione.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | No | No |
> | managementconfigurations | Sì | Sì |
> | solutions | Sì | Sì |
> | Viste | Sì | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | peerings | Sì | Sì |
> | servizi di peering | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents (eventi di policy) | No | No |
> | Stati politici | No | No |
> | policytrackedrisorse | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Sì | Sì |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sì | Sì |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems (elementi protetto per il backup) | No | No |
> | replicationeligibilityresults | No | No |
> | insiemi di credenziali | Sì | Sì |

> [!IMPORTANT]
> Vedere Linee guida per lo spostamento di [Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | No | No |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | query | Sì | Sì |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | disponibilitàstati | No | No |
> | childavailabilitystatuses | No | No |
> | risorse per l'infanzia | No | No |
> | eventi | No | No |
> | Notifiche | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts (script di distribuzione) | No | No |
> | collegamenti | No | No |
> | tags | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | Sì | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Sì | Sì |

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adadadadaptivenetworkhardenings | No | No |
> | advancedthreatprotezioneimpostazioni | No | No |
> | valutazionemetadati | No | No |
> | valutazioni | No | No |
> | Automazioni | Sì | Sì |
> | compliancerisultati | No | No |
> | Conformità | No | No |
> | datacollectionagents | No | No |
> | devicesecuritygroups | No | No |
> | informazionipolitiche di protezione | No | No |
> | iotsecuritysolutions | Sì | Sì |
> | servervulnerabilityvalutazioni | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | No | No |
> | alertrules | No | No |
> | alertruletemplates | No | No |
> | segnalibri | No | No |
> | cases | No | No |
> | connettori dati | No | No |
> | Dataconnectorscheckrequirements | No | No |
> | entities | No | No |
> | entityqueries (query di entità) | No | No |
> | Incidenti | No | No |
> | ufficiocon sensi di consensi | No | No |
> | Scheda Impostazioni | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | No | No |
> | nodes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | No | No |
> | clusters | Sì | Sì |
> | cluster/applicazioni | No | No |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | managedclusters | No | No |
> | networks | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | scala Web | Sì | Sì |
> | containergroups | No | No |
> | gateways | Sì | Sì |
> | networks | Sì | Sì |
> | chiavi private | Sì | Sì |
> | volumes | Sì | Sì |

## <a name="microsoftservices"></a>Microsoft.Servizi

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Sì | Sì |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan (piano)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ibridousebenefici | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | No | No |
> | scala Web | No | No |
> | jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pool di istanze | No | No |
> | managedinstances | No | No |
> | managedinstances / database | No | No |
> | servers | Sì | Sì |
> | server / database | Sì | Sì |
> | server / elasticpools | Sì | Sì |
> | server/jobaccounts | Sì | Sì |
> | server / jobagent | Sì | Sì |
> | virtualclusters | Sì | Sì |

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica al database SQL di Azure e ai database di Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sì | Sì |
> | sqlvirtualmachines | Sì | Sì |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | No | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sì | Sì |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Cache | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sì | Sì |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sì | Sì |

> [!IMPORTANT]
> I processi di Analisi di flusso non possono essere spostati in stato di esecuzione.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | No | No |
> | ambienti/origini eventi | No | No |
> | instances | No | No |
> | istanze / ambienti | No | No |
> | istanze /ambienti /origini eventi | No | No |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | createsubscription (sottoscrizione) | No | No |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aree di lavoro | No | No |
> | aree di lavoro / bigdatapools | No | No |
> | aree di lavoro / sqlpool | No | No |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | No | No |
> | resources | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Sì | Sì |
> | ambienti/origini eventi | Sì | Sì |
> | ambienti / referencedatasets | Sì | Sì |

## <a name="microsofttoken"></a>Microsoft.Token (informazioni in linguaggio Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | archivi | Sì | Sì |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | conto / estensione | Sì | Sì |
> | conto / progetto | Sì | Sì |

> [!IMPORTANT]
> Per modificare la sottoscrizione per DevOps di Azure, vedere Modificare la sottoscrizione di [Azure usata per la fatturazione.](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatonosacloud | No | No |
> | dedicatocloudservices | No | No |
> | virtualmachines | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | No | No |
> | Vnfs (in vnfs) | No | No |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline (informazioni in stati di base)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Sì | Sì |
> | plans | Sì | Sì |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | No | Sì |
> | connectiongateways | Sì | Sì |
> | connections | Sì | Sì |
> | customapis | Sì | Sì |
> | hostingenvironments | No | No |
> | kubeenvironments | Sì | Sì |
> | serverfarms | Sì | Sì |
> | siti | Sì | Sì |
> | siti / premieraddons | Sì | Sì |
> | siti / slot | Sì | Sì |
> | siti statici | No | No |

> [!IMPORTANT]
> Vedere Linee guida per lo [spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Informazioni su Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | chiavi multipleattivazione | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | No | No |
> | monitorii | No | No |
> | monitors | No | No |
> | notificationsettings (impostazioni di notifica) | No | No |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi
Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
