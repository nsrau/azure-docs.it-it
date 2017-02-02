---
title: Panoramica di Azure Resource Manager | Documentazione Microsoft
description: "Viene descritto come utilizzare Gestione risorse di Azure per la distribuzione, la gestione e il controllo dell’accesso delle risorse in Azure."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1460a3e6b3d225a507e5da51dcc66810862ee2de
ms.openlocfilehash: 4001c2d9bf2a635d7189ae46a855e347b93185c8


---
# <a name="azure-resource-manager-overview"></a>Panoramica di Gestione risorse di Microsoft Azure
L'infrastruttura per l'applicazione è in genere costituita da vari componenti, ad esempio una macchina virtuale, un account di archiviazione e una rete virtuale oppure un'app Web, un database, un server di database e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. Gestione risorse di Azure consente di usare le risorse incluse nella soluzione come un gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. Per la distribuzione viene usato un modello; questo modello può essere usato per diversi ambienti, ad esempio di testing, staging e produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione. 

## <a name="terminology"></a>Terminologia
Se non si ha esperienza con Azure Resource Manager, è possibile che non si conoscano alcuni termini.

* **risorsa** : elemento gestibile disponibile tramite Azure. Alcune risorse comuni sono le macchine virtuali, gli account di archiviazione, le app Web, i database e le reti virtuali, ma ne esistono molte altre.
* **gruppo di risorse** : contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. Vedere [Gruppi di risorse](#resource-groups).
* **provider di risorse** : servizio che mette a disposizione le risorse che è possibile distribuire e gestire con Resource Manager. Ogni provider di risorse offre operazioni per l'uso delle risorse distribuite. Alcuni provider di risorse comuni sono Microsoft.Compute che mette a disposizione la risorsa delle macchine virtuali, Microsoft.Storage che offre la risorsa di account di archiviazione e Microsoft.Web che mette a disposizione risorse correlate alle app Web. Vedere [Provider di risorse](#resource-providers).
* **modello di Resource Manager** : file JavaScript Object Notation (JSON) che definisce una o più risorse da distribuire in un gruppo di risorse. Definisce anche le dipendenze tra le risorse distribuite. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto. Vedere [Distribuzione del modello](#template-deployment).
* **sintassi dichiarativa** : sintassi che consente di indicare l'oggetto da creare senza dover scrivere la sequenza di comandi di programmazione per crearlo. Il modello di Resource Manager è un esempio di sintassi dichiarativa. Nel file vengono definite le proprietà per l'infrastruttura da distribuire in Azure. 

## <a name="the-benefits-of-using-resource-manager"></a>Vantaggi dell'utilizzo di Gestione risorse
Gestione risorse offre numerosi vantaggi:

* È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.
* È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.
* È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.
* È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.
* è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.
* È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.
* È possibile ottenere informazioni dettagliate sulla fatturazione per l'organizzazione visualizzando i costi di un gruppo di risorse che condividono lo stesso tag.  

Gestione risorse offre un nuovo modo per distribuire e gestire le soluzioni. Per informazioni sulle modifiche introdotte rispetto al modello di distribuzione precedente, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](resource-manager-deployment-model.md).

## <a name="consistent-management-layer"></a>Livello di gestione coerente
Resource Manager fornisce un livello di gestione coerente per le attività eseguite tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, il portale di Azure, l'API REST e gli strumenti di sviluppo. Tutti gli strumenti usano un insieme comune di operazioni. È possibile usare gli strumenti più adatti alle proprie esigenze e spostarsi rapidamente tra gli strumenti senza confusione. 

La figura seguente mostra come tutti gli strumenti interagiscono con la stessa API di Azure Resource Manager. L'API passa le richieste al servizio Resource Manager, che autentica e autorizza le richieste. Resource Manager indirizza le richieste ai provider di risorse appropriati.

![Modello di richiesta di Resource Manager](./media/resource-group-overview/consistent-management-layer.png)

## <a name="guidance"></a>Indicazioni
I suggerimenti seguenti consentono di sfruttare al meglio Resource Manager per le proprie soluzioni.

1. Definire e distribuire l'infrastruttura tramite la sintassi dichiarativa nei modelli di Gestione risorse, anziché tramite comandi imperativi.
2. Definire tutti i passaggi di distribuzione e configurazione nel modello. Per la configurazione della soluzione, è consigliabile evitare procedure manuali.
3. Eseguire i comandi imperativi per gestire le risorse, ad esempio per avviare o arrestare un'app o un computer.
4. Includere le risorse con lo stesso ciclo di vita in un gruppo di risorse. Usare le categorie per tutte le altre attività di organizzazione delle risorse.

Per altri suggerimenti sui modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](resource-manager-template-best-practices.md).

Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

## <a name="resource-groups"></a>Gruppi di risorse
Esistono alcuni fattori importanti da considerare quando si definisce il gruppo di risorse:

1. Tutte le risorse del gruppo devono condividere lo stesso ciclo di vita. Le risorse vengono distribuite, aggiornate ed eliminate insieme. Se una risorsa, ad esempio un server di database, deve esistere in un ciclo di distribuzione diverso deve essere inclusa in un altro gruppo di risorse.
2. Ogni risorsa può appartenere a un solo gruppo di risorse.
3. È possibile aggiungere o rimuovere una risorsa in un gruppo di risorse in qualsiasi momento.
4. È possibile spostare una risorsa da un gruppo di risorse a un altro. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
5. Un gruppo di risorse può contenere le risorse che risiedono in aree diverse.
6. Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative.
7. Una risorsa può interagire con le risorse di altri gruppi di risorse. Questa interazione è comune quando le due risorse sono correlate, ma non condividono lo stesso ciclo di vita, ad esempio app Web che si connettono a un database.

Quando si crea un gruppo di risorse è necessario specificarne il percorso. Perché un gruppo di risorse necessita di un percorso? E se le risorse possono avere percorsi diversi rispetto al gruppo di risorse, perché il percorso del gruppo di risorse è importante? Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

## <a name="resource-providers"></a>Provider di risorse
Ogni provider di risorse offre una serie di risorse e operazioni per l'uso di un servizio di Azure. Per archiviare chiavi e segreti sarà ad esempio necessario usare il provider di risorse **Microsoft.KeyVault** . Questo provider di risorse offre un tipo di risorsa denominato **vaults** per creare l'insieme di credenziali delle chiavi e un tipo di risorsa denominato **vaults/secrets** per creare un segreto nell'insieme di credenziali delle chiavi. 

Prima di iniziare con la distribuzione delle risorse è necessario comprendere i provider di risorse disponibili. Conoscere i nomi dei provider di risorse e delle risorse consente di definire le risorse da distribuire in Azure.

È possibile visualizzare tutti i provider di risorse tramite il portale. Nel pannello relativo alla sottoscrizione selezionare **Provider di risorse**:

![visualizzare i provider di risorse](./media/resource-group-overview/view-resource-providers.png)

È possibile recuperare tutti i provider di risorse con il cmdlet di PowerShell seguente:

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Con l'interfaccia della riga di comando di Azure è invece possibile recuperare tutti i provider di risorse con il comando seguente:

```azurecli
azure provider list
```

È possibile esaminare l'elenco restituito per trovare i provider di risorse da usare.

Per ottenere informazioni dettagliate su un provider di risorse, aggiungere lo spazio dei nomi del provider al comando. Il comando restituisce i tipi di risorse supportati per il provider di risorse, oltre alle posizioni e alle versioni API supportate per ogni tipo di risorsa. Il cmdlet di PowerShell seguente consente di ottenere informazioni dettagliate su Microsoft.Compute:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

In alternativa, usare l'interfaccia della riga di comando di Azure per recuperare i tipi di risorse, le posizioni e le versioni API supportati per Microsoft.Compute con il comando seguente:

```azurecli
azure provider show Microsoft.Compute --json > c:\Azure\compute.json
```

Per altre informazioni, vedere [Provider, aree, versioni API e schemi di Resource Manager](resource-manager-supported-services.md).

## <a name="template-deployment"></a>Distribuzione del modello
Resource Manager consente di creare un modello in formato JSON che definisce l'infrastruttura e la configurazione della soluzione di Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Quando si crea una soluzione dal portale, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero perché è possibile iniziare con il modello per la soluzione e personalizzarlo per soddisfare esigenze specifiche. È possibile recuperare un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse oppure visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il [modello esportato](resource-manager-export-template.md).

Per altre informazioni sul formato del modello e sulla sua costruzione, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md) e [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).

Resource Manager elabora il modello come qualsiasi altra richiesta. Vedere la figura per il [livello di gestione coerente](#consistent-management-layer). Il modello analizza e converte la sintassi in operazioni dell'API REST per i provider di risorse appropriati. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

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

Non è tuttavia necessario definire l'intera infrastruttura in un singolo modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/resource-group-overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/resource-group-overview/tier-templates.png)

Per altre informazioni sulla progettazione di modelli, vedere [Procedure consigliate per la progettazione di modelli di Azure Resource Manager](best-practices-resource-manager-design-templates.md). Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

Per una serie di quattro parti sull'automazione della distribuzione, vedere [Automazione della distribuzione di applicazioni nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa serie illustra argomenti come architettura, accesso e sicurezza, disponibilità, scalabilità e distribuzione delle applicazioni.

Azure Resource Manager analizza le dipendenze per far sì che le risorse vengano create nell'ordine corretto. Se una risorsa si basa sul valore di un'altra risorsa, ad esempio una macchina virtuale che richiede un account di archiviazione per i dischi, impostare una dipendenza. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. È ad esempio possibile aggiungere una risorsa alla soluzione e quindi aggiungere regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una risorsa, ma la risorsa esiste già, Azure Resource Manager esegue un aggiornamento anziché creare un nuovo asset. Gestione risorse di Azure aggiorna l'asset esistente allo stesso stato di quelli nuovi.  

In Gestione risorse sono disponibili estensioni utili negli scenari che richiedono operazioni aggiuntive, ad esempio l'installazione di software specifico non incluso nella configurazione. Se si utilizza già un servizio di gestione configurazione, ad esempio DSC, Chef o Puppet, è possibile continuare a usare il servizio utilizzando le estensioni. Per informazioni sulle estensioni delle macchine virtuali, vedere [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Infine, il modello diventa parte del codice sorgente per l'applicazione. È possibile archiviarlo nel repository del codice sorgente e aggiornarlo con l'evoluzione dell'applicazione. È possibile modificare il modello tramite Visual Studio.

Dopo aver definito il modello è possibile distribuire le risorse in Azure. Per i comandi per la distribuzione delle risorse, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md)
* [Distribuire le risorse con i modelli e l'API REST di Resource Manager](resource-group-template-deploy-rest.md)

## <a name="tags"></a>Tag
Gestione risorse di Azure offre una funzionalità di categorizzazione che consente di suddividere le risorse in categorie in base ai requisiti di gestione o fatturazione. Usare i tag quando si ha un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse nel modo più razionale. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto. Senza tag è possibile che gli utenti dell'organizzazione creino più risorse che possono risultare difficili da identificare e gestire in un secondo momento. Si supponga ad esempio di voler eliminare tutte le risorse per un progetto specifico. Se le risorse non hanno tag per il progetto sarà necessario trovarle manualmente. L'assegnazione di tag può essere un modo importante per ridurre i costi non necessari nella sottoscrizione. 

Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. È possibile creare una propria tassonomia di tag per assicurarsi che tutti gli utenti dell'organizzazione utilizzino tag comuni anziché applichino inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

L'esempio seguente illustra un tag applicato a una macchina virtuale.

```json
"resources": [    
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2015-06-15",
    "name": "SimpleWindowsVM",
    "location": "[resourceGroup().location]",
    "tags": {
        "costCenter": "Finance"
    },
    ...
  }
]
```

Per recuperare tutte le risorse con un valore di tag, usare il cmdlet di PowerShell seguente:

```powershell
Find-AzureRmResource -TagName costCenter -TagValue Finance
```

In alternativa, usare il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli
azure resource list -t costCenter=Finance --json
```

È anche possibile visualizzare le risorse con tag tramite il portale di Azure.

Il [report di uso](../billing/billing-understand-your-bill.md) della sottoscrizione include valori e nomi di tag per suddividere i costi per tag. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## <a name="access-control"></a>Controllo di accesso
Gestione risorse di Azure consente di controllare gli utenti autorizzati ad accedere ad azioni specifiche per l'organizzazione. Integra in modo nativo il controllo degli accessi in base al ruolo nella piattaforma di gestione e applica tale controllo degli accessi a tutti i servizi nel gruppo di risorse. 

È necessario comprendere due concetti principali quando si usa il controllo degli accessi in base al ruolo:

* Le definizioni di ruolo: descrivono un insieme di autorizzazioni e possono essere usate in più assegnazioni.
* Le assegnazioni di ruolo: una definizione viene assegnata a un'identità (utente o gruppo) per un particolare ambito (sottoscrizione, gruppo di risorse o risorsa). L'assegnazione viene ereditata dagli ambiti inferiori.

È possibile aggiungere utenti alla piattaforma predefinita e ai ruoli specifici delle risorse. È ad esempio possibile sfruttare il ruolo predefinito denominato Lettore che consente agli utenti di visualizzare le risorse, ma non di modificarle. È possibile aggiungere al ruolo Lettore gli utenti dell'organizzazione che richiedono questo tipo di accesso e applicare il ruolo alla sottoscrizione, al gruppo di risorse o alla risorsa.

Azure offre i quattro ruoli di piattaforma seguenti:

1. Proprietario: può gestire tutto, compresi gli accessi
2. Collaboratore: può gestire tutto ad eccezione degli accessi
3. Lettore: può visualizzare tutto, ma non apportare modifiche
4. Amministratore Accesso utenti: può gestire l'accesso degli utenti alle risorse di Azure

Azure offre anche diversi ruoli specifici delle risorse. Alcuni ruoli comuni sono i seguenti:

1. Collaboratore Macchina virtuale: può gestire le macchine virtuali, ma non può concedere l'accesso alle macchine virtuali e non può gestire la rete virtuale o l'account di archiviazione a cui sono connesse
2. Collaboratore Rete: può gestire tutte le risorse di rete, ma non concedere l'accesso a tali risorse
3. Collaboratore Account di archiviazione: può gestire gli account di archiviazione, ma non concedere l'accesso a tali account
4. Collaboratore SQL Server: può gestire server e database SQL, ma non i criteri di sicurezza correlati
5. Collaboratore Sito Web: può gestire i siti Web, ma non i piani Web cui sono connessi

Per l'elenco completo dei ruoli e delle azioni consentite, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md). 

In alcuni casi si vuole eseguire un codice o uno script che accede alle risorse, ma non con le credenziali di un utente. Si vuole invece creare un'identità chiamata "entità servizio" per l'applicazione e assegnare il ruolo appropriato per l'entità servizio. Resource Manager consente di creare le credenziali per l'applicazione e di autenticarla a livello di codice. Per altre informazioni sulla creazione di entità servizio, vedere uno degli argomenti seguenti:

* [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](resource-group-authenticate-service-principal.md)
* [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](resource-group-authenticate-service-principal-cli.md)
* [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](resource-group-create-service-principal-portal.md)

È anche possibile bloccare in modo esplicito le risorse critiche per impedire agli utenti di eliminarle o modificarle. Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="activity-logs"></a>Log attività
Resource Manager registra tutte le operazioni che creano, modificano o eliminano una risorsa. È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa. Per visualizzare i log, selezionare **Log attività** nel pannello **Impostazioni** di un gruppo di risorse. I log possono essere filtrati in base a molti valori diversi, incluso l'utente che ha avviato l'operazione. Per informazioni sull'uso dei log attività, vedere [Visualizzare i log attività per gestire le risorse di Azure](resource-group-audit.md).

## <a name="customized-policies"></a>Criteri personalizzati
Gestione risorse consente di creare criteri personalizzati per gestire le risorse. I tipi di criteri creati possono includere diversi scenari. È possibile applicare una convenzione di denominazione alle risorse e limitare i tipi e le istanze di risorse che possono essere distribuiti o le aree che possono ospitare un tipo di risorsa. È possibile richiedere l'applicazione di un valore di tag per le risorse per organizzare la fatturazione per i reparti. Creare criteri per ridurre i costi e mantenere la coerenza della sottoscrizione. 

Definire criteri con JSON e quindi applicarli nell'intera sottoscrizione o all'interno di un gruppo di risorse. I criteri sono diversi dal controllo degli accessi in base al ruolo perché vengono applicati ai tipi di risorse.

L'esempio seguente illustra un criterio che garantisce la coerenza dei tag specificando che tutte le risorse debbano includere un tag costCenter.

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

È possibile creare molti altri tipi di criteri. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## <a name="sdks"></a>SDK
Azure SDK sono disponibili per più linguaggi e piattaforme.
Ogni implementazione del linguaggio è disponibile tramite Gestione pacchetti del relativo ecosistema e in GitHub.

Il codice in ogni SDK viene generato dalle specifiche dell'API RESTful di Azure.
Queste specifiche sono open source e si basano sulla specifica Swagger 2.0.
Il codice degli SDK viene generato con un progetto open source denominato AutoRest.
AutoRest trasforma le specifiche delle API basate su REST in librerie client in più linguaggi.
Se si vogliono migliorare alcuni aspetti del codice generato negli SDK, l'intero set di strumenti per creare gli SDK è aperto, disponibile gratuitamente e basato su un formato di specifica API ampiamente diffuso.

Di seguito sono riportati i repository SDK open source. Sono graditi commenti e suggerimenti, segnalazioni di problemi e richieste pull.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [!NOTE]
> Se l'SDK non offre le funzionalità necessarie, è anche possibile chiamare direttamente l' [API REST di Azure](https://docs.microsoft.com/rest/api/resources/) .
> 
> 

## <a name="samples"></a>Esempi
### <a name="net"></a>.NET
* [Manage Azure resources and resource groups (Gestire risorse e gruppi di risorse di Azure)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template (Distribuire una macchina virtuale abilitata per SSH con un modello)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### <a name="java"></a>Java
* [Manage Azure resources (Gestire risorse di Azure)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
* [Manage Azure resource groups (Gestire gruppi di risorse di Azure)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
* [Deploy an SSH Enabled VM with a Template (Distribuire una macchina virtuale abilitata per SSH con un modello)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### <a name="nodejs"></a>Node.js
* [Manage Azure resources and resource groups (Gestire risorse e gruppi di risorse di Azure)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template (Distribuire una macchina virtuale abilitata per SSH con un modello)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### <a name="python"></a>Python
* [Manage Azure resources and resource groups (Gestire risorse e gruppi di risorse di Azure)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template (Distribuire una macchina virtuale abilitata per SSH con un modello)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### <a name="ruby"></a>Ruby
* [Manage Azure resources and resource groups (Gestire risorse e gruppi di risorse di Azure)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template (Distribuire una macchina virtuale abilitata per SSH con un modello)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Oltre che in questi esempi, è possibile cercare negli esempi della raccolta.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## <a name="next-steps"></a>Passaggi successivi
* Per una semplice introduzione all'uso dei modelli, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
* Per istruzioni dettagliate sulla creazione di un modello, vedere [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).
* Per comprendere le funzioni che è possibile usare in un modello, vedere [Funzioni del modello](resource-group-template-functions.md)
* Per informazioni sull'uso di Visual Studio con Resource Manager, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
* Per informazioni sull'uso di Visual Studio Code con Resource Manager, vedere [Uso dei modelli di Azure Resource Manager in Visual Studio Code](resource-manager-vs-code.md).

Ecco una dimostrazione video di questa panoramica:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]


[powershellref]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/azurerm.resources



<!--HONumber=Jan17_HO2-->


