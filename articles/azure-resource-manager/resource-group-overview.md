---
title: Panoramica di Azure Resource Manager | Documentazione Microsoft
description: Viene descritto come utilizzare Gestione risorse di Azure per la distribuzione, la gestione e il controllo dell’accesso delle risorse in Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: tomfitz
ms.openlocfilehash: 0ad1d12a4a2ca3a293546f2bac85210bb9152269
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269298"
---
# <a name="azure-resource-manager-overview"></a>Panoramica di Gestione risorse di Microsoft Azure

Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione coerente che consente di creare, aggiornate ed eliminare risorse nella sottoscrizione di Azure. Offre funzionalità di controllo di accesso, controllo e assegnazione di tag che è possibile usare per proteggere e organizzare le risorse dopo la distribuzione.

Quando si eseguono operazioni nel portale, in PowerShell, nell'interfaccia della riga di comando di Azure, nelle API REST o negli SDK client, l'API di Azure Resource Manager gestisce la richiesta. Poiché tutte le richieste vengono gestite tramite la stessa API, i risultati e le funzionalità risultano coerenti in tutti i vari strumenti. Tutte le funzionalità disponibili nel portale sono disponibili anche tramite PowerShell, l'interfaccia della riga di comando di Azure, le API REST e gli SDK client. Le funzionalità inizialmente rilasciate tramite API vengono rappresentate nel portale entro 180 giorni dal rilascio iniziale.

La figura seguente mostra tutti gli strumenti che interagiscono con la stessa API di Azure Resource Manager. L'API passa le richieste al servizio Resource Manager, che autentica e autorizza le richieste. Resource Manager le indirizza quindi al servizio appropriato.

![Modello di richiesta di Resource Manager](./media/resource-group-overview/consistent-management-layer.png)

## <a name="terminology"></a>Terminologia

Se non si ha esperienza con Azure Resource Manager, ecco alcuni termini con cui acquisire familiarità.

* **risorsa** : elemento gestibile disponibile tramite Azure. Sono ad esempio risorse le macchine virtuali, gli account di archiviazione, le app Web, i database e le reti virtuali.
* **gruppo di risorse** : contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse include le risorse che si vogliono gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. Vedere [Gruppi di risorse](#resource-groups).
* **provider di risorse**: servizio che fornisce le risorse di Azure. Un provider di risorse comune è ad esempio **Microsoft.Compute**, che fornisce la risorsa macchina virtuale. **Microsoft.Storage** è un altro provider di risorse comune. Vedere [Provider di risorse](#resource-providers).
* **modello di Resource Manager** : file JSON (JavaScript Object Notation) che definisce una o più risorse da distribuire a un gruppo di risorse o a una sottoscrizione. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto. Vedere [Distribuzione del modello](#template-deployment).
* **sintassi dichiarativa** : sintassi che consente di indicare l'oggetto da creare senza dover scrivere la sequenza di comandi di programmazione per crearlo. Il modello di Resource Manager è un esempio di sintassi dichiarativa. Nel file vengono definite le proprietà per l'infrastruttura da distribuire in Azure.

## <a name="the-benefits-of-using-resource-manager"></a>Vantaggi dell'utilizzo di Gestione risorse

Gestione risorse offre numerosi vantaggi:

* È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.
* È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.
* È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.
* È possibile definire le dipendenze tra risorse in modo che vengano distribuite nell'ordine corretto.
* è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.
* È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.
* È possibile ottenere informazioni dettagliate sulla fatturazione per l'organizzazione visualizzando i costi di un gruppo di risorse che condividono lo stesso tag.

## <a name="understand-management-scope"></a>Informazioni sull'ambito di gestione

Azure offre quattro livelli relativi all'ambito di gestione: [gruppi di gestione](../governance/management-groups/index.md), sottoscrizioni, [gruppi di risorse](#resource-groups) e risorse. Nella figura seguente viene illustrato un esempio di questi livelli.

![Scope](./media/resource-group-overview/scope-levels.png)

Le impostazioni di gestione possono essere applicate a qualsiasi di questi livelli di ambito. Il livello selezionato determina l'estensione con cui viene applicata l'impostazione. I livelli inferiori ereditano le impostazioni dai livelli superiori. Ad esempio, quando si applicano [criteri](../governance/policy/overview.md) alla sottoscrizione, tali criteri vengono applicati a tutti i gruppi di risorse e le risorse nella sottoscrizione. Quando si applicano criteri al gruppo di risorse, tali criteri vengono applicati al gruppo di risorse e a tutte le risorse che contiene. Tuttavia, un altro gruppo di risorse non disporrà di tale assegnazione di criteri.

## <a name="guidance"></a>Materiale sussidiario

I suggerimenti seguenti consentono di sfruttare al meglio Resource Manager per le proprie soluzioni.

* Definire e distribuire l'infrastruttura tramite la sintassi dichiarativa nei modelli di Gestione risorse, anziché tramite comandi imperativi.
* Definire tutti i passaggi di distribuzione e configurazione nel modello. Per la configurazione della soluzione, è consigliabile evitare procedure manuali.
* Eseguire i comandi imperativi per gestire le risorse, ad esempio per avviare o arrestare un'app o un computer.
* Includere le risorse con lo stesso ciclo di vita in un gruppo di risorse. Usare le categorie per tutte le altre attività di organizzazione delle risorse.

Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

Per suggerimenti sulla creazione di modelli di Resource Manager, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).

## <a name="resource-groups"></a>Gruppi di risorse
Esistono alcuni fattori importanti da considerare quando si definisce il gruppo di risorse:

* Tutte le risorse del gruppo devono condividere lo stesso ciclo di vita. Le risorse vengono distribuite, aggiornate ed eliminate insieme. Se una risorsa, ad esempio un server di database, deve esistere in un ciclo di distribuzione diverso deve essere inclusa in un altro gruppo di risorse.
* Ogni risorsa può appartenere a un solo gruppo di risorse.
* È possibile aggiungere o rimuovere una risorsa in un gruppo di risorse in qualsiasi momento.
* È possibile spostare una risorsa da un gruppo di risorse a un altro. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
* Un gruppo di risorse può contenere risorse che risiedono in aree diverse.
* Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative.
* Una risorsa può interagire con le risorse di altri gruppi di risorse. Questa interazione è comune quando le due risorse sono correlate ma non condividono lo stesso ciclo di vita, ad esempio app Web che si connettono a un database.

Quando si crea un gruppo di risorse è necessario specificarne il percorso. Perché un gruppo di risorse necessita di un percorso? E se le risorse possono avere percorsi diversi rispetto al gruppo di risorse, perché il percorso del gruppo di risorse è importante? Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Se l'area del gruppo di risorse è temporaneamente non disponibile, non è possibile aggiornare le risorse nel gruppo di risorse perché i metadati non sono disponibili. Le risorse in altre aree continueranno a funzionare come previsto, ma non è possibile aggiornarle. Per ridurre il rischio, collocare il gruppo di risorse e le risorse nella stessa area.

## <a name="resource-providers"></a>Provider di risorse

Ogni provider di risorse offre una serie di risorse e operazioni per l'uso di tali risorse. Per archiviare chiavi e segreti sarà ad esempio necessario usare il provider di risorse **Microsoft.KeyVault** . Questo provider di risorse offre un tipo di risorsa denominato **vaults** per creare l'insieme di credenziali delle chiavi.

Il nome di un tipo di risorsa è nel formato: **{resource-provider}/{resource-type}**. Il tipo di risorsa per un insieme di credenziali delle chiavi è **Microsoft.KeyVault/vaults**.

Prima di iniziare con la distribuzione delle risorse è necessario comprendere i provider di risorse disponibili. Conoscere i nomi dei provider di risorse e delle risorse consente di definire le risorse da distribuire in Azure. Inoltre, è necessario conoscere le posizioni e le versioni dell'API valide per ogni tipo di risorsa. Per altre informazioni, vedere [Provider e tipi di risorse](resource-manager-supported-services.md).

Per tutte le altre operazioni offerte dai provider di risorse, vedere le [API REST di Azure](/rest/api/azure/).

## <a name="template-deployment"></a>Distribuzione del modello

Resource Manager consente di creare un modello in formato JSON che definisce l'infrastruttura e la configurazione della soluzione di Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

Per informazioni sul formato del modello e su come crearlo, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md). Per visualizzare la sintassi JSON per i tipi di risorse, vedere [Define resources in Azure Resource Manager templates](/azure/templates/) (Definire le risorse nei modelli di Azure Resource Manager).

Resource Manager elabora il modello come qualsiasi altra richiesta. Il modello analizza e converte la sintassi in operazioni dell'API REST per i provider di risorse appropriati. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Converte la definizione nell'operazione dell'API REST seguente, che viene inviata al provider di risorse Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

La modalità di definizione dei modelli e dei gruppi di risorse è una scelta personale e dipende dalla modalità di gestione della soluzione preferita. Ad esempio, è possibile distribuire l'applicazione a tre livelli tramite un unico modello in un singolo gruppo di risorse.

![modello a tre livelli](./media/resource-group-overview/3-tier-template.png)

Non è tuttavia necessario definire l'intera infrastruttura in un unico modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/resource-group-overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/resource-group-overview/tier-templates.png)

Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

Azure Resource Manager analizza le dipendenze per far sì che le risorse vengano create nell'ordine corretto. Se una risorsa si basa sul valore di un'altra risorsa, ad esempio una macchina virtuale che richiede un account di archiviazione per i dischi, impostare una dipendenza. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. È ad esempio possibile aggiungere una risorsa alla soluzione e quindi aggiungere regole di configurazione per le risorse già distribuite. Se il modello definisce una risorsa già esistente, Resource Manager la aggiorna invece di crearne una nuova.

In Resource Manager sono disponibili estensioni utili negli scenari che richiedono operazioni aggiuntive, ad esempio l'installazione di software specifico non incluso nella configurazione. Se si usa già un servizio di gestione della configurazione, ad esempio DSC, Chef o Puppet, è possibile continuare a usarlo tramite estensioni. Per informazioni sulle estensioni delle macchine virtuali, vedere [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando si crea una soluzione dal portale, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero, perché è possibile iniziare con il modello della soluzione e personalizzarlo per soddisfare esigenze specifiche. Per un esempio completo, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). È anche possibile recuperare un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse oppure visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il [modello esportato](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

Infine, il modello diventa parte del codice sorgente per l'applicazione. È possibile archiviarlo nel repository del codice sorgente e aggiornarlo con l'evoluzione dell'applicazione. È possibile modificare il modello tramite Visual Studio.

Dopo aver definito il modello è possibile distribuire le risorse in Azure. Per distribuire le risorse, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md)
* [Distribuire le risorse con i modelli e l'API REST di Resource Manager](resource-group-template-deploy-rest.md)

## <a name="safe-deployment-practices"></a>Procedure di distribuzione sicure

Quando si distribuisce un servizio complesso in Azure, è necessario distribuire il servizio in più aree e verificarne l'integrità prima di procedere al passaggio successivo. Usare [Azure Deployment Manager](deployment-manager-overview.md) per coordinare un'implementazione per fasi del servizio. Con l'implementazione temporanea del servizio, è possibile individuare potenziali problemi prima che il servizio sia distribuito in tutte le aree. Se non sono necessarie queste precauzioni, le operazioni di distribuzione nella sezione precedente sono la scelta migliore.

Deployment Manager è attualmente disponibile nella versione di anteprima privata.

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare Azure Resource Manager per la distribuzione, la gestione e il controllo di accesso delle risorse in Azure. Procedere con l'articolo successivo per imparare a creare il primo modello di Azure Resource Manager.

> [!div class="nextstepaction"]
> [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md)
