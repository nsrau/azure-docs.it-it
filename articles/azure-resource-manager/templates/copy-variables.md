---
title: Definire più istanze di una variabile
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire un'iterazione più volte durante la creazione di una variabile.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ea4caf3553b3cd14eec194b8cef0db59499a4f4c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622873"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Iterazione variabile nei modelli di Azure Resource Manager

Questo articolo illustra come creare più di un valore per una variabile nel modello di Azure Resource Manager. Aggiungendo l'elemento **Copy** alla sezione Variables del modello, è possibile impostare dinamicamente il numero di elementi per una variabile durante la distribuzione. È anche possibile evitare di ripetere la sintassi del modello.

È anche possibile usare copia con [risorse](copy-resources.md), [Proprietà in una risorsa](copy-properties.md)e [output](copy-outputs.md).

## <a name="variable-iteration"></a>Iterazione delle variabili

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

La proprietà **Name** è qualsiasi valore che identifica il ciclo. La proprietà **count** specifica il numero di iterazioni desiderate per la variabile.

La proprietà di **input** specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruiti dal valore nella proprietà di **input** . Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

Nell'esempio seguente viene illustrato come creare una matrice di valori stringa:

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

Il modello precedente restituisce una matrice con i valori seguenti:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

Nell'esempio seguente viene illustrato come creare una matrice di oggetti con tre proprietà: Name, diskSizeGB e diskIndex.

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

Nell'esempio precedente viene restituita una matrice con i valori seguenti:

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
> L'iterazione della variabile supporta un argomento offset. L'offset deve essere successivo al nome dell'iterazione, ad esempio copyIndex (' diskNames ', 1). Se non si specifica un valore di offset, il valore predefinito è 0 per la prima istanza.
>

È anche possibile usare l'elemento Copy all'interno di una variabile. Nell'esempio seguente viene creato un oggetto con una matrice come uno dei relativi valori.

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

Nell'esempio precedente viene restituito un oggetto con i valori seguenti:

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

Nell'esempio seguente vengono illustrati i diversi modi in cui è possibile utilizzare Copy con le variabili.

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

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Se si distribuisce un modello con Azure PowerShell 2,6 o versioni successive, l'interfaccia della riga di comando di Azure 2.0.74 o versione successiva o l'API REST versione **2019-05-10** o successiva, è possibile impostare Count su zero. Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti illustrano scenari comuni per la creazione di più di un valore per una variabile.

|Modello  |Descrizione  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Mostra le diverse modalità di iterazione delle variabili. |
|[Più regole di sicurezza](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuisce più regole di sicurezza a un gruppo di sicurezza di rete. Costruisce le regole di sicurezza da un parametro. Per il parametro, vedere il [file di parametri NSG multipli](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Tutorial: create multiple resource instances using Resource Manager templates](template-tutorial-create-multiple-instances.md) (Esercitazione: Creare più istanze di risorse usando i modelli di Resource Manager).
* Per altri usi dell'elemento Copy, vedere:
  * [Iterazione delle risorse nei modelli di Azure Resource Manager](copy-resources.md)
  * [Iterazione proprietà nei modelli di Azure Resource Manager](copy-properties.md)
  * [Iterazione di output nei modelli di Azure Resource Manager](copy-outputs.md)
* Per altre informazioni sulle sezioni di un modello, vedere [Authoring Azure Resource Manager Templates](template-syntax.md) (Creazione di modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](deploy-powershell.md).

