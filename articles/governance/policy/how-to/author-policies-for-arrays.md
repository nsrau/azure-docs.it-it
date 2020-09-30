---
title: Creare i criteri per le proprietà della matrice nelle risorse
description: Informazioni su come usare i parametri della matrice e le espressioni del linguaggio della matrice, valutare l'alias [*] e aggiungere elementi con le regole di definizione di Criteri di Azure.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: c67982197c0161d99f29747d6fd11166cba86079
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576898"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Creare i criteri per le proprietà della matrice nelle risorse di Azure

Le proprietà di Azure Resource Manager sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse sono invece definite come matrici. In Criteri di Azure le matrici vengono usate in modi diversi:

- Tipo di un [parametro della definizione](../concepts/definition-structure.md#parameters) per offrire più opzioni
- Parte di una [regola dei criteri](../concepts/definition-structure.md#policy-rule) che usa le condizioni **in** o **notIn**
- Parte di una regola dei criteri che valuta l'[alias\[\*\]](../concepts/definition-structure.md#understanding-the--alias) per:
  - Scenari, ad esempio di tipo **None**, **Any** o **All**
  - Scenari complessi con l'espressione **count**
- Nell'[effetto Append](../concepts/effects.md#append) per sostituire o aggiungere a una matrice esistente

Questo articolo descrive tutti gli usi di Criteri di Azure e offre diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametri

La definizione di un parametro come matrice consente l'uso di criteri flessibili quando è necessario più di un valore.
La definizione dei criteri consente di usare un'unica località qualsiasi per il parametro **allowedLocations**. Il valore predefinito è _eastus2_:

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

Il valore di **type** è _string_, quindi è possibile impostare un solo valore quando si assegnano i criteri. Se si assegnano questi criteri, le risorse nell'ambito sono consentite solo all'interno di un'unica area di Azure. La maggior parte delle definizioni dei criteri deve consentire un elenco di opzioni approvate, ad esempio _eastus2_, _eastus_ e _westus2_.

Per creare la definizione dei criteri che consenta più opzioni, usare _array_ per **type**. Gli stessi criteri possono essere riscritti nel modo seguente:

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
> Dopo aver salvato una definizione dei criteri, la proprietà **type** in un parametro non può essere modificata.

Questa nuova definizione dei parametri accetta più di un valore durante l'assegnazione dei criteri. Dopo aver definito la proprietà della matrice **allowedValues**, i valori disponibili durante l'assegnazione sono esclusivamente quelli disponibili nell'elenco predefinito delle opzioni. L'uso di **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare i valori a una matrice di parametri durante l'assegnazione

Quando si assegnano i criteri tramite il portale di Azure, un parametro di **type** _array_ viene visualizzato come unica casella di testo, in cui viene visualizzato il suggerimento "Usare il ; per separare i valori. Ad esempio: Londra;New York". Per passare i valori delle località consentite di _eastus2_, _eastus_ e _westus2_ al parametro, usare la stringa seguente:

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

- Interfaccia della riga di comando di Azure: comando [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) con il parametro **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) con il parametro **PolicyParameter**
- API REST: durante l'operazione di [creazione](/rest/api/resources/policyassignments/create) di tipo _PUT_ come parte del corpo della richiesta, come valore della proprietà **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Regole dei criteri e matrici

### <a name="array-conditions"></a>Condizioni delle matrici

Le [condizioni](../concepts/definition-structure.md#conditions) della regola dei criteri per cui poter usare un parametro di _array_
**type** sono `in` e `notIn`. Usare la definizione dei criteri seguente con la condizione `equals` come esempio:

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

Il tentativo di creare questa definizione dei criteri tramite il portale di Azure genera un errore, ad esempio il messaggio di errore seguente:

- "Non è stato possibile applicare i parametri ai criteri '{GUID}' a causa di errori di convalida. Controllare che i parametri dei criteri siano definiti correttamente. Eccezione interna: "Il risultato della valutazione dell'espressione '[parameters('allowedLocations')]' del linguaggio è di tipo "Array", ma il tipo previsto è "String".

Il valore **type** previsto della condizione `equals` è _string_. Poiché **allowedLocations** è definito come **type** _array_, il motore dei criteri valuta l'espressione del linguaggio e genera l'errore. Con le condizioni `in` e `notIn`, il motore dei criteri prevede la presenza di **type** _array_ nell'espressione del linguaggio. Per correggere questo messaggio di errore, modificare `equals` in `in` o `notIn`.

### <a name="evaluating-the--alias"></a>Valutazione dell'alias [*]

Gli alias con **\[\*\]** aggiunto al nome indicano che il valore della proprietà **type** è _array_. Anziché valutare il valore dell'intera matrice, **\[\*\]** consente di valutare ogni elemento della matrice singolarmente, usando l'AND logico tra ogni elemento. Sono tre gli scenari standard in cui questa valutazione per elemento è utile: gli elementi _None_, _Any_ o _All_ corrispondono. Per scenari complessi, usare l'espressione [count](../concepts/definition-structure.md#count).

Il motore dei criteri attiva il valore **effect** nel blocco **then** solo quando la regola **if** restituisce true.
Questo aspetto è importante per comprendere in che modo **\[\*\]** valuta ogni singolo elemento della matrice.

Esempio di regola dei criteri per la tabella dello scenario seguente:

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

La matrice **ipRules** per la tabella dello scenario è la seguente:

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

Di seguito sono riportati i risultati della combinazione della condizione, la regola dei criteri di esempio e la matrice di valori esistenti precedenti:

|Condizione |Risultato | Scenario |Spiegazione |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nessuno |Nessuna corrispondenza |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effetto dei criteri |Nessuna corrispondenza |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_ e l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effetto dei criteri |Una o più corrispondenze |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nessuno |Una o più corrispondenze |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_. L'operatore logico restituisce false (**non** _true_), quindi l'effetto non viene attivato. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effetto dei criteri |Corrispondenza non totale |Un elemento della matrice restituisce true (127.0.0.1 == 127.0.0.1) e uno false (127.0.0.1 == 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effetto dei criteri |Corrispondenza non totale |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 == 127.0.0.1 and 10.0.4.1 == 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`{<field>,"Equals":"127.0.0.1"}` |Nessuno |Corrispondenza totale |Un elemento della matrice restituisce true (127.0.0.1 == 127.0.0.1) e uno false (127.0.0.1 == 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"Equals":"10.0.4.1"}` |Nessuno |Corrispondenza totale |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |

## <a name="modifying-arrays"></a>Modifica di matrici

[Accodare](../concepts/effects.md#append) e [modificare](../concepts/effects.md#modify) le proprietà ALTER su una risorsa durante la creazione o l'aggiornamento. Quando si utilizzano le proprietà della matrice, il comportamento di questi effetti dipende dal fatto che l'operazione stia tentando di modificare  **\[\*\]** o meno l'alias:

> [!NOTE]
> L'uso dell' `modify` effetto con gli alias è attualmente in **Anteprima**.

|Alias |Effetto | Risultato |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Criteri di Azure accoda l'intera matrice specificata nei dettagli dell'effetto, se mancante. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``add`operazione with | Criteri di Azure accoda l'intera matrice specificata nei dettagli dell'effetto, se mancante. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``addOrReplace`operazione with | Criteri di Azure accoda l'intera matrice specificata nei dettagli effetti se mancante o sostituisce la matrice esistente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Criteri di Azure Accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``add`operazione with | Criteri di Azure Accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``addOrReplace`operazione with | Criteri di Azure rimuove tutti i membri della matrice esistenti e accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Criteri di Azure aggiunge un valore alla `action` proprietà di ogni membro della matrice. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``add`operazione with | Criteri di Azure aggiunge un valore alla `action` proprietà di ogni membro della matrice. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``addOrReplace`operazione with | Criteri di Azure aggiunge o sostituisce la `action` proprietà esistente di ogni membro della matrice. |

Per altre informazioni, vedere gli [esempi di Append](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
