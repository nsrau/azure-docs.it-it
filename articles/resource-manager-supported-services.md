<properties
   pageTitle="Servizi, aree, schemi e versioni supportati da Gestione risorse | Microsoft Azure"
   description="Descrive i provider di risorse che supportano Gestione risorse, i relativi schemi e le versioni API disponibili, nonché le aree che possono ospitare le risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/06/2016"
   ms.author="tomfitz"/>

# Provider, aree, versioni API e schemi di Gestione risorse

Gestione risorse di Azure fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi, supportano Gestione risorse e alcuni servizi supportano Gestione risorse solo parzialmente. Microsoft abiliterà Gestione risorse per ogni servizio importante per le soluzioni future, ma fino a quando il supporto sarà coerente, è necessario conoscere lo stato corrente di ogni servizio. In questo argomento è fornito un elenco di provider di risorse supportati per Gestione risorse di Azure.

Durante la distribuzione delle risorse, è anche necessario conoscere quali aree supportano tali risorse e quali versioni dell'API sono disponibili per tali risorse. La sezione [Aree supportate](#supported-regions) illustra come trovare le aree adatte alla propria sottoscrizione e alle proprie risorse. La sezione [Versioni API supportate](#supported-api-versions) illustra come determinare quali versioni dell'API è possibile usare.

Per i servizi supportati nel portale di Azure e nel portale classico, vedere il [grafico di disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

Nelle tabelle seguenti vengono elencati quali servizi supportano la distribuzione e la gestione tramite Gestione risorse e quali no. La colonna denominata **Sposta risorse** indica se le risorse di questo tipo possono essere spostate sia in un nuovo gruppo di risorse sia in una nuova sottoscrizione. Il collegamento nella colonna **Modelli di avvio rapido** invia una query all'archivio modelli di avvio rapido di Azure per il provider di risorse specificato. I modelli di avvio rapido vengono aggiunti e aggiornati di frequente. La presenza di un collegamento per un particolare servizio non significa necessariamente che la query restituirà modelli dall'archivio.


## Calcolo

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Macchine virtuali | Sì | No | [VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Batch | Sì | Sì | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Servizi del ciclo di vita Dynamics | Sì | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (anteprima) | Sì | | [REST di Service Fabric](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Macchine virtuali (classiche) | Limitato | Parziale (vedere di seguito) | - | - | | App remota | No | - | - | - | | Servizi cloud (classici) | No | Parziale (vedere di seguito) | - | - | - |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md).

È possibile spostare Macchine virtuali (classiche) e Servizi cloud in nuovi gruppi di risorse, ma non in nuove sottoscrizioni.

## Rete

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Gateway applicazione | Sì | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Sì | | [REST di DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Bilanciamento del carico | Sì | | [REST del servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Reti virtuali | Sì | Parziale (vedere di seguito) | [REST di Rete virtuale](https://msdn.microsoft.com/it-IT/library/azure/mt163650.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gestione traffico | Sì | | [REST di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [01/11/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | Sì | No | [REST di ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

È possibile spostare Reti virtuali in nuovi gruppi di risorse, ma non in nuove sottoscrizioni.

## Dati e archiviazione

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sì | Sì | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [08/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Archiviazione | Sì | No | [REST di archiviazione](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Account di archiviazione](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Cache Redis | Sì | Sì | | [01/08/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Database SQL | Sì | Sì | [REST di database SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [01/04/2014-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Search | Sì | Sì | [REST Ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| SQL Data Warehouse | Sì | | | |
| StorSimple | No | - | - | - | | Cache gestita | No| - | - | - |

## Web e dispositivi mobili

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Gestione API | Sì | Sì | [REST di gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| App per le API | Sì | | | [01/03/2015-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [App per le API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| App Web | Sì | Sì, con limitazioni (vedere di seguito) | | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Hub di notifica | Sì | Sì | [REST di Hub di notifica](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [01/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| App per la logica | Sì | Sì | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Engagement | Sì | Sì | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

Quando si usano le app Web non è possibile spostare solo un piano di servizio app. Per spostare le app Web, le opzioni disponibili sono:

- Spostare tutte le risorse da un gruppo di risorse a un gruppo di risorse diverso, se il gruppo di risorse di destinazione non contiene già risorse Microsoft.Web.
- Spostare le app Web in un gruppo di risorse diverso, ma conservare il piano di servizio app nel gruppo di risorse originale.

## Analytics

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Hub eventi | Sì | | [REST di Hub eventi](https://msdn.microsoft.com/library/azure/dn790674.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Analisi dei flussi | Sì | | [REST di analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | Sì | Sì | [REST di HDInsights](https://msdn.microsoft.com/library/azure/mt622197.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Data factory | Sì | Sì | [REST di Data factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Machine Learning | No | - | - | - | | Catalogo dati | No | - | - | - |

## Supporti multimediali e CDN

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| RETE CDN | Sì | | [REST della rete CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servizi multimediali | No | | | |


## Integrazione ibrida

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Servizi BizTalk | Sì | | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Bus di servizio | Sì | | [REST bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Backup | No | - | - | - | | Ripristino sito | No | - | - | - |

## Gestione dell'identità e dell'accesso 

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Azure Active Directory | No | - | - | - | | Azure Active Directory B2C | No | - | - | - | | Multi-Factor Authentication | No | - | - | - |

## Servizi per gli sviluppatori 

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | Sì | No | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Mappe | Sì | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Lab di sviluppo/test (anteprima) | Sì | | | [21/05/2015-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Account di Visual Studio | Sì | | | [26/02/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Gestione e sicurezza

| Servizio | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Automazione | Sì | Sì | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Insieme di credenziali di chiave | Sì | Sì | [REST insieme di credenziali di chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Insieme di credenziali chiave](resource-manager-template-keyvault.md)<br />[Segreto dell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Utilità di pianificazione | Sì | | [REST dell'utilità di pianificazione](https://msdn.microsoft.com/library/azure/mt629143.aspx) | [01/08/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Operational Insights | Sì | Sì | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| Hub IoT | Sì | | [REST hub IoT](https://msdn.microsoft.com/library/azure/mt589014.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Sicurezza (anteprima) | Sì | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Gestione risorse

| Funzionalità | Gestione risorse abilitato | Sposta risorse | API REST | Schema | Modelli di avvio rapido |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Authorization | Sì | N/D | [Blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controllo degli accessi in base al ruolo](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Blocco della risorsa](resource-manager-template-lock.md)<br />[Assegnazioni di ruoli](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Risorse | Sì | N/D | [Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Collegamenti alle risorse](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Provider e tipi di risorse

Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. Queste informazioni possono essere recuperate tramite l'API REST, Azure PowerShell o l'interfaccia della riga di comando di Azure.

### API REST

Per ottenere tutti i provider di risorse disponibili, con informazioni sullo stato della registrazione e i tipi, le posizioni e le versioni dell'API supportate, usare l'operazione [Elencare tutti i provider di risorse](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L'esempio seguente illustra come ottenere tutti i provider di risorse disponibili.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable
    
L'output sarà analogo al seguente:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    ...

L'esempio seguente illustra come ottenere i tipi di risorse disponibili per un determinato provider di risorse.

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
L'output sarà analogo al seguente:

    ResourceTypeName                Locations                                         ApiVersions
    ----------------                ---------                                         ------
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} {20...
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} {20...
    ...
    
### Interfaccia della riga di comando di Azure

È possibile salvare in un file le informazioni relative a un provider di risorse con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

## Aree supportate

Quando si distribuiscono le risorse, in genere è necessario specificare un'area. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Inoltre, potrebbero essere presenti limitazioni sulla sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa. Queste limitazioni possono essere correlate a problemi per il paese di origine o il risultato di un criterio inserito dall'amministratore della sottoscrizione per utilizzare solo determinate aree.

Per un elenco completo di tutte le aree supportate per tutti i servizi di Azure, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services). Questo elenco può tuttavia includere aree che non sono supportate dalla sottoscrizione dell'utente. È possibile determinare le aree per un particolare tipo di risorsa supportate dalla sottoscrizione dell’utente eseguendo uno dei seguenti comandi.

### API REST

Per individuare le aree disponibili per un determinato tipo di risorsa nella sottoscrizione, usare l'operazione [Elenca tutti i provider di risorse](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L'esempio seguente mostra come ottenere le aree supportate per i siti Web.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
L'output sarà analogo al seguente:

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

### Interfaccia della riga di comando di Azure

L'esempio seguente restituisce tutti i percorsi supportati per ogni tipo di risorsa.

    azure location list

È anche possibile filtrare i risultati dei percorsi con uno strumento come **jq**. Per informazioni su strumenti come jq, vedere [Strumenti utili per interagire con Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Che restituisce:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Versioni API supportate

Quando si distribuisce un modello, è necessario specificare una versione dell'API da usare per la creazione di ogni risorsa. La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascerà una nuova versione dell'API REST. Di conseguenza, la versione dell'API specificata nel modello influisce sulle proprietà che è possibile specificare nel modello. In generale, è opportuno selezionare la versione più recente dell'API durante la creazione di nuovi modelli. Per i modelli esistenti, è possibile decidere se si desidera continuare a usare una versione precedente dell'API o aggiornare il modello alla versione più recente per sfruttare i vantaggi delle nuove funzionalità.

### API REST

Per scoprire quali versioni API sono disponibili per determinati tipi di risorse, usare l'operazione [Elenca tutti i provider di risorse](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L'esempio seguente illustra come ottenere le versioni dell'API disponibili per un determinato tipo di risorse.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
L'output sarà analogo al seguente:
    
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

### Interfaccia della riga di comando di Azure

È possibile salvare le informazioni (incluse le versioni dell'API disponibili) per un provider di risorse in un file con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

È possibile aprire il file e trovare l'elemento **apiVersions**

## Passaggi successivi

- Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0107_2016-->