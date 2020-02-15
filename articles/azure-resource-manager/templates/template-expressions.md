---
title: Sintassi e espressioni del modello
description: Descrive la sintassi JSON dichiarativa per i modelli Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207401"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintassi ed espressioni nei modelli di Azure Resource Manager

La sintassi di base del modello è JSON. Tuttavia, è possibile usare le espressioni per estendere i valori JSON disponibili all'interno del modello.  Le espressioni iniziano e terminano con le parentesi quadre: `[` e `]`rispettivamente. Il valore dell'espressione viene valutato quando viene distribuito il modello. Un'espressione può restituire una stringa, un Integer, un valore booleano, una matrice o un oggetto.

Un'espressione modello non può superare i 24.576 caratteri.

## <a name="use-functions"></a>Usare le funzioni

Azure Resource Manager fornisce [funzioni](template-functions.md) che è possibile utilizzare in un modello. Nell'esempio seguente viene illustrata un'espressione che utilizza una funzione nel valore predefinito di un parametro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

All'interno dell'espressione, la sintassi `resourceGroup()` chiama una delle funzioni che Gestione risorse fornisce per l'utilizzo all'interno di un modello. In questo caso, si tratta della funzione [resourceGroup](template-functions-resource.md#resourcegroup) . Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. La sintassi `.location` recupera una proprietà dall'oggetto restituito dalla funzione.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')** . Durante la valutazione, la funzione mantiene invariato l'uso delle maiuscole/minuscole, a meno che queste non vengano modificate espressamente dalla funzione, ad esempio toUpper o toLower. Alcuni tipi di risorse possono presentare requisiti del case distinti rispetto alla valutazione delle funzioni.

Per passare un valore di stringa come parametro a una funzione, usare le virgolette singole.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

La maggior parte delle funzioni funziona allo stesso modo se distribuite in un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Le funzioni seguenti presentano restrizioni basate sull'ambito:

* [resourceGroup](template-functions-resource.md#resourcegroup) : può essere usato solo nelle distribuzioni in un gruppo di risorse.
* [resourceId](template-functions-resource.md#resourceid) : può essere usato in qualsiasi ambito, ma i parametri validi cambiano a seconda dell'ambito.
* [Subscription](template-functions-resource.md#subscription) -può essere usato solo nelle distribuzioni in un gruppo di risorse o in una sottoscrizione.

## <a name="escape-characters"></a>Caratteri di escape

Per fare in modo che una stringa letterale inizi con una parentesi quadra aperta `[` e termini con una parentesi quadra chiusa `]`, ma non venga interpretata come espressione, aggiungere una parentesi aggiuntiva per avviare la stringa con `[[`. Ad esempio, la variabile:

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

## <a name="null-values"></a>Valori Null

Per impostare una proprietà su null, è possibile usare **null** o **[JSON (' null ')]** . La [funzione JSON](template-functions-array.md#json) restituisce un oggetto vuoto quando si fornisce `null` come parametro. In entrambi i casi, Gestione risorse modelli lo considerano come se la proprietà non fosse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](template-functions.md).
* Per ulteriori informazioni sui file modello, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
