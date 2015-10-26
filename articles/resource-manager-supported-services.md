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
   ms.date="10/13/2015"
   ms.author="tomfitz"/>

# Supporto di Gestione risorse di Azure per servizi e aree

Gestione risorse di Azure fornisce un nuovo modo per distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi, supportano Gestione risorse e alcuni servizi supportano Gestione risorse solo parzialmente. Microsoft abiliterà Gestione risorse per ogni servizio importante per le soluzioni future, ma fino a quando il supporto sarà coerente, è necessario conoscere lo stato corrente di ogni servizio. In questo argomento è fornito un elenco di provider di risorse supportati per Gestione risorse di Azure.

Durante la distribuzione delle risorse, è necessario inoltre conoscere quali aree supportano tali risorse. La sezione [Aree supportate](#supported-regions) illustra come individuare quali aree funzioneranno per la propria sottoscrizione e le proprie risorse.

Nelle tabelle seguenti vengono elencati quali servizi supportano la distribuzione e la gestione tramite Gestione risorse e quali no. La colonna denominata **Sposta risorse** indica se le risorse di questo tipo possono essere spostate sia in un nuovo gruppo di risorse che in una nuova sottoscrizione. La colonna denominata **Portale di anteprima** indica se è possibile creare il servizio tramite il portale di anteprima.


## Calcolo

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Macchine virtuali | Sì | Sì | No | [Creare una VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Sì | No | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Servizi del ciclo di vita Dynamics | Sì | No | | | |
| Macchine virtuali (classiche) | Limitato | No | Parziale (vedere di seguito) | - | - | | App remota | No | - | - | - | - | | Infrastruttura di servizi | No | - | - | - | - |

Macchine virtuali (classiche) fa riferimento a risorse che sono state distribuite attraverso il modello di distribuzione classica, anziché tramite il modello di distribuzione di Gestione risorse. In generale, queste risorse non supportano le operazioni di Gestione risorse, ma esistono alcune operazioni che sono state abilitate. Per altre informazioni su questi modelli di distribuzione, vedere [Comprendere la distribuzione di Gestione risorse e la distribuzione classica](resource-manager-deployment-model.md).

È possibile spostare le risorse delle macchine virtuali (classiche) nel nuovo gruppo di risorse, ma non in una nuova sottoscrizione.

## Web e dispositivi mobili

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Gestione API| Sì | No | Sì | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| App per le API | Sì | Sì | | | [01/03/2015-anteprima](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| App Web | Sì | Sì | Sì, con limitazioni (vedere di seguito) | | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Hub di notifica | Sì | Sì | | [Creare un hub di notifica:](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [01/04/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| App per la logica | Sì | Sì | | | |
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
| StorSimple | No | No | - | - | - | | Backup | No | No | - | - | - | | Site Recovery | No | No | - | - | - | | Cache gestita | No | No | - | - | - | | Catalogo dati | No | No | - | - | - |

## Analytics

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Hub eventi | Sì | No | | [Creare l'hub eventi](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Analisi dei flussi | Sì | Sì | | | |
| HDInsights | Sì | Sì | | | |
| Data Factory | Sì | Sì | Sì | [Creare Data factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Machine Learning | No | No | - | - | - |

## Rete

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Gateway applicazione | Sì | | | | |
| DNS | Sì | | | [Creare una zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Bilanciamento del carico | Sì | | | [Crea servizio di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Reti virtuali | Sì | Sì | No | [Creare una rete virtuale](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [01/08/2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Gestione traffico | Sì | No | | [Creare un profilo di Gestione traffico](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Express Route | No | No | - | - | - |

## Supporti multimediali e CDN

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Servizi multimediali | No | No | | | |
| RETE CDN | No | No | | | |

## Integrazione ibrida

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Servizi BizTalk | Sì | No | | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Bus di servizio | Sì | No | | [REST bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |

## Gestione dell'identità e dell'accesso 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | No | No | - | - | - | | Azure Active Directory B2C | No | No | - | - | - | | Multi-Factor Authentication | No | No | - | - | - |

## Servizi per gli sviluppatori 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Sì | Sì | | | [01/04/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Mappe | Sì | Sì | | | |
| Account di Visual Studio | Sì | | | | [26/02/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## gestione 

| Servizio | Gestione risorse abilitato | Portale di anteprima | Sposta risorse | API REST | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automazione | Sì | Sì | | | |
| Insieme di credenziali di chiave | Sì | No | Sì | [REST insieme di credenziali di chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Utilità di pianificazione | Sì | No | | | [01/08/2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Sì | No | Sì | | |
| Hub IoT | Sì | Sì | | | |


## Aree supportate

Quando si distribuiscono le risorse, in genere è necessario specificare un'area. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Inoltre, potrebbero essere presenti limitazioni sulla sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa. Queste limitazioni possono essere correlate a problemi per il paese di origine o il risultato di un criterio inserito dall'amministratore della sottoscrizione per utilizzare solo determinate aree.

Prima di distribuire le risorse, controllare le aree supportate per il tipo di risorsa eseguendo uno dei seguenti comandi.

### API REST

La soluzione migliore per scoprire le aree sono disponibili per un determinato tipo di risorsa è l’operazione [Elenca tutti i provider di risorse](https://msdn.microsoft.com/library/azure/dn790524.aspx). Questa operazione restituisce solo le aree disponibili per la sottoscrizione e il tipo di risorsa.

### PowerShell

Nell'esempio seguente viene illustrato come ottenere le aree supportate per i siti Web utilizzando l'anteprima di Azure PowerShell 1.0. Per ulteriori informazioni sul rilascio dell’Anteprima 1.0, vedere [Anteprima di Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/)

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

È inoltre possibile filtrare i risultati dei percorsi con uno strumento come **jq**. Per informazioni su strumenti come jq, vedere [Strumenti utili per interagire con Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Che restituisce:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

<!---HONumber=Oct15_HO3-->