---
title: Alle variabili del modello di gestione delle risorse Azure | Documenti Microsoft
description: Viene descritto come definire le variabili in un modelli di gestione risorse di Azure utilizzando la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Sezione di variabili dei modelli di gestione risorse di Azure
Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili, che però permettono spesso di semplificare il modello riducendo le espressioni complesse.

## <a name="define-and-use-a-variable"></a>Definire e utilizzare una variabile

Nell'esempio seguente viene illustrata una definizione di variabile. Crea un valore stringa per un nome di account di archiviazione. Usa diverse funzioni di modello per ottenere un valore di parametro e concatenata a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

La variabile viene utilizzata durante la definizione di risorsa.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Definizioni disponibili

Nell'esempio precedente è stato illustrato un modo per definire una variabile. È possibile utilizzare una delle seguenti definizioni:

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
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Variabili di configurazione

È possibile utilizzare i tipi complessi di JSON per definire i valori correlati per un ambiente. 

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

Nei parametri, si crea un valore che indica i valori di configurazione da utilizzare.

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

## <a name="use-copy-element-in-variable-definition"></a>Utilizzare l'elemento copy nella definizione di variabile

È possibile usare la sintassi **copia** per creare una variabile con una matrice di più elementi. Fornire un conteggio del numero di elementi. Ogni elemento contiene le proprietà nell'oggetto **input**. È possibile usare l'istruzione copy in una variabile oppure per creare la variabile. Quando si definisce una variabile e utilizzare **copia** all'interno di tale variabile, si crea un oggetto che dispone di una proprietà di matrice. Quando si utilizza **copia** al livello superiore e definire uno o più variabili all'interno di essa, si crea uno o più gruppi. Entrambi gli approcci sono illustrati nell'esempio seguente:

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
        }
    ]
},
```

La variabile **disco matrice in oggetto** contiene il seguente oggetto con una matrice denominata **dischi**:

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

La variabile **matrice dischi-alto livello** contiene la matrice seguente:

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

È anche possibile specificare più di un oggetto quando si usa la copia per creare le variabili. Nell'esempio seguente vengono definite due matrici come variabili. Una è denominata **disks-top-level-array** e include cinque elementi. L'altra è denominata **a-different-array** e include tre elementi.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Questo approccio funziona bene quando è necessario accettare i valori di parametro e assicurarsi che siano nel formato corretto per un valore del modello. Il seguente esempio formatta i valori dei parametri per l'utilizzo nella definizione di regole di sicurezza:

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

## <a name="recommendations"></a>Raccomandazioni
Le informazioni seguenti possono essere utili quando si usano variabili:

* Usare le variabili per i valori da usare più volte in un modello. Se un valore viene usato una sola volta, un valore hardcoded facilita la lettura del modello.
* Non è possibile usare la funzione [reference](resource-group-template-functions-resource.md#reference) nella sezione **variables** del modello. La funzione **reference** deriva il proprio valore dallo stato di runtime della risorsa, ma le variabili vengono risolte durante l'analisi iniziale del modello. Costruire invece valori che richiedono la funzione **reference** direttamente nella sezione **resources** o **outputs** del modello.
* Includere le variabili per i nomi delle risorse che devono essere univoci.

## <a name="example-templates"></a>Modelli di esempio

Questi modelli di esempio vengono illustrati alcuni scenari per l'utilizzo di variabili. Distribuire in modo da verificare come le variabili vengono gestite in diversi scenari. 

|Modello  |DESCRIZIONE  |
|---------|---------|
| [definizioni di variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Illustra i diversi tipi di variabili. Il modello di non distribuire le risorse. Costruisce i valori delle variabili e restituisce questi valori. |
| [variabile di configurazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Viene illustrato l'utilizzo di una variabile che definisce i valori di configurazione. Il modello di non distribuire le risorse. Costruisce i valori delle variabili e restituisce questi valori. |
| [le regole di sicurezza di rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [file dei parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Costruisce una matrice nel formato corretto per l'assegnazione delle regole di sicurezza a un gruppo di sicurezza di rete. |


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions-resource.md#resourceid).