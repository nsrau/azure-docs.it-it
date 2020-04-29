---
title: Definire più istanze di un valore di output
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire un'iterazione più volte quando si restituisce un valore da una distribuzione.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617829"
---
# <a name="output-iteration-in-arm-templates"></a>Iterazione di output nei modelli ARM

Questo articolo illustra come creare più di un valore per un output nel modello di Azure Resource Manager (ARM). Aggiungendo l'elemento **Copy** alla sezione Outputs del modello, è possibile restituire dinamicamente un numero di elementi durante la distribuzione.

È anche possibile usare copia con [risorse](copy-resources.md), [Proprietà in una risorsa](copy-properties.md)e [variabili](copy-variables.md).

## <a name="outputs-iteration"></a>Iterazione degli output

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

La proprietà **count** specifica il numero di iterazioni desiderate per il valore di output.

La proprietà di **input** specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruiti dal valore nella proprietà di **input** . Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

Nell'esempio seguente viene creato un numero variabile di account di archiviazione e viene restituito un endpoint per ogni account di archiviazione:

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

Il modello precedente restituisce una matrice con i valori seguenti:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Nell'esempio seguente vengono restituite tre proprietà dai nuovi account di archiviazione.

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

Nell'esempio precedente viene restituita una matrice con i valori seguenti:

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

* Per eseguire un'esercitazione, vedere [esercitazione: creare più istanze di risorse usando i modelli ARM](template-tutorial-create-multiple-instances.md).
* Per altri usi dell'elemento Copy, vedere:
  * [Iterazione delle risorse nei modelli ARM](copy-resources.md)
  * [Iterazione delle proprietà nei modelli ARM](copy-properties.md)
  * [Iterazione delle variabili nei modelli ARM](copy-variables.md)
* Per informazioni sulle sezioni di un modello, vedere [creazione di modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [distribuire un'applicazione con il modello ARM](deploy-powershell.md).

