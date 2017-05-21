---
title: "Distribuire più istanze delle risorse di Azure | Documentazione Microsoft"
description: "Usare l&quot;operazione di copia e le matrici in un modello di Gestione risorse di Azure per eseguire più iterazioni durante la distribuzione delle risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e98fa067c0ed385fe20f66645311c9fd51cd6456
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Distribuire più istanze di una risorsa o di una proprietà nei modelli di Azure Resource Manager
In questo argomento viene illustrato come eseguire un'iterazione del modello di Gestione risorse di Azure per creare più istanze di una risorsa.

## <a name="resource-iteration"></a>Iterazione delle risorse
Per creare più istanze di un tipo di risorsa, aggiungere un elemento `copy` al tipo di risorsa. Nell'elemento copy si specifica il numero di iterazioni e un nome per questo ciclo. Il valore del conteggio deve essere un numero intero positivo e non può essere maggiore di 800. Resource Manager crea le risorse in parallelo. Pertanto l'ordine di creazione non è garantito. Per creare risorse iterate in sequenza, vedere [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md) (Ciclo sequenziale per i modelli di Azure Resource Manager). 

La risorsa da ricreare più volte assume il formato seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Si noti che il nome di ogni risorsa include la funzione `copyIndex()` che restituisce l'iterazione corrente nel ciclo. `copyIndex()` è in base zero. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex())]",
```

Crea questi nomi:

* storage0
* storage1
* storage2

Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione copyIndex(). Il numero di iterazioni da eseguire viene comunque specificato nell'elemento copia, ma il valore di copyIndex è compensato dal valore specificato. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crea questi nomi:

* storage1
* storage2
* storage3

L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Usare la funzione `length` nella matrice per specificare il conteggio per le iterazioni e `copyIndex` per recuperare l'indice corrente nella matrice. Quindi l'esempio seguente:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Crea questi nomi:

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="depend-on-resources-in-a-loop"></a>In base alle risorse in un ciclo
L'elemento `dependsOn` consente di specificare che una risorsa sia distribuita dopo un'altra. Per distribuire una risorsa che dipende dalla raccolta di risorse in un ciclo, usare il nome del ciclo di copia nell'elemento dependsOn. L'esempio seguente illustra come distribuire tre account di archiviazione prima di distribuire la macchina virtuale. La definizione completa di macchina virtuale non viene visualizzata. Si noti che la copia ha la proprietà name impostata su `storagecopy` e l'elemento dependsOn per le macchine virtuali impostato su `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Creare più istanze di una risorsa figlio
Non è possibile usare un ciclo di copia per una risorsa figlio. Per creare più istanze di una risorsa cosiddetta "annidata" all'interno di un'altra risorsa, è invece necessario creare tale risorsa come una risorsa di livello superiore. La relazione con la risorsa padre si definisce con le proprietà type e name.

Si supponga, ad esempio, di definire in genere un set di dati come una risorsa figlio all'interno di una data factory.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Per creare più istanze di un set di dati, spostarlo all'esterno della data factory. Il set di dati deve essere sullo stesso livello della data factory, di cui è comunque una risorsa figlio. La relazione fra set di dati e data factory viene mantenuta con le proprietà type e name. Poiché non è possibile dedurre il tipo dalla sua posizione nel modello, è necessario specificarne il nome completo nel formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Per stabilire una relazione padre/figlio con un'istanza della data factory, specificare il nome del set di dati che include il nome della risorsa padre. Usare il formato: `{parent-resource-name}/{child-resource-name}`.  

Nell'esempio seguente viene descritta l'implementazione:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle sezioni di un modello, vedere [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md) (Creazione di modelli di Azure Resource Manager).
* Per creare risorse iterate in sequenza, vedere [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md) (Ciclo sequenziale per i modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).


