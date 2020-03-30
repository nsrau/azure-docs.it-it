---
title: Definire più istanze di una variabileDefine multiple instances of a variable
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire più iterazioni durante la creazione di una variabile.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153302"
---
# <a name="variable-iteration-in-arm-templates"></a>Iterazione variabile nei modelli ARMVariable iteration in ARM templates

Questo articolo illustra come creare più di un valore per una variabile nel modello di Azure Resource Manager (ARM). Aggiungendo l'elemento **copy** alla sezione variables del modello, è possibile impostare dinamicamente il numero di elementi per una variabile durante la distribuzione. Si evita inoltre di dover ripetere la sintassi del modello.

È inoltre possibile utilizzare la copia con [risorse](copy-resources.md), [le proprietà in una risorsa](copy-properties.md)e gli [output](copy-outputs.md).

## <a name="variable-iteration"></a>Iterazione delle variabili

L'elemento copy ha il seguente formato generale:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

La proprietà **name** è qualsiasi valore che identifica il ciclo. La proprietà **count** specifica il numero di iterazioni desiderato per la variabile.

La proprietà **input** specifica le proprietà che si desidera ripetere. Creare una matrice di elementi costruiti dal valore nella proprietà **di input.** Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

Nell'esempio seguente viene illustrato come creare una matrice di valori stringa:The following example shows how to create an array of string values:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Il modello precedente restituisce una matrice con i valori seguenti:The preceding template returns an array with the following values:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

Nell'esempio seguente viene illustrato come creare una matrice di oggetti con tre proprietà: name, diskSizeGB e diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

Nell'esempio precedente viene restituita una matrice con i valori seguenti:The preceding example returns an array with the following values:

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
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> L'iterazione variabile supporta un argomento di offset. L'offset deve venire dopo il nome dell'iterazione, ad esempio copyIndex('diskNames', 1). Se non si specifica un valore di offset, il valore predefinito è 0 per la prima istanza.
>

È inoltre possibile utilizzare l'elemento copy all'interno di una variabile. Nell'esempio seguente viene creato un oggetto che dispone di una matrice come uno dei relativi valori.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Nell'esempio precedente viene restituito un oggetto con i valori seguenti:The preceding example returns an object with the following values:

```json
{
    "sampleProperty": "sampleValue",
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
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

L'esempio seguente mostra i diversi modi in cui è possibile utilizzare la copia con le variabili.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può superare 800.

Il conteggio non può essere un numero negativo. Se si distribuisce un modello con Azure PowerShell 2.6 o versione successiva, l'interfaccia della riga di comando di Azure 2.0.74 o successiva o la versione dell'API REST **2019-05-10** o successiva, è possibile impostare count su zero. Le versioni precedenti di PowerShell, CLI e l'API REST non supportano zero per count.

## <a name="example-templates"></a>Modelli di esempio

Negli esempi seguenti vengono illustrati scenari comuni per la creazione di più di un valore per una variabile.

|Modello  |Descrizione  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Mostra le diverse modalità di iterazione delle variabili. |
|[Più regole di sicurezza](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuisce più regole di sicurezza a un gruppo di sicurezza di rete. Costruisce le regole di sicurezza da un parametro. Per il parametro, vedere il [file di parametri NSG multipli](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Esercitazione: creare più istanze](template-tutorial-create-multiple-instances.md)di risorse utilizzando i modelli ARM .
* Per altri utilizzi dell'elemento copy, vedere:
  * [Iterazione delle risorse nei modelli ARMResource iteration in ARM templates](copy-resources.md)
  * [Iterazione delle proprietà nei modelli ARMProperty iteration in ARM templates](copy-properties.md)
  * [Iterazione di output nei modelli ARMOutput iteration in ARM templates](copy-outputs.md)
* Per informazioni sulle sezioni di un modello, vedere Creazione di [modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [Distribuire un'applicazione con il modello ARM.](deploy-powershell.md)

