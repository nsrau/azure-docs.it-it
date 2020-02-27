---
title: Definire più istanze di una proprietà
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire un'iterazione più volte durante la creazione di una proprietà in una risorsa.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b759389cd1065c399658bd8d0c1ddd263054697c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622869"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Iterazione proprietà nei modelli di Azure Resource Manager

Questo articolo illustra come creare più di un'istanza di una proprietà nel modello di Azure Resource Manager. Aggiungendo l'elemento **Copy** alla sezione Properties di una risorsa nel modello, è possibile impostare dinamicamente il numero di elementi per una proprietà durante la distribuzione. È anche possibile evitare di ripetere la sintassi del modello.

È anche possibile usare copia con [risorse](copy-resources.md), [variabili](copy-variables.md)e [output](copy-outputs.md).

## <a name="property-iteration"></a>Iterazione delle proprietà

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Per **nome**specificare il nome della proprietà della risorsa che si desidera creare. La proprietà **count** specifica il numero di iterazioni desiderate per la proprietà.

La proprietà di **input** specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruiti dal valore nella proprietà di **input** .

Nell'esempio seguente viene illustrato come applicare `copy` alla proprietà dataDisks in una macchina virtuale:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Si noti che quando si usa `copyIndex` all'interno di un'iterazione di proprietà, è necessario specificare il nome dell'iterazione.

> [!NOTE]
> L'iterazione della proprietà supporta anche un argomento offset. L'offset deve essere successivo al nome dell'iterazione, ad esempio copyIndex (' datadisks ', 1).
>

Resource Manager espande la matrice `copy` durante la distribuzione. Il nome della matrice diventa il nome della proprietà. I valori di input diventano le proprietà dell'oggetto. Il modello distribuito diventa:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

L'elemento di copia è una matrice, pertanto è possibile specificare più di una proprietà per una risorsa.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

È possibile usare l'iterazione di risorse e di proprietà contemporaneamente. Fare riferimento all'iterazione di proprietà con il nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Se si distribuisce un modello con Azure PowerShell 2,6 o versioni successive, l'interfaccia della riga di comando di Azure 2.0.74 o versione successiva o l'API REST versione **2019-05-10** o successiva, è possibile impostare Count su zero. Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

## <a name="example-templates"></a>Modelli di esempio

Nell'esempio seguente viene illustrato uno scenario comune per la creazione di più di un valore per una proprietà.

|Modello  |Descrizione  |
|---------|---------|
|[Distribuzione VM con un numero variabile di dischi dati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuisce più dischi dati con una macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Tutorial: create multiple resource instances using Resource Manager templates](template-tutorial-create-multiple-instances.md) (Esercitazione: Creare più istanze di risorse usando i modelli di Resource Manager).
* Per altri usi dell'elemento Copy, vedere:
  * [Iterazione delle risorse nei modelli di Azure Resource Manager](copy-resources.md)
  * [Iterazione variabile nei modelli di Azure Resource Manager](copy-variables.md)
  * [Iterazione di output nei modelli di Azure Resource Manager](copy-outputs.md)
* Per altre informazioni sulle sezioni di un modello, vedere [Authoring Azure Resource Manager Templates](template-syntax.md) (Creazione di modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](deploy-powershell.md).

