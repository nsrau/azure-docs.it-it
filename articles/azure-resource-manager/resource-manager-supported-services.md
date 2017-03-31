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
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 46eb4a20c85140a7893d77984f375d6abf3e7634
ms.lasthandoff: 03/22/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provider, aree, versioni API e schemi di Gestione risorse
Questo argomento fornisce un elenco di provider di risorse che supportano Azure Resource Manager.

Durante la distribuzione delle risorse, è anche necessario conoscere quali aree supportano tali risorse e quali versioni dell'API sono disponibili per tali risorse. La sezione [Aree supportate](#supported-regions) illustra come trovare le aree adatte alla propria sottoscrizione e alle proprie risorse. La sezione [Versioni API supportate](#supported-api-versions) illustra come determinare quali versioni dell'API è possibile usare.

Per i servizi supportati nel portale di Azure e nel portale classico, vedere il [grafico di disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/). Per informazioni su servizi che supportano lo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuova](resource-group-move-resources.md).

Nelle tabelle seguenti vengono elencati quali servizi Microsoft supportano la distribuzione e la gestione tramite Resource Manager e quali no. Esistono anche diversi provider di risorse di terze parti che supportano Resource Manager. Nella sezione [Provider e tipi di risorse](#resource-providers-and-types) verrà illustrato come visualizzare tutti i provider di risorse.

## <a name="compute"></a>Calcolo
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| Batch |Sì |[Batch REST](/rest/api/batchservice) |[Risorse di batch](/azure/templates/microsoft.batch/batchaccounts) |
| Registro di sistema del contenitore |Sì |[Registro contenitori REST](/rest/api/containerregistry) |[Risorse di Registro contenitori](/azure/templates/microsoft.containerregistry/registries) |
| Servizio contenitore |Sì |[Container Service REST](/rest/api/compute/containerservices) |[Risorse del servizio contenitore](/azure/templates/microsoft.containerservice/containerservices) |
| Servizi del ciclo di vita Dynamics |Sì | | |
| Set di scalabilità |Sì |[Scale Set REST](/rest/api/compute/virtualmachinescalesets) |[Risorse del set di scalabilità](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |Sì |[REST di Service Fabric](/rest/api/servicefabric) | [Schema Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |
| Macchine virtuali |Sì |[VM REST](/rest/api/compute/virtualmachines) |[Risorse VM](/azure/templates/microsoft.compute/virtualmachines) |
| Macchine virtuali (classico) |Limitato |- |- |
| RemoteApp |No |- |- |
| Servizi cloud (versione classica) |Limitato (vedere sotto) |- |- |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md). 

Servizi cloud (classico) può essere usato con altre risorse classiche. Le risorse classiche tuttavia non usufruiscono di tutte le funzionalità di Resource Manager e non rappresentano un'opzione valida per le soluzioni future. Si consiglia di modificare l'infrastruttura dell'applicazione per usare risorse degli spazi dei nomi Microsoft.Compute, Microsoft.Storage e Microsoft.Network.

## <a name="networking"></a>Rete
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| gateway applicazione |Sì |[Application Gateway REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Risorse del gateway applicazione](/azure/templates/microsoft.network/applicationgateways) |
| DNS |Sì |[REST di DNS](/rest/api/dns) |[Risorse DNS](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |Sì |[REST di ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [Risorse di ExpressRoute](/azure/templates/microsoft.network/expressroutecircuits) |
| Bilanciamento del carico |Sì |[REST del servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Risorse di bilanciamento del carico](/azure/templates/microsoft.network/loadbalancers) |
| Gestione traffico |Sì |[REST di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Risorse di Gestione traffico](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Reti virtuali |Sì |[REST di Rete virtuale](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Risorse di Rete virtuale](/azure/templates/microsoft.network/virtualnetworks) |
| Gateway di rete |Sì |[Gateway di rete REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [Risorse di connessione](/azure/templates/microsoft.network/connections) <br /> [Risorse di gateway di rete locale](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Risorse di gateway di rete virtuale](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Archiviazione
| Service | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- | --- |
| Importazione/esportazione | Sì | [Importazione/esportazione REST](/rest/api/storageimportexport/) | [Risorse di importazione/esportazione](/azure/templates/microsoft.importexport/jobs) |
| Archiviazione |Sì |[REST di archiviazione](/rest/api/storagerp) |[Risorse di archiviazione](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |Sì | | |

## <a name="databases"></a>Database
| Service | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- | --- |
| DocumentDB |Sì |[REST DocumentDB](/rest/api/documentdbresourceprovider) |[Risorse di DocumentDB](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Cache Redis |Sì | [Redis Cache REST (REST di Cache Redis)](/rest/api/redis) |[Risorse di Redis](/azure/templates/microsoft.cache/redis) |
| Database SQL |Sì |[REST di database SQL](/rest/api/sql) |[Risorse del database SQL](/azure/templates/microsoft.sql/servers) |
| SQL Data Warehouse |Sì | | |

## <a name="web--mobile"></a>Web e dispositivi mobili
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| App per le API |Sì | [App Service REST (REST del servizio app)](/rest/api/appservice) |[Risorse Web](/azure/templates/microsoft.web/sites) |
| Gestione API |Sì |[REST di gestione API](/rest/api/apimanagement) |[Risorse di Gestione API](/azure/templates/microsoft.apimanagement/service) |
| Registrazione di certificati | Sì | [REST per la registrazione di certificati](/rest/api/appservice/appservicecertificateorders) | [Risorse di registrazione di certificati](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |Sì | | | |
| Registrazione di dominio | Sì | [Registrazione di dominio](/rest/api/appservice/domains) | [Risorse di registrazione di dominio](/azure/templates/microsoft.domainregistration/domains)  |
| App per le funzioni |Sì | [REST di app per le funzioni](/rest/api/appservice) | [Risorse Web](/azure/templates/microsoft.web/sites) |
| App per la logica |Sì |[REST di App per la logica](/rest/api/logic) |[Risorse dell'app per la logica](/azure/templates/microsoft.logic/workflows) |
| App per dispositivi mobili |Sì | [App Service REST (REST del servizio app)](/rest/api/appservice) | [Risorse Web](/azure/templates/microsoft.web/sites) |
| Mobile Engagement |Sì |[REST di Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Search |Sì |[REST Ricerca](/rest/api/searchservice) | [Risorse di ricerca](/azure/templates/microsoft.search/searchservices) |
| App Web |Sì | [REST di App Web](/rest/api/appservice/webapps) | [Risorse Web](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>Intelligence e analisi
| Service | Gestione risorse abilitato | API REST | Formato del modello | 
| --- | --- | --- | --- |
| Analysis Services | Sì | [REST di Analysis Service](/rest/api/analysisservices) | [Risorse di Analysis Services](/azure/templates/microsoft.analysisservices/servers) |
| Servizi cognitivi |Sì | [Cognitive Services REST (REST di Servizi cognitivi)](/rest/api/cognitiveservices) |[Risorse di Servizi cognitivi](/azure/templates/microsoft.cognitiveservices/accounts) |
| Data Catalog |Sì |[API REST del catalogo dati](/rest/api/datacatalog) |[Schema Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data factory |Sì |[REST di Data factory](/rest/api/datafactory) | |
| Analisi Data Lake |Sì | [Data Lake REST (REST di Data Lake)](/rest/api/datalakeanalytics) |[Risorse di Data Lake Analytics](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Archivio Data Lake |Sì |[Data Lake Store REST Reference](/rest/api/datalakestore) |[Risorse di Data Lake Store](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |Sì |[REST di HDInsights](/rest/api/hdinsight) | |
| Machine Learning |Sì |[Machine Learning REST](/rest/api/machinelearning) |[Risorse di Machine Learning](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Analisi di flusso |Sì |[REST di analisi di flusso](/rest/api/streamanalytics) | |
| Power BI |Sì |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Risorse di Power BI](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>Internet delle cose
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| Hub eventi |Sì |[REST di Hub eventi](/rest/api/eventhub) |[Risorse dell'hub eventi](/azure/templates/microsoft.eventhub/namespaces) |
| Hub IoT |Sì |[REST hub IoT](/rest/api/iothub) |[Risorse dell'hub IoT](/azure/templates/microsoft.devices/iothubs) |
| Hub di notifica |Sì |[REST di Hub di notifica](/rest/api/notificationhubs) |[Risorse dell'hub di notifica](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>Supporti multimediali e CDN
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| RETE CDN |Sì |[REST della rete CDN](/rest/api/cdn) |[Risorse della rete CDN](/azure/templates/microsoft.cdn/profiles) |
| Servizi multimediali |Sì |[Riferimento all'API REST di Servizi multimediali di Azure](/rest/api/media) |[Risorse multimediali](/azure/templates/microsoft.media/mediaservices) |

## <a name="hybrid-integration"></a>Integrazione ibrida
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| Servizi BizTalk |Sì | |[Schema BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Servizio di ripristino |Sì |[REST Servizi di ripristino](/rest/api/recoveryservices) |[Risorse di Servizi di ripristino](/azure/templates/microsoft.recoveryservices/vaults) |
| Bus di servizio |Sì |[REST del bus di servizio](/rest/api/servicebus) |[Risorse del bus di servizio](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>Gestione dell'identità e dell'accesso
Azure Active Directory funziona con Gestione risorse per l'abilitazione del controllo di accesso basato sul ruolo per la sottoscrizione. Per informazioni sull'uso del controllo degli accessi in base al ruolo e su Active Directory vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servizi per gli sviluppatori
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| Monitorare |Sì |[REST Monitoraggio](/rest/api/monitor) |[Risorse di Insights](/azure/templates/microsoft.insights/alertrules) |
| Bing Mappe |Sì | | |
| DevTest Labs |Sì | [DevTest REST (REST di DevTest) ](/rest/api/dtl) |[Risorse di DevTest Lab](/azure/templates/microsoft.devtestlab/labs) |
| Account di Visual Studio |Sì | |[Schema Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>Gestione e sicurezza
| Servizio | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- |
| Advisor | Sì | [REST di Advisor](/rest/api/advisor/) | - |
| Automazione |Sì |[REST automazione](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Schema di automazione](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |
| Fatturazione | Sì | [REST di fatturazione](/rest/api/billing/) | - |
| Insieme di credenziali di chiave |Sì |[REST insieme di credenziali di chiave](/rest/api/keyvault) |[Risorse di insieme di credenziali chiave](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights |Sì | | |
| Utilità di pianificazione |Sì |[REST dell'utilità di pianificazione](/rest/api/scheduler) |[Risorse dell'utilità di pianificazione](/azure/templates/microsoft.scheduler/jobcollections) |
| Sicurezza |Sì |[Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Gestione server | Sì | [REST di gestione del server](/rest/api/servermanagement/) | [Risorse di gestione del server](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>Gestione risorse
| Funzionalità | Gestione risorse abilitato | API REST | Formato del modello |
| --- | --- | --- | --- | --- |
| Authorization |Sì |[REST autorizzazione](/rest/api/authorization) |[Risorse di autorizzazione](/azure/templates/microsoft.authorization/locks) |
| Risorse |Sì |[REST risorse](/rest/api/resources) |[Risorse di distribuzione](/azure/templates/microsoft.resources/deployments) |

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

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


L'esempio seguente illustra come ottenere i tipi di risorse disponibili per un determinato provider di risorse.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

Per registrare un provider di risorse, specificare lo spazio dei nomi:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente illustra come ottenere tutti i provider di risorse disponibili.

```azurecli
az provider list
```

È possibile visualizzare le informazioni relative a un provider di risorse specifico con il comando seguente:

```azurecli
az provider show --namespace Microsoft.Web
```

Per registrare un provider di risorse, specificare lo spazio dei nomi:

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente illustra come ottenere le posizioni supportate per i siti Web.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>Versioni API supportate
Quando si distribuisce un modello, è necessario specificare una versione dell'API da usare per la creazione di ogni risorsa. La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST. Di conseguenza, la versione dell'API specificata nel modello influisce sulle proprietà che è possibile specificare nel modello. In generale, è opportuno selezionare la versione più recente dell'API durante la creazione di modelli. Per i modelli esistenti, è possibile decidere se si desidera continuare a usare una versione precedente dell'API o aggiornare il modello alla versione più recente per sfruttare i vantaggi delle nuove funzionalità.

### <a name="portal"></a>di Microsoft Azure
Le versioni dell'API supportate vengono determinate nello stesso modo in cui si stabiliscono le aree supportate, come illustrato prima.

### <a name="rest-api"></a>API REST
Per scoprire quali versioni API sono disponibili per determinati tipi di risorse, usare l'operazione [Elenca tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L'esempio seguente illustra come ottenere le versioni dell'API disponibili per un determinato tipo di risorse.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

L'output è simile a:

```powershell
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
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Il comando seguente consente di ottenere le versioni API disponibili per un provider di risorse:

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).


