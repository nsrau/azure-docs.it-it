---
title: Criteri autore per le proprietà delle matrici sulle risorse
description: Informazioni su come usare i parametri di matrice e le espressioni del linguaggio di matrici, valutare l'alias [*] e aggiungere elementi con le regole di definizione dei criteri di Azure.
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: 035f300d01efe80cc44687d3779d7a5fb6be2fc3
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555157"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Modificare i criteri per le proprietà delle matrici nelle risorse di Azure

Azure Resource Manager proprietà sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse sono invece definite come matrici. In criteri di Azure, le matrici vengono usate in diversi modi:

- Tipo di un [parametro di definizione](../concepts/definition-structure.md#parameters)per fornire più opzioni
- Parte di una [regola di criteri](../concepts/definition-structure.md#policy-rule) che usa le condizioni **in** o **notIn**
- Parte di una regola di criteri che valuta il [\[\*alias \]](../concepts/definition-structure.md#understanding-the--alias) da valutare:
  - Scenari come **None**, **any**o **All**
  - Scenari complessi con **conteggio**
- Nell' [effetto di Accodamento](../concepts/effects.md#append) da sostituire o aggiungere a una matrice esistente

Questo articolo descrive ogni uso di criteri di Azure e offre diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametri

La definizione di un parametro come matrice consente la flessibilità dei criteri quando è necessario più di un valore.
Questa definizione di criteri consente qualsiasi percorso singolo per il parametro **allowedLocations** e il valore predefinito è _eastus2_:

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

Poiché il **tipo** è _stringa_, è possibile impostare un solo valore quando si assegnano i criteri. Se questo criterio viene assegnato, le risorse nell'ambito sono consentite solo all'interno di una singola area di Azure. La maggior parte delle definizioni di criteri deve consentire un elenco di opzioni approvate, ad esempio consentire _eastus2_, _eastus_e _westus2_.

Per creare la definizione dei criteri per consentire più opzioni, usare il **tipo**di _matrice_ . Lo stesso criterio può essere riscritto nel modo seguente:

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
> Una volta salvata una definizione dei criteri, la proprietà **Type** di un parametro non può essere modificata.

Questa nuova definizione di parametro richiede più di un valore durante l'assegnazione dei criteri. Con la proprietà array **allowedValues** definita, i valori disponibili durante l'assegnazione sono ulteriormente limitati all'elenco predefinito di opzioni. L'utilizzo di **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare i valori a una matrice di parametri durante l'assegnazione

Quando si assegnano i criteri tramite la portale di Azure, un parametro di **tipo** _Array_ viene visualizzato come una sola casella di testo. L'hint dice "use; per separare i valori. (ad esempio, Londra; New York) ". Per passare i valori di posizione consentiti di _eastus2_, _eastus_e _westus2_ al parametro, usare la stringa seguente:

`eastus2;eastus;westus2`

Il formato del valore del parametro è diverso quando si usa l'interfaccia della riga di comando di Azure, Azure PowerShell o l'API REST. I valori vengono passati tramite una stringa JSON che include anche il nome del parametro.

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

- INTERFACCIA della riga di comando di Azure: comando [AZ Policy Assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) with Parameter **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) con il parametro **PolicyParameter**
- API REST: nell'operazione _put_ [create](/rest/api/resources/policyassignments/create) come parte del corpo della richiesta come valore della proprietà **Properties. Parameters**

## <a name="policy-rules-and-arrays"></a>Regole e matrici di criteri

### <a name="array-conditions"></a>Condizioni di matrice

Le [condizioni](../concepts/definition-structure.md#conditions) della regola dei criteri per le quali è possibile utilizzare una _matrice_
**tipo** di parametro sono limitate a `in` e `notIn`. Usare la definizione di criteri seguente con la condizione `equals` come esempio:

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

Il tentativo di creare la definizione di criteri tramite il portale di Azure genera un errore, ad esempio il messaggio di errore seguente:

- "Impossibile parametrizzare il criterio ' {GUID}' a causa di errori di convalida. Verificare che i parametri dei criteri siano definiti correttamente. L'eccezione interna ' risultato della valutazione dell'espressione del linguaggio ' [Parameters (' allowedLocations ')]' è di tipo ' array '. il tipo previsto è' String ' .'. "

Il **tipo** di condizione previsto `equals` è _String_. Poiché **allowedLocations** è definito come _matrice_di tipi, il motore dei criteri valuta l'espressione del linguaggio e genera l'errore. Con il `in` e la condizione `notIn`, il motore dei criteri prevede la _matrice_ di **tipi** nell'espressione del linguaggio. Per correggere questo messaggio di errore, modificare `equals` in `in` o `notIn`.

### <a name="evaluating-the--alias"></a>Valutazione dell'alias [*]

Gli alias con **\[\*\]** allegati al nome indicano che il **tipo** è una _matrice_. Anziché valutare il valore dell'intera matrice, **\[\*\]** rende possibile valutare ogni elemento della matrice. Esistono tre scenari standard in cui questa valutazione per elemento è utile in: nessuno, nessuno e tutti. Per gli scenari complessi, usare [count](../concepts/definition-structure.md#count).

Il motore dei criteri attiva l' **effetto** in **quindi** solo quando la regola **if** restituisce true.
Questo aspetto è importante per comprendere nel contesto del modo in cui **\[\*\]** valuta ogni singolo elemento della matrice.

La regola dei criteri di esempio per la tabella dello scenario seguente:

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

La matrice **ipRules** è la seguente per la tabella dello scenario riportata di seguito:

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

Per ogni condizione di esempio riportata di seguito, sostituire `<field>` con `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

I risultati seguenti sono il risultato della combinazione della condizione e della regola dei criteri di esempio e della matrice dei valori esistenti precedente:

|Condizione |Risultato |Spiegazione |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno come true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effetto criteri |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_ e l'effetto viene attivato. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effetto criteri |Un elemento della matrice restituisce true (127.0.0.1 = = 127.0.0.1) e uno come false (127.0.0.1 = = 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effetto criteri |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effetto criteri |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno come true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_. L'operatore logico restituisce false (**non** _true_), quindi l'effetto non viene attivato. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Un elemento della matrice restituisce true (127.0.0.1 = = 127.0.0.1) e uno come false (127.0.0.1 = = 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |

## <a name="the-append-effect-and-arrays"></a>L'effetto di Accodamento e le matrici

L' [effetto di Accodamento](../concepts/effects.md#append) si comporta in modo diverso a seconda che il **campo details. field** sia un **\[\*\]** alias.

- Quando non è un **\[\*\]** alias, Append sostituisce l'intera matrice con la proprietà **value**
- Quando un **\[\*\]** alias, Aggiungi aggiunge la proprietà **value** alla matrice esistente o crea la nuova matrice

Per ulteriori informazioni, vedere gli [esempi di Accodamento](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).