---
title: Servizi supportati da Resource Manager | Microsoft Docs
description: "Descrive i provider di risorse che supportano Gestione risorse, i relativi schemi e le versioni API disponibili, nonché le aree che possono ospitare le risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4f541e34e7c0696e4074613c4ab0734a096c6d12
ms.openlocfilehash: b26007d4e73ae60b808fbd4b6e85de57da4a3a49


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provider, aree, versioni API e schemi di Gestione risorse
Gestione risorse di Azure fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi, supportano Gestione risorse e alcuni servizi supportano Gestione risorse solo parzialmente. In questo argomento è fornito un elenco di provider di risorse supportati per Gestione risorse di Azure.

Durante la distribuzione delle risorse, è anche necessario conoscere quali aree supportano tali risorse e quali versioni dell'API sono disponibili per tali risorse. La sezione [Aree supportate](#supported-regions) illustra come trovare le aree adatte alla propria sottoscrizione e alle proprie risorse. La sezione [Versioni API supportate](#supported-api-versions) illustra come determinare quali versioni dell'API è possibile usare.

Per i servizi supportati nel portale di Azure e nel portale classico, vedere il [grafico di disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/). Per informazioni su servizi che supportano lo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuova](resource-group-move-resources.md).

Nelle tabelle seguenti vengono elencati quali servizi Microsoft supportano la distribuzione e la gestione tramite Resource Manager e quali no. Il collegamento nella colonna **Modelli di avvio rapido** invia una query all'archivio modelli di avvio rapido di Azure per il provider di risorse specificato. I modelli di avvio rapido vengono aggiunti e aggiornati di frequente. La presenza di un collegamento per un particolare servizio non significa necessariamente che la query restituisce modelli dall'archivio. Esistono anche diversi provider di risorse di terze parti che supportano Resource Manager. Nella sezione [Provider e tipi di risorse](#resource-providers-and-types) verrà illustrato come visualizzare tutti i provider di risorse.

## <a name="compute"></a>Calcolo
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Batch |Sì |[Batch REST](https://docs.microsoft.com/rest/api/batchservice/) |[2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Contenitore |Sì |[Container Service REST](https://msdn.microsoft.com/library/azure/mt711470.aspx) |[2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Servizi del ciclo di vita Dynamics |Sì | | | |
| Set di scalabilità |Sì |[Scale Set REST](https://msdn.microsoft.com/library/azure/mt705635.aspx) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Sì |[REST di Service Fabric](https://msdn.microsoft.com/library/azure/dn707692.aspx) | |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Macchine virtuali |Sì |[VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Macchine virtuali (classico) |Limitato |- |- |- |
| RemoteApp |No |- |- |- |
| Servizi cloud (versione classica) |Limitato (vedere sotto) |- |- |- |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md). 

Servizi cloud (classico) può essere usato con altre risorse classiche, tuttavia le risorse classiche non usufruiscono di tutte le funzionalità di Gestione risorse e non sono un'opzione valida per le soluzioni future. Si consiglia di modificare l'infrastruttura dell'applicazione per usare risorse degli spazi dei nomi Microsoft.Compute, Microsoft.Storage e Microsoft.Network.

## <a name="networking"></a>Rete
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Gateway applicazione |Sì |[Application Gateway REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Sì |[REST di DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx) |[2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |Sì |[REST di ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Bilanciamento del carico |Sì |[REST del servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gestione traffico |Sì |[REST di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[01/11/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Reti virtuali |Sì |[REST di Rete virtuale](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gateway VPN |Sì |[Gateway di rete REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Archiviazione
| Service | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| Archiviazione |Sì |[REST di archiviazione](https://msdn.microsoft.com/library/azure/mt163683.aspx) |[Account di archiviazione](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Sì | | | |

## <a name="databases"></a>Database
| Service | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |Sì |[REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) |[08/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Cache Redis |Sì | [Redis Cache REST (REST di Cache Redis)](https://docs.microsoft.com/rest/api/redis/) |[2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Database SQL |Sì |[REST di database SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) |[01/04/2014-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse |Sì | | | |

## <a name="web--mobile"></a>Web e dispositivi mobili
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| App per le API |Sì | [App Service REST (REST del servizio app)](https://docs.microsoft.com/rest/api/appservice/) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[App per le API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestione API |Sì |[REST di gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx) |[2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Sì | | | |
| App per le funzioni |Sì | | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| App per la logica |Sì |[API REST del servizio flusso di lavoro](https://msdn.microsoft.com/library/azure/mt643787.aspx) |[2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| App per dispositivi mobili |Sì | [App Service REST (REST del servizio app)](https://docs.microsoft.com/rest/api/appservice/) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Mobile Engagement |Sì |[REST di Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Search |Sì |[REST Ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| App Web |Sì | [App Service REST (REST del servizio app)](https://docs.microsoft.com/rest/api/appservice/) |[01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Analytics
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Catalogo dati |Sì |[API REST del catalogo dati](https://msdn.microsoft.com/library/azure/mt267595.aspx) |[2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data factory |Sì |[REST di Data factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Analisi Data Lake |Sì | [Data Lake REST (REST di Data Lake)](https://docs.microsoft.com/rest/api/datalakeanalytics/) |[2015-10-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Archivio Data Lake |Sì |[Data Lake Store REST Reference](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[2015-10-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Sì |[REST di HDInsights](https://msdn.microsoft.com/library/azure/mt622197.aspx) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Machine Learning |Sì |[Machine Learning REST](https://msdn.microsoft.com/library/azure/mt767538.aspx) |[2016-05-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Analisi dei flussi |Sì |[REST di analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | |
| Power BI |Sì |[Power BI Embedded REST](https://msdn.microsoft.com/library/azure/mt712303.aspx) |[2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |

## <a name="intelligence"></a>Intelligence
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Servizi cognitivi |Sì | [Cognitive Services REST (REST di Servizi cognitivi)](https://docs.microsoft.com/rest/api/cognitiveservices/) |[2016-02-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |

## <a name="internet-of-things"></a>Internet delle cose
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Hub eventi |Sì |[REST di Hub eventi](https://msdn.microsoft.com/library/azure/dn790674.aspx) |[2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Hub IoT |Sì |[REST hub IoT](https://msdn.microsoft.com/library/azure/mt589014.aspx) |[2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Hub di notifica |Sì |[REST di Hub di notifica](https://msdn.microsoft.com/library/azure/dn495827.aspx) |[01/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Supporti multimediali e CDN
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| RETE CDN |Sì |[REST della rete CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx) |[2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servizi multimediali |Sì |[Riferimento all'API REST di Servizi multimediali di Azure](https://msdn.microsoft.com/library/azure/hh973617.aspx) |[2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) | |

## <a name="hybrid-integration"></a>Integrazione ibrida
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Servizi BizTalk |Sì | |[01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Servizio di ripristino |Sì |[Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx) |[2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus di servizio |Sì |[REST del bus di servizio](https://msdn.microsoft.com/library/azure/mt639375.aspx) |[2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Gestione dell'identità e dell'accesso
Azure Active Directory funziona con Gestione risorse per l'abilitazione del controllo di accesso basato sul ruolo per la sottoscrizione. Per informazioni sull'uso del controllo degli accessi in base al ruolo e su Active Directory vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servizi per gli sviluppatori
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Application Insights |Sì |[Informazioni di riferimento sulle API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx) |[01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Mappe |Sì | | | |
| DevTest Labs |Sì | [DevTest REST (REST di DevTest) ](https://docs.microsoft.com/rest/api/dtl/) |[2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Account di Visual Studio |Sì | |[26/02/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Gestione e sicurezza
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Automazione |Sì |[REST automazione](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Insieme di credenziali di chiave |Sì |[REST insieme di credenziali di chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx) |[Key vault](resource-manager-template-keyvault.md)<br />[Segreto dell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights |Sì | | | |
| Utilità di pianificazione |Sì |[REST dell'utilità di pianificazione](https://msdn.microsoft.com/library/azure/mt629143.aspx) |[2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sicurezza (anteprima) |Sì |[Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Gestione risorse
| Funzionalità | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| Authorization |Sì |[Blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controllo degli accessi in base al ruolo](https://msdn.microsoft.com/library/azure/dn906885.aspx) |[Blocco risorse](resource-manager-template-lock.md)<br />[Assegnazioni di ruoli](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Risorse |Sì |[Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) |[Collegamenti alle risorse](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Provider e tipi di risorse
Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. Queste informazioni possono essere recuperate tramite l'API REST, Azure PowerShell o l'interfaccia della riga di comando di Azure.

Per usare un provider di risorse, il provider deve essere registrato con l'account. Per impostazione predefinita, molti provider di risorse sono registrati automaticamente. Tuttavia, potrebbe essere necessario registrare alcuni provider di risorse manualmente. Nell'esempio seguente viene illustrato come ottenere lo stato della registrazione di un provider di risorse e registrare il provider di risorse, se necessario.

### <a name="portal"></a>di Microsoft Azure
È possibile visualizzare facilmente un elenco di provider di risorse supportati con i passaggi seguenti:

1. Selezionare **Autorizzazioni personali**.
   
    ![Autorizzazioni personali](./media/resource-manager-supported-services/my-permissions.png)
2. Selezionare **Stato provider di risorse**.
   
    ![Stato provider di risorse](./media/resource-manager-supported-services/resource-provider-status.png)
3. Viene visualizzato l'elenco completo di provider di risorse per la sottoscrizione.
   
    ![Elenco di provider di risorse](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API REST
Per ottenere tutti i provider di risorse disponibili, completi di tipi, località, versioni API e stato di registrazione, usare l'operazione [Elencare tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . Per registrare un provider di risorse, vedere [Registrare una sottoscrizione con un provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

### <a name="powershell"></a>PowerShell
L'esempio seguente illustra come ottenere tutti i provider di risorse disponibili.

    Get-AzureRmResourceProvider -ListAvailable


L'esempio seguente illustra come ottenere i tipi di risorse disponibili per un determinato provider di risorse.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

L'output è simile a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

Per registrare un provider di risorse, specificare lo spazio dei nomi:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente illustra come ottenere tutti i provider di risorse disponibili.

    azure provider list

L'output è simile a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

È possibile salvare in un file le informazioni relative a un provider di risorse specifico con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Per registrare un provider di risorse, specificare lo spazio dei nomi:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Aree supportate
Quando si distribuiscono le risorse, in genere è necessario specificare un'area. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa. Queste limitazioni possono essere correlate a problemi per il paese di origine o il risultato di un criterio inserito dall'amministratore della sottoscrizione per utilizzare solo determinate aree. 

Per un elenco completo di tutte le aree supportate per tutti i servizi di Azure, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services), anche se questo elenco potrebbe includere aree non supportate dalla sottoscrizione. È possibile determinare le aree per un particolare tipo di risorsa supportata dalla sottoscrizione tramite il portale, l'API REST, PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="portal"></a>di Microsoft Azure
È possibile visualizzare le aree supportate per un tipo di risorsa seguendo questa procedura:

1. Selezionare **Altri servizi** > **Esplora risorse**.
   
    ![Esplora risorse](./media/resource-manager-supported-services/select-resource-explorer.png)
2. Aprire il nodo **Provider**.
   
    ![Selezionare i provider](./media/resource-manager-supported-services/select-providers.png)
3. Selezionare un provider di risorse e visualizzare le versioni di API e le aree supportate.
   
    ![Visualizzare un provider](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST
Per individuare le aree disponibili per un determinato tipo di risorsa nella sottoscrizione, usare l'operazione [Elenca tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L'esempio seguente mostra come ottenere le aree supportate per i siti Web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

L'output è simile a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente restituisce tutti i percorsi supportati per ogni tipo di risorsa.

    azure location list

È anche possibile filtrare i risultati delle località con un'utilità JSON come [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Che restituisce:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versioni API supportate
Quando si distribuisce un modello, è necessario specificare una versione dell'API da usare per la creazione di ogni risorsa. La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST. Di conseguenza, la versione dell'API specificata nel modello influisce sulle proprietà che è possibile specificare nel modello. In generale, è opportuno selezionare la versione più recente dell'API durante la creazione di modelli. Per i modelli esistenti, è possibile decidere se si desidera continuare a usare una versione precedente dell'API o aggiornare il modello alla versione più recente per sfruttare i vantaggi delle nuove funzionalità.

### <a name="portal"></a>di Microsoft Azure
Le versioni dell'API supportate vengono determinate nello stesso modo in cui si stabiliscono le aree supportate, come illustrato prima.

### <a name="rest-api"></a>API REST
Per scoprire quali versioni API sono disponibili per determinati tipi di risorse, usare l'operazione [Elenca tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L'esempio seguente illustra come ottenere le versioni dell'API disponibili per un determinato tipo di risorse.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

L'output è simile a:

    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile salvare le informazioni (incluse le versioni dell'API disponibili) per un provider di risorse in un file con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

È possibile aprire il file e trovare l'elemento **apiVersions**

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).




<!--HONumber=Nov16_HO3-->


