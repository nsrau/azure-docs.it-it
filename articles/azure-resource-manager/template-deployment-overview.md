---
title: Modelli di Gestione risorse di Azure
description: Viene descritto come usare i modelli di Azure Resource Manager per la distribuzione delle risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390729"
---
# <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Con il passaggio al cloud, molti team hanno adottato metodi di sviluppo agile. Questi team eseguono un'iterazione rapida. È necessario distribuire in modo più frequente e affidabile le soluzioni nel cloud. La divisione tra le operazioni e lo sviluppo è scomparsa perché i team hanno la necessità di gestire l'infrastruttura e il codice sorgente in un unico processo.

Una soluzione a questi problemi consiste nell'automatizzare la distribuzione e utilizzare la pratica dell'infrastruttura come codice. Usare il codice per definire gli elementi da distribuire e gestire il codice tramite lo stesso processo del codice sorgente. L'infrastruttura viene archiviata come codice in un repository di origine e ne viene configurata la versione.

Azure Resource Manager consente di implementare l'infrastruttura come codice tramite modelli Gestione risorse. Il modello è un file JavaScript Object Notation (JSON) che contiene l'infrastruttura e la configurazione per la soluzione Azure. Il modello usa la sintassi dichiarativa, che consente di indicare lo stato che si intende distribuire senza dover scrivere la sequenza di comandi di programmazione per crearla. Nel modello si specificano le risorse da distribuire e le proprietà di tali risorse.

## <a name="benefits-of-resource-manager-templates"></a>Vantaggi dei modelli di Gestione risorse

I modelli Gestione risorse offrono diversi vantaggi. È possibile:

* Distribuire, gestire e monitorare tutte le risorse per la soluzione come gruppo, anziché gestire singolarmente queste risorse.

* Distribuire ripetutamente la soluzione nel corso del ciclo di vita di sviluppo e avere la certezza che le risorse vengano distribuite in uno stato coerente.

* Gestire l'infrastruttura tramite modelli dichiarativi anziché script.

* Definire le dipendenze tra le risorse in modo che vengano distribuite nell'ordine corretto.

* Sfrutta i vantaggi delle nuove versioni e dei servizi immediatamente perché non sono necessarie operazioni intermedie da parte di altre parti.

## <a name="template-file"></a>File modello

All'interno del modello è possibile scrivere [espressioni di modello](template-expressions.md) che estendono le funzionalità di JSON. Queste espressioni usano le [funzioni](resource-group-template-functions.md) fornite da Gestione risorse.

Il modello include le sezioni seguenti:

* [Parameters](template-parameters.md) : specificare i valori durante la distribuzione che consentono di utilizzare lo stesso modello con ambienti diversi.

* [Variabili](template-variables.md) : definire i valori usati nei modelli.

* [Funzioni definite dall'utente](template-user-defined-functions.md) : creare funzioni personalizzate che semplificano il modello.

* [Risorse](resource-group-authoring-templates.md#resources) : specificare le risorse da distribuire.

* [Output](template-outputs.md) : valori restituiti dalle risorse distribuite.

## <a name="dependencies"></a>Dependencies

Azure Resource Manager analizza le dipendenze per far sì che le risorse vengano create nell'ordine corretto. Se una risorsa si basa sul valore di un'altra risorsa, ad esempio una macchina virtuale che richiede un account di archiviazione per i dischi, impostare una dipendenza. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

## <a name="conditional-deployment"></a>Distribuzione condizionale

È possibile aggiungere una risorsa al modello e, facoltativamente, distribuirla. In genere, si passa un valore di parametro che indica se la risorsa deve essere distribuita. Per ulteriori informazioni, vedere [distribuzione condizionale nei modelli gestione risorse](conditional-resource-deployment.md).

## <a name="create-multiple-instances"></a>Creare più istanze

Anziché ripetere i blocchi di JSON più volte nel modello, è possibile usare un elemento Copy per specificare più di un'istanza di una variabile, una proprietà o una risorsa. Per ulteriori informazioni, vedere la pagina relativa [all'iterazione di risorse, proprietà o variabili nei modelli Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Esporta modelli

È possibile ottenere un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse o visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il [modello esportato](export-template-portal.md).

Quando si crea una soluzione dal portale, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero, perché è possibile iniziare con il modello della soluzione e personalizzarlo per soddisfare esigenze specifiche. Per un esempio completo, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Distribuzione modelli processo

Quando si distribuisce un modello, Gestione risorse analizza il modello e ne converte la sintassi in operazioni API REST. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

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

Se la risorsa esiste già nel gruppo di risorse e la richiesta non contiene aggiornamenti per le proprietà, non viene eseguita alcuna azione. Se la risorsa esiste ma le proprietà sono state modificate, viene aggiornata la risorsa esistente. Se la risorsa non esiste, viene creata la nuova risorsa. Questo approccio rende sicuro la ridistribuzione di un modello e il fatto che le risorse rimangano in uno stato coerente.

## <a name="template-design"></a>Progettazione modello

La modalità di definizione dei modelli e dei gruppi di risorse è una scelta personale e dipende dalla modalità di gestione della soluzione preferita. Ad esempio, è possibile distribuire l'applicazione a tre livelli tramite un unico modello in un singolo gruppo di risorse.

![modello a tre livelli](./media/template-deployment-overview/3-tier-template.png)

Non è tuttavia necessario definire l'intera infrastruttura in un unico modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/template-deployment-overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/template-deployment-overview/tier-templates.png)

Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà nei file di modello, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per iniziare a sviluppare un modello, vedere [usare Visual Studio Code per creare modelli Azure Resource Manager](resource-manager-tools-vs-code.md).


