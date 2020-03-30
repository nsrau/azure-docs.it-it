---
title: Sintassi ed espressioni del modello
description: Descrive la sintassi JSON dichiarativa per i modelli di Azure Resource Manager.Describes the declarative JSON syntax for Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460110"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintassi ed espressioni nei modelli di Azure Resource Manager

La sintassi di base del modello è JSON. Tuttavia, è possibile usare le espressioni per estendere i valori JSON disponibili all'interno del modello.  Le espressioni iniziano e terminano con parentesi quadre: `[` e `]` rispettivamente. Il valore dell'espressione viene valutato quando viene distribuito il modello. Un'espressione può restituire una stringa, un intero, un valore booleano, una matrice o un oggetto.

Un'espressione modello non può superare i 24.576 caratteri.

## <a name="use-functions"></a>Usare le funzioni

Azure Resource Manager offre [funzioni](template-functions.md) che è possibile usare in un modello. Nell'esempio seguente viene illustrata un'espressione che usa una funzione nel valore predefinito di un parametro:The following example shows an expression that uses a function in the default value of a parameter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

All'interno dell'espressione, la sintassi `resourceGroup()` chiama una delle funzioni fornite da Resource Manager per l'utilizzo all'interno di un modello. In questo caso, è la funzione [resourceGroup.In](template-functions-resource.md#resourcegroup) this case, it's the resourceGroup function. Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. La `.location` sintassi recupera una proprietà dall'oggetto restituito da tale funzione.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')**. Durante la valutazione, la funzione mantiene invariato l'uso delle maiuscole/minuscole, a meno che queste non vengano modificate espressamente dalla funzione, ad esempio toUpper o toLower. Alcuni tipi di risorse possono avere requisiti di distinzione tra maiuscole e minuscole separati dal modo in cui vengono valutate le funzioni.

Per passare un valore stringa come parametro a una funzione, usare virgolette singole.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

La maggior parte delle funzioni funziona allo stesso modo indipendentemente dal fatto che sia distribuita a un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Le seguenti funzioni hanno restrizioni basate sull'ambito:

* [resourceGroup](template-functions-resource.md#resourcegroup) - può essere usato solo nelle distribuzioni in un gruppo di risorse.resourceGroup - can only be used in deployments to a resource group.
* [resourceId](template-functions-resource.md#resourceid) - può essere utilizzato in qualsiasi ambito, ma i parametri validi cambiano a seconda dell'ambito.
* [sottoscrizione](template-functions-resource.md#subscription) - può essere usato solo nelle distribuzioni in un gruppo di risorse o in una sottoscrizione.subscription - can only be used in deployments to a resource group or subscription.

## <a name="escape-characters"></a>Caratteri di escape

Per fare in modo che `[` una stringa letterale `]`inizi con una parentesi quadra aperta e termini con `[[`una parentesi quadra chiusa, ma non che venga interpretata come un'espressione, aggiungere una parentesi quadra aggiuntiva per iniziare la stringa con . Ad esempio, la variabile:

```json
"demoVar1": "[[test value]"
```

Risolve in `[test value]`.

Tuttavia, se la stringa letterale non termina con una parentesi quadra, non eseguire l'escape della prima parentesi quadra. Ad esempio, la variabile:

```json
"demoVar2": "[test] value"
```

Risolve in `[test] value`.

Per eseguire l'escape delle virgolette doppie in un'espressione, ad esempio l'aggiunta di un oggetto JSON nel modello, usare la barra rovesciata.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Quando si passano i valori dei parametri, l'utilizzo dei caratteri di escape dipende da dove viene specificato il valore del parametro. Se si imposta un valore predefinito nel modello, è necessaria la parentesi quadra aperta aggiuntiva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Se si utilizza il valore `[test value]`predefinito, il modello restituisce .

Tuttavia, se si passa un valore di parametro tramite la riga di comando, i caratteri vengono interpretati letteralmente. Distribuzione del modello precedente con:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Restituisce `[[test value]`. Utilizzare invece:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

La stessa formattazione si applica quando si passano valori da un file di parametri. I caratteri vengono interpretati letteralmente. Se utilizzato con il modello precedente, `[test value]`il seguente file di parametri restituisce:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Valori Null

Per impostare una proprietà su null, è possibile utilizzare **null** o **[json('null')]**. La [funzione json](template-functions-array.md#json) restituisce un `null` oggetto vuoto quando si fornisce come parametro. In entrambi i casi, i modelli di Resource Manager lo considerano come se la proprietà non fosse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](template-functions.md).
* Per altre informazioni sui file di modello, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)
