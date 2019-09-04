---
title: Sintassi ed espressioni del modello di Azure Resource Manager
description: Descrive la sintassi JSON dichiarativa per i modelli Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259483"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintassi ed espressioni nei modelli di Azure Resource Manager

La sintassi di base del modello è JSON. Tuttavia, è possibile usare le espressioni per estendere i valori JSON disponibili all'interno del modello.  Le espressioni iniziano e terminano con le `[` parentesi `]`quadre: rispettivamente e. Il valore dell'espressione viene valutato quando viene distribuito il modello. Un'espressione può restituire una stringa, un Integer, un valore booleano, una matrice o un oggetto.

Un'espressione modello non può superare i 24.576 caratteri.

## <a name="use-functions"></a>Usare le funzioni

Nell'esempio seguente viene illustrata un'espressione nel valore predefinito di un parametro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

All'interno dell'espressione, la `resourceGroup()` sintassi chiama una delle funzioni che Gestione risorse fornisce per l'utilizzo all'interno di un modello. In questo caso, si tratta della funzione [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) . Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. La sintassi `.location` recupera una proprietà dall'oggetto restituito dalla funzione.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')** . Durante la valutazione, la funzione mantiene invariato l'uso delle maiuscole/minuscole, a meno che queste non vengano modificate espressamente dalla funzione, ad esempio toUpper o toLower. Alcuni tipi di risorse possono presentare requisiti del case distinti rispetto alla valutazione delle funzioni.

Per passare un valore di stringa come parametro a una funzione, usare le virgolette singole.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Caratteri di escape

Per avere una stringa letterale che inizia con una `[` parentesi quadra aperta e termina con `]`una parentesi quadra chiusa, ma non deve essere interpretata come espressione, aggiungere una parentesi aggiuntiva `[[`per avviare la stringa con. Ad esempio, la variabile:

```json
"demoVar1": "[[test value]"
```

Viene risolto in `[test value]`.

Tuttavia, se la stringa letterale non termina con una parentesi, non usare caratteri di escape per la prima parentesi. Ad esempio, la variabile:

```json
"demoVar2": "[test] value"
```

Viene risolto in `[test] value`.

Per eseguire l'escape delle virgolette doppie in un'espressione, ad esempio l'aggiunta di un oggetto JSON nel modello, usare la barra rovesciata.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Passaggi successivi

* Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md).
* Per ulteriori informazioni sui file modello, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
