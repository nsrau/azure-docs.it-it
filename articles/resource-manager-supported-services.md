<properties
   pageTitle="Servizi supportati e aree supportate da Gestione risorse | Microsoft Azure"
   description="Vengono descritti i provider di risorse che supportano Gestione risorse e le aree che possono ospitare le risorse."
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
   ms.date="11/11/2015"
   ms.author="tomfitz"/>

# Supporto di Gestione risorse per servizi, aree e versioni API

Gestione risorse di Azure fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi, supportano Gestione risorse e alcuni servizi supportano Gestione risorse solo parzialmente. Microsoft abiliterà Gestione risorse per ogni servizio importante per le soluzioni future, ma fino a quando il supporto sarà coerente, è necessario conoscere lo stato corrente di ogni servizio. In questo argomento è fornito un elenco di provider di risorse supportati per Gestione risorse di Azure.

Durante la distribuzione delle risorse, è anche necessario conoscere quali aree supportano tali risorse e quali versioni dell'API sono disponibili per tali risorse. La sezione [Aree supportate](#supported-regions) illustra come trovare le aree adatte alla propria sottoscrizione e alle proprie risorse. La sezione [Versioni API supportate](#supported-api-versions) illustra come determinare quali versioni dell'API è possibile usare.

Nelle tabelle seguenti vengono elencati quali servizi supportano la distribuzione e la gestione tramite Gestione risorse e quali no. La colonna denominata **Sposta risorse** indica se le risorse di questo tipo possono essere spostate sia in un nuovo gruppo di risorse che in una nuova sottoscrizione. La colonna denominata **Portale di anteprima** indica se è possibile creare il servizio tramite il portale di anteprima.


## Calcolo

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Macchine virtuali | Sì | Sì, molte opzioni | No | [Creare una VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Sì | [Sì (solo classica)](https://portal.azure.com/#create/Microsoft.BatchAccount) | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Servizi del ciclo di vita Dynamics | Sì | No | | | |
| Macchine virtuali (classiche) | Limitato | Sì | Parziale (vedere di seguito) | - | - |
| App remota | No | - | - | - | - |
| Infrastruttura di servizi | No | - | - | - | - |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md).

È possibile spostare le risorse delle macchine virtuali (classiche) nel nuovo gruppo di risorse, ma non in una nuova sottoscrizione.

## Web e dispositivi mobili

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Gestione API | Sì | No | Sì | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| App per le API | Sì | [Sì](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [01/03/2015-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| App Web | Sì | [Sì](https://portal.azure.com/#create/Microsoft.WebSite) | Sì, con limitazioni (vedere di seguito) | | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Hub di notifica | Sì | [Sì](https://portal.azure.com/#create/Microsoft.NotificationHub) | | [Creare un hub di notifica:](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [01/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| App per la logica | Sì | [Sì](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | | | |
| Mobile Engagement | Sì | No | Sì | | |

Quando si usano le app Web non è possibile spostare solo un piano di servizio app. Per spostare le app Web, le opzioni disponibili sono:

- Spostare tutte le risorse da un gruppo di risorse a un gruppo di risorse diverso, se il gruppo di risorse di destinazione non contiene già risorse Microsoft.Web.
- Spostare le app Web in un gruppo di risorse diverso, ma conservare il piano di servizio app nel gruppo di risorse originale.


## Dati e archiviazione

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Sì | Sì | Sì | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Archiviazione | Sì | Sì | | [Creazione dell'account di archiviazione](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Cache Redis | Sì | Sì | Sì | | [01/04/2014-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| Database SQL | Sì | Sì | Sì | [Creare database](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [01/04/2014-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Ricerca | Sì | Sì | Sì | [REST Ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Sì | Sì | | | |
| StorSimple | No | No | - | - | - |
| Backup | No | No | - | - | - |
| Site Recovery | No | No | - | - | - |
| Cache gestita | No | No | - | - | - |
| Catalogo dati | No | No | - | - | - |

## Analytics

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Hub eventi | Sì | No | | [Creare l'hub eventi](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Analisi dei flussi | Sì | [Sì](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | Sì | [Sì](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | | | |
| Data factory | Sì | [Sì](https://portal.azure.com/#create/Microsoft.DataFactory) | Sì | [Creare Data factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Machine Learning | No | No | - | - | - | | Catalogo dati | No | No | - | - | - |

## Rete

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Gateway applicazione | Sì | | | | |
| DNS | Sì | | | [Creare una zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Bilanciamento del carico | Sì | | | [Crea servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Reti virtuali | Sì | [Sì](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | No | [Creare una rete virtuale](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Gestione traffico | Sì | No | | [Creare un profilo di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | Sì | No | No | [REST di ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## Supporti multimediali e CDN

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| RETE CDN | Sì (anteprima) | No | | | |
| Servizi multimediali | No | No | | | |


## Integrazione ibrida

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Servizi BizTalk | Sì | No | | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Bus di servizio | Sì | No | | [REST bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| Backup | No | No | - | - | - | | Ripristino sito | No | No | - | - | - |

## Gestione dell'identità e dell'accesso 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | No | No | - | - | - |
| Azure Active Directory B2C | No | No | - | - | - |
| Multi-Factor Authentication | No | No | - | - | - |

## Servizi per gli sviluppatori 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Sì | [Sì](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Mappe | Sì | [Sì](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Account di Visual Studio | Sì | | | | [26/02/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## gestione 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automazione | Sì | [Sì](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | | | |
| Insieme di credenziali di chiave | Sì | No | Sì | [REST insieme di credenziali di chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Utilità di pianificazione | Sì | No | | | [01/08/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Sì | No | Sì | | |
| Hub IoT | Sì | [Sì](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## Gestione risorse

| Funzionalità | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Authorization | Sì | N/D | N/D | [Blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controllo degli accessi in base al ruolo](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Blocco di risorsa](resource-manager-template-lock.md)<br />[Assegnazioni di ruoli](resource-manager-template-role.md) |
| Risorse | Sì | N/D | N/D | [Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Collegamenti alle risorse](resource-manager-template-links.md) |


## Aree supportate

Quando si distribuiscono le risorse, in genere è necessario specificare un'area. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Inoltre, potrebbero essere presenti limitazioni sulla sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa. Queste limitazioni possono essere correlate a problemi per il paese di origine o il risultato di un criterio inserito dall'amministratore della sottoscrizione per utilizzare solo determinate aree.

Per un elenco completo di tutte le aree supportate per tutti i servizi di Azure, vedere [Servizi per area](https://azure.microsoft.com/regions/#services); tuttavia, questo elenco può includere aree che non sono supportate dalla sottoscrizione dell'utente. È possibile determinare le aree per un particolare tipo di risorsa supportate dalla sottoscrizione dell’utente eseguendo uno dei seguenti comandi.

### API REST

Per individuare le aree disponibili per un determinato tipo di risorsa nella sottoscrizione, usare l'operazione [Elenca tutti i provider di risorse](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

Nell'esempio seguente viene illustrato come ottenere le aree supportate per i siti Web utilizzando l'anteprima di Azure PowerShell 1.0. Per altre informazioni sul rilascio dell'anteprima di 1.0, vedere [Anteprima di Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/)

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

Usare il comando seguente per Azure PowerShell 0.9.8.

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

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

L'esempio seguente illustra come ottenere le versioni dell'API disponibili per un determinato tipo di risorse usando l'anteprima di Azure PowerShell 1.0.

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

Per Azure PowerShell 0.9.8, utilizzare:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Interfaccia della riga di comando di Azure

È possibile salvare le informazioni (incluse le versioni dell'API disponibili) per un provider di risorse in un file con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

È possibile aprire il file e trovare l'elemento **apiVersions**

## Passaggi successivi

- Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per altre informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=Nov15_HO3-->