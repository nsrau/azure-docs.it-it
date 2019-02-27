---
title: Variabili del modello di Azure Resource Manager | Microsoft Docs
description: Descrive come definire le variabili dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 50feca90d375d6afd3b04afe019ad9f9025f19dc
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308572"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Sezione variables dei modelli di Azure Resource Manager
Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili. Queste tuttavia consentono spesso di semplificare il modello, riducendo le espressioni complesse.

## <a name="define-and-use-a-variable"></a>Definire e usare una variabile

L'esempio seguente illustra la definizione di una variabile: Crea un valore stringa per il nome di un account di archiviazione. Usa diverse funzioni di modello per ottenere un valore di parametro e concatenarlo a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

La variabile viene usata quando si definisce la risorsa.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Definizioni disponibili

L'esempio precedente illustra un modo per definire una variabile. È possibile usare una delle definizioni seguenti:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": <object-or-value-to-repeat>
        }
    ]
}
```

## <a name="configuration-variables"></a>Variabili di configurazione

È possibile usare i tipi JSON complessi per definire i valori associati a un ambiente. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

Nei parametri, creare un valore che indichi i valori di configurazione da usare.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Recuperare le impostazioni correnti con:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Usare l'elemento di copia nella definizione della variabile

Per creare più istanze di una variabile, usare la proprietà `copy` nella sezione variables. Si crea una matrice di elementi costruita dal valore della proprietà `input`. È possibile usare la proprietà `copy` all'interno di una variabile o al livello superiore della sezione variables. Quando si usa `copyIndex` all'interno di un'iterazione delle variabili, è necessario specificare il nome dell'iterazione.

L'esempio seguente illustra come usare copy:

```json
"variables": {
  "disk-array-on-object": {
    "copy": [
      {
        "name": "disks",
        "count": 3,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks')]"
        }
      }
    ]
  },
  "copy": [
    {
      "name": "disks-top-level-array",
      "count": 3,
      "input": {
        "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
        "diskSizeGB": "1",
        "diskIndex": "[copyIndex('disks-top-level-array')]"
      }
    },
    {
      "name": "top-level-string-array",
      "count": 5,
      "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
    }
  ]
},
```

Dopo che l'espressione copy è stata valutata, la variabile **disk-array-on-object** contiene l'oggetto seguente con una matrice denominata **disks**:

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

La variabile **disks-top-level-array** contiene la matrice seguente:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

La variabile **top-level-string-array** contiene la matrice seguente:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

L'uso di copy funziona bene quando occorre associare i valori dei parametri a quelli delle risorse. L'esempio seguente formatta i valori dei parametri da usare nella definizione delle regole di sicurezza:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>Modelli di esempio

Questi modelli di esempio illustrano alcuni scenari per l'uso delle variabili. Distribuirli per testare il modo in cui le variabili vengono gestite in scenari diversi. 

|Modello  |DESCRIZIONE  |
|---------|---------|
| [definizioni delle variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Illustra i diversi tipi di variabili. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [variabile di configurazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustra l'uso di una variabile che definisce i valori di configurazione. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [regole di sicurezza della rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [file dei parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crea una matrice nel formato corretto per assegnare le regole di sicurezza a un gruppo di sicurezza della rete. |


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per altri suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions-resource.md#resourceid).