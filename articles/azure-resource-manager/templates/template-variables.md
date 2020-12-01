---
title: Variabili nei modelli
description: Viene descritto come definire le variabili in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 5d9b58d63e96656c45d3494d24099bbeadc46b11
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353460"
---
# <a name="variables-in-arm-template"></a>Variabili nel modello ARM

Questo articolo descrive come definire e usare le variabili nel modello di Azure Resource Manager (modello ARM). Per semplificare il modello, è possibile usare le variabili. Anziché ripetere le espressioni complesse nell'intero modello, si definisce una variabile che contiene l'espressione complessa. Quindi, si fa riferimento a tale variabile in base alle esigenze nell'intero modello.

Gestione risorse risolve le variabili prima di avviare le operazioni di distribuzione. Indipendentemente dalla posizione in cui viene usata la variabile nel modello, Resource Manager la sostituisce con il valore risolto.

Il formato di ogni variabile deve corrispondere a uno dei [tipi di dati](template-syntax.md#data-types).

## <a name="define-variable"></a>Definisci variabile

L'esempio seguente illustra la definizione di una variabile: Crea un valore stringa per il nome di un account di archiviazione. USA diverse funzioni di modello per ottenere un valore di parametro e lo concatena a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Non è possibile usare la funzione [Reference](template-functions-resource.md#reference) o una delle funzioni [elenco](template-functions-resource.md#list) nella sezione Variables. Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando le variabili vengono risolte.

## <a name="use-variable"></a>Usare la variabile

Nel modello si fa riferimento al valore per il parametro usando la funzione [variables](template-functions-deployment.md#variables) . Nell'esempio seguente viene illustrato come utilizzare la variabile per una proprietà della risorsa.

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

È possibile definire variabili che contengono valori correlati per la configurazione di un ambiente. La variabile viene definita come oggetto con i valori. Nell'esempio seguente viene illustrato un oggetto che contiene i valori per due ambienti, **test** e **Prod**.

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

Per recuperare le impostazioni per l'ambiente specificato, usare insieme la variabile e il parametro.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Modelli di esempio

Negli esempi seguenti vengono illustrati gli scenari per l'utilizzo di variabili.

|Modello  |Descrizione  |
|---------|---------|
| [definizioni delle variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Illustra i diversi tipi di variabili. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [variabile di configurazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustra l'uso di una variabile che definisce i valori di configurazione. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [regole di sicurezza della rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [file dei parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crea una matrice nel formato corretto per assegnare le regole di sicurezza a un gruppo di sicurezza della rete. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le variabili, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per consigli sulla creazione di variabili, vedere procedure consigliate [-variabili](template-best-practices.md#variables).
