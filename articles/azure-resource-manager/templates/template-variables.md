---
title: Variabili nei modelli
description: Viene descritto come definire le variabili in un modello di Azure Resource Manager.Describes how to define variables in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483817"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variabili nel modello di Azure Resource ManagerVariables in Azure Resource Manager template

Questo articolo descrive come definire e usare le variabili nel modello di Azure Resource Manager.This article describes how to define and use variables in your Azure Resource Manager template. Utilizzare le variabili per semplificare il modello. Anziché ripetere espressioni complesse in tutto il modello, si definisce una variabile che contiene l'espressione complessa. Quindi, si fa riferimento a tale variabile in base alle esigenze in tutto il modello.

Resource Manager risolve le variabili prima di avviare le operazioni di distribuzione. Ovunque la variabile venga utilizzata nel modello, Resource Manager la sostituisce con il valore risolto.

## <a name="define-variable"></a>Definisci variabile

L'esempio seguente illustra la definizione di una variabile: Crea un valore stringa per il nome di un account di archiviazione. Usa diverse funzioni di modello per ottenere un valore di parametro e lo concatena a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Non è possibile utilizzare la funzione [di riferimento](template-functions-resource.md#reference) o una delle funzioni [di elenco](template-functions-resource.md#list) nella sezione variables. Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando le variabili vengono risolte.

## <a name="use-variable"></a>Usare la variabile

Nel modello, si fa riferimento al valore per il parametro utilizzando la funzione [variables](template-functions-deployment.md#variables) . Nell'esempio seguente viene illustrato come utilizzare la variabile per una proprietà della risorsa.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Variabili di configurazione

È possibile definire variabili che contengono valori correlati per la configurazione di un ambiente. La variabile viene definita come oggetto con i valori. Nell'esempio seguente viene illustrato un oggetto che contiene valori per due ambienti: **test** e **prod**.

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

Per recuperare le impostazioni per l'ambiente specificato, utilizzare la variabile e il parametro insieme.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Modelli di esempio

Negli esempi seguenti vengono illustrati scenari per l'utilizzo delle variabili.

|Modello  |Descrizione  |
|---------|---------|
| [definizioni delle variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Illustra i diversi tipi di variabili. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [variabile di configurazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustra l'uso di una variabile che definisce i valori di configurazione. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [regole di sicurezza della rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [file dei parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crea una matrice nel formato corretto per assegnare le regole di sicurezza a un gruppo di sicurezza della rete. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le variabili, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)
* Per suggerimenti sulla creazione di variabili, vedere [Procedure consigliate - variabili](template-best-practices.md#variables).
