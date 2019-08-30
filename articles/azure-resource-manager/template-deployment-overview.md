---
title: Modelli di Gestione risorse di Azure
description: Viene descritto come usare i modelli di Azure Resource Manager per la distribuzione delle risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166524"
---
# <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Con Azure Resource Manager è possibile creare modelli che definiscono quello che si vuole distribuire in Azure. Il modello è un file JavaScript Object Notation (JSON) che contiene l'infrastruttura e la configurazione della soluzione di Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

Il modello usa la sintassi dichiarativa, che consente di indicare lo stato che si intende distribuire senza dover scrivere la sequenza di comandi di programmazione per crearla. Nel modello si specificano le risorse da distribuire e le proprietà di tali risorse.

## <a name="benefits-of-resource-manager-templates"></a>Vantaggi dei modelli di Gestione risorse

I modelli Gestione risorse offrono diversi vantaggi:

* È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.

* È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

* È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.

* È possibile definire le dipendenze tra risorse in modo che vengano distribuite nell'ordine corretto.

I suggerimenti seguenti consentono di sfruttare al meglio Resource Manager per le proprie soluzioni.

* Definire e distribuire l'infrastruttura tramite la sintassi dichiarativa nei modelli di Gestione risorse, anziché tramite comandi imperativi.

* Definire tutti i passaggi di distribuzione e configurazione nel modello. Per la configurazione della soluzione, è consigliabile evitare procedure manuali.

* Eseguire i comandi imperativi per gestire le risorse, ad esempio per avviare o arrestare un'app o un computer.

* Seguire [Azure Resource Manager procedure consigliate](template-best-practices.md)per il modello.

## <a name="template-deployment-process"></a>Distribuzione modelli processo

Quando si distribuisce un modello, Gestione risorse analizza il modello e ne converte la sintassi in operazioni API REST. Invia tali operazioni ai provider di risorse appropriati. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

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

## <a name="template-structure"></a>Struttura del modello

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

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui file modello, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).

Dopo aver definito il modello è possibile distribuire le risorse in Azure. Per distribuire le risorse, vedere:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md)
* [Distribuire le risorse con i modelli e l'API REST di Resource Manager](resource-group-template-deploy-rest.md)

