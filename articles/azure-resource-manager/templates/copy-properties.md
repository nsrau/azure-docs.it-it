---
title: Definire più istanze di una proprietàDefine multiple instances of a property
description: Usare l'operazione di copia in un modello di Azure Resource Manager per eseguire più iterazioni durante la creazione di una proprietà in una risorsa.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391330"
---
# <a name="property-iteration-in-arm-templates"></a>Iterazione delle proprietà nei modelli ARMProperty iteration in ARM templates

Questo articolo illustra come creare più di un'istanza di una proprietà nel modello di Azure Resource Manager (ARM). Aggiungendo l'elemento **copy** alla sezione delle proprietà di una risorsa nel modello, è possibile impostare in modo dinamico il numero di elementi per una proprietà durante la distribuzione. Si evita inoltre di dover ripetere la sintassi del modello.

È inoltre possibile utilizzare copy con [resources](copy-resources.md), [variables](copy-variables.md)e [output .](copy-outputs.md)

## <a name="property-iteration"></a>Iterazione delle proprietà

L'elemento copy ha il seguente formato generale:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Per **nome**, specificare il nome della proprietà della risorsa che si desidera creare.

La proprietà **count** specifica il numero di iterazioni desiderato per la proprietà.

La proprietà **input** specifica le proprietà che si desidera ripetere. Creare una matrice di elementi costruiti dal valore nella proprietà **di input.**

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

Si noti che quando si usa `copyIndex` all'interno di un'iterazione di proprietà, è necessario specificare il nome dell'iterazione. L'iterazione della proprietà supporta anche un argomento di offset. L'offset deve venire dopo il nome dell'iterazione, ad esempio copyIndex('dataDisks', 1).

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

L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Usare la funzione `length` nella matrice per specificare il conteggio per le iterazioni e `copyIndex` per recuperare l'indice corrente nella matrice.

Il modello di esempio seguente crea un gruppo di failover per i database passati come matrice.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

L'elemento di copia è una matrice, pertanto è possibile specificare più di una proprietà per una risorsa.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
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

Il conteggio non può superare 800.

Il conteggio non può essere un numero negativo. Se si distribuisce un modello con Azure PowerShell 2.6 o versione successiva, l'interfaccia della riga di comando di Azure 2.0.74 o successiva o la versione dell'API REST **2019-05-10** o successiva, è possibile impostare count su zero. Le versioni precedenti di PowerShell, CLI e l'API REST non supportano zero per count.

## <a name="example-templates"></a>Modelli di esempio

Nell'esempio seguente viene illustrato uno scenario comune per la creazione di più di un valore per una proprietà.

|Modello  |Descrizione  |
|---------|---------|
|[Distribuzione VM con un numero variabile di dischi dati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuisce più dischi dati con una macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Esercitazione: creare più istanze](template-tutorial-create-multiple-instances.md)di risorse utilizzando i modelli ARM .
* Per altri utilizzi dell'elemento copy, vedere:
  * [Iterazione delle risorse nei modelli ARMResource iteration in ARM templates](copy-resources.md)
  * [Iterazione variabile nei modelli ARMVariable iteration in ARM templates](copy-variables.md)
  * [Iterazione di output nei modelli ARMOutput iteration in ARM templates](copy-outputs.md)
* Per informazioni sulle sezioni di un modello, vedere Creazione di [modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [Distribuire un'applicazione con il modello ARM.](deploy-powershell.md)

