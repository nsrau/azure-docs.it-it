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
ms.date: 12/27/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 7fa1be008b5be0c912af14674ffb38d99dcf6bcc
ms.openlocfilehash: 4042810a5ccfffba74ae9cffd82f194e8058ad83


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provider, aree, versioni API e schemi di Gestione risorse
Gestione risorse di Azure fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi, supportano Gestione risorse e alcuni servizi supportano Gestione risorse solo parzialmente. In questo argomento è fornito un elenco di provider di risorse supportati per Gestione risorse di Azure.

Durante la distribuzione delle risorse, è anche necessario conoscere quali aree supportano tali risorse e quali versioni dell'API sono disponibili per tali risorse. La sezione [Aree supportate](#supported-regions) illustra come trovare le aree adatte alla propria sottoscrizione e alle proprie risorse. La sezione [Versioni API supportate](#supported-api-versions) illustra come determinare quali versioni dell'API è possibile usare.

Per i servizi supportati nel portale di Azure e nel portale classico, vedere il [grafico di disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/). Per informazioni su servizi che supportano lo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuova](resource-group-move-resources.md).

Nelle tabelle seguenti vengono elencati quali servizi Microsoft supportano la distribuzione e la gestione tramite Resource Manager e quali no. Il collegamento nella colonna **Modelli di avvio rapido** invia una query all'archivio modelli di avvio rapido di Azure per il provider di risorse specificato. I modelli di avvio rapido vengono aggiunti e aggiornati di frequente. La presenza di un collegamento per un particolare servizio non significa necessariamente che la query restituisce modelli dall'archivio. Esistono anche diversi provider di risorse di terze parti che supportano Resource Manager. Nella sezione [Provider e tipi di risorse](#resource-providers-and-types) verrà illustrato come visualizzare tutti i provider di risorse.

## <a name="compute"></a>Calcolo
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Batch |Sì |[Batch REST](/rest/api/batchservice) |[Schema batch](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Contenitore |Sì |[Container Service REST](/rest/api/containerregistry) |[Schema contenitore](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Servizi del ciclo di vita Dynamics |Sì | | | |
| Set di scalabilità |Sì |[Scale Set REST](/rest/api/compute/virtualmachinescalesets) |[Schema set di scalabilità](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Sì |[REST di Service Fabric](/rest/api/servicefabric) | [Schema Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Macchine virtuali |Sì |[VM REST](/rest/api/compute/virtualmachines) |[Schema di macchine virtuali](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Macchine virtuali (classico) |Limitato |- |- |- |
| RemoteApp |No |- |- |- |
| Servizi cloud (versione classica) |Limitato (vedere sotto) |- |- |- |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md). 

Servizi cloud (classico) può essere usato con altre risorse classiche. Le risorse classiche tuttavia non usufruiscono di tutte le funzionalità di Resource Manager e non rappresentano un'opzione valida per le soluzioni future. Si consiglia di modificare l'infrastruttura dell'applicazione per usare risorse degli spazi dei nomi Microsoft.Compute, Microsoft.Storage e Microsoft.Network.

## <a name="networking"></a>Rete
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Gateway applicazione |Sì |[Application Gateway REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Sì |[REST di DNS](/rest/api/dns) |[Schema DNS](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |Sì |[REST di ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Bilanciamento del carico |Sì |[REST del servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Schema Load Balancer](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gestione traffico |Sì |[REST di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Schema Gestione traffico](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Reti virtuali |Sì |[REST di Rete virtuale](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Schema Rete virtuale](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gateway VPN |Sì |[Gateway di rete REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Archiviazione
| Service | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| Archiviazione |Sì |[REST di archiviazione](/rest/api/storagerp) |[Account di archiviazione](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Sì | | | |

## <a name="databases"></a>Database
| Service | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |Sì |[REST DocumentDB](/rest/api/documentdbresourceprovider) |[Schema DocumentDB](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Cache Redis |Sì | [Redis Cache REST (REST di Cache Redis)](/rest/api/redis) |[Schema Redis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Database SQL |Sì |[REST di database SQL](/rest/api/sql) |[Schema database SQL](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse |Sì | | | |

## <a name="web--mobile"></a>Web e dispositivi mobili
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| App per le API |Sì | [App Service REST (REST del servizio app)](/rest/api/appservice) |[Schema app per le API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[App per le API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestione API |Sì |[REST di gestione API](/rest/api/apimanagement) |[Schema Gestione API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Sì | | | |
| App per le funzioni |Sì | [REST di app per le funzioni](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| App per la logica |Sì |[REST di App per la logica](/rest/api/logic) |[Schema App per la logica](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| App per dispositivi mobili |Sì | [App Service REST (REST del servizio app)](/rest/api/appservice) |[Schema App per dispositivi mobili](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Mobile Engagement |Sì |[REST di Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Search |Sì |[REST Ricerca](/rest/api/searchservice) | [Schema Ricerca](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| App Web |Sì | [REST di App Web](/rest/api/appservice/webapps) |[Schema App Web](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>Intelligence e analisi
| Service | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Analysis Services | Sì | [REST di Analysis Service](/rest/api/analysisservices) | [Schema Analysis Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Servizi cognitivi |Sì | [Cognitive Services REST (REST di Servizi cognitivi)](/rest/api/cognitiveservices) |[Schema Servizi cognitivi](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Data Catalog |Sì |[API REST del catalogo dati](/rest/api/datacatalog) |[Schema Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data factory |Sì |[REST di Data factory](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Analisi Data Lake |Sì | [Data Lake REST (REST di Data Lake)](/rest/api/datalakeanalytics) |[Schema Data Lake Analytics](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Archivio Data Lake |Sì |[Data Lake Store REST Reference](/rest/api/datalakestore) |[Schema Data Lake Store](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeStore.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Sì |[REST di HDInsights](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Machine Learning |Sì |[Machine Learning REST](/rest/api/machinelearning) |[Schema Machine Learning](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Analisi di flusso |Sì |[REST di analisi di flusso](/rest/api/streamanalytics) | | |
| Power BI |Sì |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Schema Power BI](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>Internet delle cose
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Hub eventi |Sì |[REST di Hub eventi](/rest/api/eventhub) |[Schema hub eventi](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Hub IoT |Sì |[REST hub IoT](/rest/api/iothub) |[Schema hub IoT](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Hub di notifica |Sì |[REST di Hub di notifica](/rest/api/notificationhubs) |[Schema Hub d notifica](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Supporti multimediali e CDN
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| RETE CDN |Sì |[REST della rete CDN](/rest/api/cdn) |[Schema rete CDN](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servizi multimediali |Sì |[Riferimento all'API REST di Servizi multimediali di Azure](/rest/api/media) |[Schema Servizi multimediali](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |[Microsoft.Media](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Media%22&type=Code)  |

## <a name="hybrid-integration"></a>Integrazione ibrida
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Servizi BizTalk |Sì | |[Schema BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Servizio di ripristino |Sì |[REST Servizi di ripristino](/rest/api/recoveryservices) |[Schema Servizi di ripristino](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus di servizio |Sì |[REST del bus di servizio](/rest/api/servicebus) |[Schema bus di servizio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Gestione dell'identità e dell'accesso
Azure Active Directory funziona con Gestione risorse per l'abilitazione del controllo di accesso basato sul ruolo per la sottoscrizione. Per informazioni sull'uso del controllo degli accessi in base al ruolo e su Active Directory vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servizi per gli sviluppatori
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Monitorare |Sì |[REST Monitoraggio](/rest/api/monitor) |[Schema Insights](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Mappe |Sì | | | |
| DevTest Labs |Sì | [DevTest REST (REST di DevTest) ](/rest/api/dtl) |[Schema lab di sviluppo/test](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Account di Visual Studio |Sì | |[Schema Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Gestione e sicurezza
| Servizio | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- |
| Automazione |Sì |[REST automazione](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Schema Automazione](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Insieme di credenziali di chiave |Sì |[REST insieme di credenziali di chiave](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Segreto dell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights |Sì | | | |
| Utilità di pianificazione |Sì |[REST dell'utilità di pianificazione](/rest/api/scheduler) |[Schema Utilità di pianificazione](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sicurezza (anteprima) |Sì |[Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Gestione risorse
| Funzionalità | Gestione risorse abilitato | API REST | Schema | Modelli di avvio rapido |
| --- | --- | --- | --- | --- | --- |
| Authorization |Sì |[REST autorizzazione](/rest/api/authorization) |[Blocco risorse](resource-manager-template-lock.md)<br />[Assegnazioni di ruoli](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Risorse |Sì |[REST risorse](/rest/api/resources) |[Collegamenti alle risorse](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Provider e tipi di risorse
Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. Queste informazioni possono essere recuperate tramite l'API REST, Azure PowerShell o l'interfaccia della riga di comando di Azure.

Per usare un provider di risorse, il provider deve essere registrato con l'account. Per impostazione predefinita, molti provider di risorse sono registrati automaticamente. Tuttavia, potrebbe essere necessario registrare alcuni provider di risorse manualmente. Nell'esempio seguente viene illustrato come ottenere lo stato della registrazione di un provider di risorse e registrare il provider di risorse, se necessario.

### <a name="portal"></a>Portale
È possibile visualizzare facilmente un elenco di provider di risorse supportate selezionando **Provider di risorse** nel pannello di sottoscrizione. Per registrare la sottoscrizione con un provider di risorse, selezionare il collegamento **Registra**.
   
![Elenco di provider di risorse](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API REST
Per ottenere tutti i provider di risorse disponibili, completi di tipi, località, versioni API e stato di registrazione, usare l'operazione per [elencare tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . Per registrare un provider di risorse, vedere [Registrare una sottoscrizione con un provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

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

È possibile salvare in un file le informazioni relative a un provider di risorse specifico con il comando seguente:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Per registrare un provider di risorse, specificare lo spazio dei nomi:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Aree supportate
Quando si distribuiscono le risorse, in genere è necessario specificare un'area. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero essere anche presenti limitazioni sulla sottoscrizione che impediscono l'uso di alcune aree che supportano la risorsa. Queste limitazioni possono essere correlate a problemi per il paese di origine o il risultato di un criterio inserito dall'amministratore della sottoscrizione per utilizzare solo determinate aree. 

Per un elenco completo di tutte le regioni supportate per tutti i servizi di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/#services). Questo elenco potrebbe includere tuttavia aree non supportate dalla sottoscrizione. È possibile determinare le aree per un particolare tipo di risorsa supportata dalla sottoscrizione tramite il portale, l'API REST, PowerShell o l'interfaccia della riga di comando di Azure.

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
È possibile salvare in un file le informazioni (incluse le versioni dell'API disponibili) per un provider di risorse con il comando seguente:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

È possibile aprire il file e trovare l'elemento **apiVersions**

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).




<!--HONumber=Feb17_HO2-->


