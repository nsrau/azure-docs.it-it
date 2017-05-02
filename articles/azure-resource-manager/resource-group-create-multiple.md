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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Distribuire più istanze delle risorse nei modelli di Azure Resource Manager
In questo argomento viene illustrato come eseguire un'iterazione del modello di Gestione risorse di Azure per creare più istanze di una risorsa.

## <a name="copy-and-copyindex"></a>copy e copyIndex
La risorsa da ricreare più volte assume il formato seguente:

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Si noti che il numero di iterazioni da eseguire è specificato nell'oggetto copia:

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

Il valore del conteggio deve essere un numero intero positivo e non può essere maggiore di 800.

Si noti che il nome di ogni risorsa include la funzione `copyIndex()` che restituisce l'iterazione corrente nel ciclo.

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

Se si distribuiscono tre siti Web, essi sono denominati:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione copyIndex(), ad esempio `copyIndex(1)`. Il numero di iterazioni da eseguire viene comunque specificato nell'elemento copia, ma il valore di copyIndex è compensato dal valore specificato. Quindi, usando lo stesso modello dell'esempio precedente, ma specificando copyIndex(1), si distribuirebbero tre siti Web denominati:

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager crea le risorse in parallelo. Pertanto l'ordine di creazione non è garantito. Per creare risorse iterate in sequenza, vedere [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md) (Ciclo sequenziale per i modelli di Azure Resource Manager). 

L'oggetto copia può essere applicato solo a una risorsa di livello principale, mentre non è possibile applicarlo a una proprietà su un tipo di risorsa o a una risorsa figlio. L'esempio di codice seguente mostra in che modo è possibile applicare la copia:

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

Per eseguire l'iterazione di una risorsa figlio, vedere [Create multiple instances of a child resource](#create-multiple-instances-of-a-child-resource) (Creare più istanze di una risorsa figlio).

Anche se non è possibile applicare la funzione copy a una proprietà, tale proprietà rimane parte delle iterazioni della risorsa che la contiene. Pertanto, è possibile usare copyIndex() all'interno della proprietà per specificare i valori. Per creare più valori per una proprietà, vedere [Create multiple instances of property on resource type](resource-manager-property-copy.md) (Creare più istanze di proprietà sul tipo di risorsa).

## <a name="use-copy-with-array"></a>Usare l'oggetto copy con matrice
L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Per distribuire tre siti Web denominati:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Usare il modello seguente:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Si noti che la funzione `length` viene usata per specificare il numero. Fornire la matrice come parametro alla funzione length.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

## <a name="depend-on-resources-in-a-loop"></a>In base alle risorse in un ciclo
L'elemento `dependsOn` consente di specificare che una risorsa sia distribuita dopo un'altra. Per distribuire una risorsa che dipende dalla raccolta di risorse in un ciclo, usare il nome del ciclo di copia nell'elemento dependsOn. L'esempio seguente illustra come distribuire tre account di archiviazione prima di distribuire la macchina virtuale. La definizione completa di macchina virtuale non viene visualizzata. Si noti che la copia ha la proprietà name impostata su `storagecopy` e l'elemento dependsOn per le macchine virtuali impostato su `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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


