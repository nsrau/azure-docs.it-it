---
title: Criteri di autore per le proprietà di matrice sulle risorse di Azure
description: Si apprenderà a creare i parametri di matrice, creare le espressioni del linguaggio delle regole per l'array, valutare l'alias [*] e per l'aggiunta di elementi in una matrice esistente con regole di definizione dei criteri di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 479f77791a0b035f2d1de6085dfb12f5196288ee
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979334"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Criteri di autore per le proprietà di matrice sulle risorse di Azure

Proprietà di Resource Manager di Azure sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse vengono invece definite come matrici. In Criteri di Azure, le matrici vengono usate in diversi modi:

- Il tipo di un [parametro definition](../concepts/definition-structure.md#parameters), per offrire più opzioni
- Parte di un [regola dei criteri](../concepts/definition-structure.md#policy-rule) usando le condizioni **nelle** o **notIn**
- Parte di una regola di criteri che restituisce il [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) valutare scenari specifici, ad esempio **None**, **qualsiasi**, o  **Tutti i**
- Nel [append effetto](../concepts/effects.md#append) per sostituire o aggiungere a una matrice esistente

Questo articolo illustra ogni uso di criteri di Azure e fornisce diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametri

Definizione di un parametro sotto forma di matrice consente la flessibilità di criteri quando è necessari più di un valore.
Questa definizione di criteri consente a qualsiasi percorso singolo per il parametro **allowedLocations** e l'impostazione predefinita _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Come **tipo** era _stringa_, solo un valore può essere impostato quando assegnano i criteri. Se viene assegnato questo criterio, le risorse nell'ambito sono consentite solo all'interno di una singola area di Azure. La maggior parte delle definizioni di criteri necessari consentire un elenco delle opzioni approvate, ad esempio per consentire _eastus2_, _eastus_, e _westus2_.

Per creare la definizione dei criteri per consentire più opzioni, usare il _matrice_ **tipo**. Lo stesso criterio può essere riscritto come segue:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Dopo avere salvata una definizione di criteri, il **tipo** proprietà in un parametro non può essere modificato.

La nuova definizione di parametro accetta più di un valore durante l'assegnazione dei criteri. Con la proprietà della matrice **allowedValues** definito, i valori disponibili durante l'assegnazione risulteranno ulteriormente limitate per l'elenco predefinito di opzioni. Sfrutta **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare valori a una matrice di parametri durante l'assegnazione

Quando si assegnano i criteri tramite il portale di Azure, un parametro di **tipo** _matrice_ viene visualizzato come una singola casella di testo. L'hint per la dicitura "usare; per separare i valori. (ad esempio, Londra; New York) ". Per passare i valori di percorso consentiti dei _eastus2_, _eastus_, e _westus2_ al parametro, usare la stringa seguente:

`eastus2;eastus;westus2`

Il formato per il valore del parametro è diverso quando si usa l'API REST, Azure PowerShell o CLI di Azure. I valori vengono passati tramite una stringa JSON che include anche il nome del parametro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Per usare questa stringa con ogni SDK, usare i comandi seguenti:

- Interfaccia della riga di comando di Azure: Comando [creare l'assegnazione dei criteri az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) con il parametro **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) con il parametro **PolicyParameter**
- API REST: Nel _inserito_ [creare](/rest/api/resources/policyassignments/create) operazione come parte del corpo della richiesta come valore del **properties.parameters** proprietà

## <a name="policy-rules-and-arrays"></a>Matrici e le regole di criteri

### <a name="array-conditions"></a>Condizioni di matrice

La regola dei criteri [condizioni](../concepts/definition-structure.md#conditions) che un' _matrice_
**tipo** del parametro può essere usato con è limitato a `in` e `notIn`. Eseguire la seguente definizione di criteri con condizione `equals` ad esempio:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

È stato effettuato un tentativo di creare questa definizione di criteri tramite i lead del portale di Azure a un errore, ad esempio questo messaggio di errore:

- "Il criterio '{GUID}' potrebbe non essere con parametri a causa di errori di convalida. Verificare se i parametri dei criteri sono definiti correttamente. L'eccezione interna 'valutazione risultato dell'espressione del linguaggio '[parameters('allowedLocations')]' è di tipo 'Array', tipo previsto: 'String' '."

Previsto **tipo** della condizione `equals` viene _stringa_. Poiché **allowedLocations** viene definito come **tipo** _matrice_, il motore dei criteri viene valutata l'espressione del linguaggio e genera l'errore. Con il `in` e `notIn` condizione, il motore dei criteri prevede il **tipo** _matrice_ nell'espressione del linguaggio. Per risolvere questo messaggio di errore, modificare `equals` a uno `in` o `notIn`.

### <a name="evaluating-the--alias"></a>La valutazione dell'alias [*]

Gli alias contenenti **[\*]** collegato in base al nome indicano la **tipo** è un _matrice_. Invece di valutare il valore della matrice intera **[\*]** consente di valutare ogni elemento della matrice. Esistono tre scenari di in che questo per ogni valutazione dell'elemento è utile: Nessuno, uno e così via

I trigger di motore di criteri il **effetto** nelle **quindi** solo quando il **se** regola restituisce true.
Questo aspetto è importante comprendere nel contesto del modo in cui **[\*]** valuta ogni singolo elemento della matrice.

La regola di criterio di esempio per la tabella di scenario seguente:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Il **ipRules** array si trovi come indicato di seguito per la tabella di scenario seguente:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Per ogni esempio di condizione seguente, sostituire `<field>` con `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

I seguenti risultati sono il risultato della combinazione di condizione regola dei criteri di esempio e matrice di valori esistenti precedenti:

|Condizione |Risultato |Spiegazione |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Un elemento della matrice viene valutata come falsa (127.0.0.1! = 127.0.0.1) e uno come true (127.0.0.1! = 192.168.1.1), in modo che il **notEquals** condizione è _false_ e non viene attivato l'effetto. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effetto dei criteri |Entrambi gli elementi di matrice vengono valutate come true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), in modo che il **notEquals** condizione è _true_ e viene attivato l'effetto. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effetto dei criteri |Un elemento della matrice viene valutata come true (127.0.0.1 = = 127.0.0.1) e uno come false (127.0.0.1 = = 192.168.1.1), in modo che il **è uguale a** condizione è _false_. L'operatore logico viene valutata come true (**non** _false_), quindi viene attivato l'effetto. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effetto dei criteri |Entrambi gli elementi di matrice restituiscono false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), in modo che il **è uguale a** condizione è _false_. L'operatore logico viene valutata come true (**non** _false_), quindi viene attivato l'effetto. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effetto dei criteri |Un elemento della matrice viene valutata come falsa (127.0.0.1! = 127.0.0.1) e uno come true (127.0.0.1! = 192.168.1.1), in modo che il **notEquals** condizione è _false_. L'operatore logico viene valutata come true (**non** _false_), quindi viene attivato l'effetto. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Entrambi gli elementi di matrice vengono valutate come true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), in modo che il **notEquals** condizione è _true_. L'operatore logico viene valutata come falsa (**non** _true_), in modo che non viene attivato l'effetto. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Un elemento della matrice viene valutata come true (127.0.0.1 = = 127.0.0.1) e uno come false (127.0.0.1 = = 192.168.1.1), in modo che il **è uguale a** condizione è _false_ e non viene attivato l'effetto. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Entrambi gli elementi di matrice restituiscono false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), in modo che il **è uguale a** condizione è _false_ e non viene attivato l'effetto. |

## <a name="the-append-effect-and-arrays"></a>L'effetto di accodamento e matrici

Il [append effetto](../concepts/effects.md#append) si comporta in modo diverso a seconda se il **details.field** è un **[\*]** alias o non.

- Se non una **[\*]** alias, aggiungere sostituisce l'intera matrice con il **valore** proprietà
- Quando un **[\*]** aggiungere alias, aggiunge il **valore** matrici di proprietà esistente o crea la nuova matrice

Per altre informazioni, vedere la [append esempi](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi nella [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Comprendere come [a livello di codice, creare criteri](programmatically-create.md).
- Informazioni su come [monitora e aggiorna le risorse non conformi](remediate-resources.md).
- Esaminare un gruppo di gestione riguarda [organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).