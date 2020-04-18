---
title: Definire più istanze di un valore di outputDefine multiple instances of an output value
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire più volte la restituzione di un valore da una distribuzione.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617829"
---
# <a name="output-iteration-in-arm-templates"></a>Iterazione di output nei modelli ARMOutput iteration in ARM templates

Questo articolo illustra come creare più di un valore per un output nel modello di Azure Resource Manager (ARM). Aggiungendo l'elemento **copy** alla sezione outputs del modello, è possibile restituire dinamicamente un numero di elementi durante la distribuzione.

È inoltre possibile utilizzare la copia con [risorse](copy-resources.md), [le proprietà in una risorsa](copy-properties.md)e le [variabili](copy-variables.md).

## <a name="outputs-iteration"></a>Iterazione output

L'elemento copy ha il seguente formato generale:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

La proprietà **count** specifica il numero di iterazioni desiderato per il valore di output.

La proprietà **input** specifica le proprietà che si desidera ripetere. Creare una matrice di elementi costruiti dal valore nella proprietà **di input.** Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

L'esempio seguente crea un numero variabile di account di archiviazione e restituisce un endpoint per ogni account di archiviazione:The following example creates a variable number of storage number of storage accounts and returns an endpoint for each storage account:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Il modello precedente restituisce una matrice con i valori seguenti:The preceding template returns an array with the following values:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

L'esempio seguente restituisce tre proprietà dai nuovi account di archiviazione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Nell'esempio precedente viene restituita una matrice con i valori seguenti:The preceding example returns an array with the following values:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Esercitazione: creare più istanze](template-tutorial-create-multiple-instances.md)di risorse utilizzando i modelli ARM .
* Per altri utilizzi dell'elemento copy, vedere:
  * [Iterazione delle risorse nei modelli ARMResource iteration in ARM templates](copy-resources.md)
  * [Iterazione delle proprietà nei modelli ARMProperty iteration in ARM templates](copy-properties.md)
  * [Iterazione variabile nei modelli ARMVariable iteration in ARM templates](copy-variables.md)
* Per informazioni sulle sezioni di un modello, vedere Creazione di [modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [Distribuire un'applicazione con il modello ARM.](deploy-powershell.md)

