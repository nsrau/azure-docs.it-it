---
title: Criteri dell'autore per le proprietà della matrice nelle risorse
description: Informazioni su come usare i parametri di matrice e le espressioni del linguaggio di matrice, valutare l'alias [-] e aggiungere elementi con le regole di definizione dei criteri di Azure.Learn to work with array parameters and array language expressions, evaluate the [-] alias, and to append elements with Azure Policy definition rules.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280664"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Criteri dell'autore per le proprietà della matrice nelle risorse di AzureAuthor policies for array properties on Azure resources

Le proprietà di Azure Resource Manager sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse vengono invece definite come matrici. In Criteri di Azure gli array vengono usati in diversi modi:In Azure Policy, arrays are used in several different ways:

- Il tipo di un parametro di [definizione](../concepts/definition-structure.md#parameters), per fornire più opzioni
- Parte di una [regola dei criteri](../concepts/definition-structure.md#policy-rule) che utilizza le condizioni **in** o **notIn**
- Parte di una regola dei criteri che valuta [ \[ \* \] l'alias](../concepts/definition-structure.md#understanding-the--alias) da valutare:
  - Scenari come **None**, **Any**o **All**
  - Scenari complessi con **count**
- [Nell'effetto di aggiunta](../concepts/effects.md#append) per sostituire o aggiungere a una matrice esistente

Questo articolo illustra ogni utilizzo dei criteri di Azure e fornisce diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametriDefine a parameter array

La definizione di un parametro come matrice consente la flessibilità dei criteri quando è necessario più di un valore.
Questa definizione dei criteri consente qualsiasi singola posizione per il parametro **allowedLocations** e il valore predefinito è _eastus2_:

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

Poiché **type** era _string_, è possibile impostare un solo valore durante l'assegnazione del criterio. Se questo criterio viene assegnato, le risorse nell'ambito sono consentite solo all'interno di una singola area di Azure.If this policy is assigned, resources in scope are only allowed within a single Azure region. La maggior parte delle definizioni dei criteri deve consentire un elenco di opzioni approvate, ad esempio _eastus2_, _eastus_e _westus2_.

Per creare la definizione dei criteri per consentire più opzioni, utilizzare il **tipo di** _matrice_ . Lo stesso criterio può essere riscritto come segue:

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
> Una volta salvata una definizione di criterio, la proprietà del **tipo** in un parametro non può essere modificata.

Questa nuova definizione di parametro accetta più di un valore durante l'assegnazione dei criteri. Una volta definita la proprietà della matrice **allowedValues,** i valori disponibili durante l'assegnazione sono ulteriormente limitati all'elenco predefinito di scelte. L'utilizzo di **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare valori a una matrice di parametri durante l'assegnazionePass values to a parameter array during assignment

Quando si assegnano i criteri tramite il portale di Azure, un parametro di **tipo** _matrice_ viene visualizzato come una singola casella di testo. Il suggerimento dice "Usa ; per separare i valori. (ad es. Londra; New York)". Per passare i valori di posizione consentiti di _eastus2_, _eastus_e _westus2_ al parametro, utilizzare la stringa seguente:

`eastus2;eastus;westus2`

Il formato per il valore del parametro è diverso quando si usa l'interfaccia della riga di comando di Azure, Azure PowerShell o l'API REST. I valori vengono passati tramite una stringa JSON che include anche il nome del parametro.

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

Per utilizzare questa stringa con ogni SDK, utilizzare i comandi seguenti:

- Interfaccia di comando di Azure: Comando [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) con parameter **params**
- Azure PowerShell: cmdlet New-AzPolicyAssignment con il parametro **PolicyParameterAzure** PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) with parameter PolicyParameter
- API REST: nell'operazione _di_ [creazione](/rest/api/resources/policyassignments/create) PUT come parte del corpo della richiesta come valore della proprietà **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Regole e matrici di criteri

### <a name="array-conditions"></a>Condizioni di array

Le [condizioni](../concepts/definition-structure.md#conditions) della regola dei criteri con cui `in` è `notIn`possibile utilizzare un**tipo** di parametro di _matrice_
sono limitate a e . Prendiamo come esempio la `equals` seguente definizione di criteri con condition:

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

Il tentativo di creare questa definizione di criteri tramite il portale di Azure comporta un errore, ad esempio questo messaggio di errore:Tenting to create this policy definition through the Azure portal leads to an error such as this error message:

- "Impossibile parametrizzare i criteri ''GUID'' a causa di errori di convalida. Verificare se i parametri dei criteri sono definiti correttamente. L'eccezione interna 'Risultato della valutazione dell'espressione del linguaggio '[parameters('allowedLocations')]' è di tipo 'Array', il tipo previsto è 'String'.'."

Il **tipo** di `equals` condizione previsto è _string_. Poiché **allowedLocations** è definito come _matrice_di **tipo** , il motore dei criteri valuta l'espressione del linguaggio e genera l'errore. Con `in` la `notIn` condizione e , il motore dei criteri prevede la _matrice_ di **tipi** nell'espressione del linguaggio. Per risolvere questo messaggio `equals` di `in` `notIn`errore, passare a o .

### <a name="evaluating-the--alias"></a>Valutazione dell'alias [-]

Gli alias ** \[ \* ** associati al relativo nome indicano che il **tipo** è _una matrice_. Anziché valutare il valore dell'intera matrice, ** \[ \* ** consente di valutare ogni elemento della matrice singolarmente, con AND logico tra di essi. Esistono tre scenari standard, questa valutazione per elemento è utile in: _None_, _Any_o _All_ elements match. Per scenari complessi, utilizzare [count](../concepts/definition-structure.md#count).

Il motore dei criteri attiva **l'effetto** in **then** solo quando la regola **if** restituisce come true.
Questo fatto è importante comprendere nel ** \[ \* ** contesto del modo in cui valuta ogni singolo elemento della matrice.

La regola dei criteri di esempio per la tabella degli scenari seguente:The example policy rule for the scenario table below:

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

La matrice **ipRules** è la seguente per la tabella degli scenari seguente:

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

Per ogni esempio di `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`condizione riportato di seguito, sostituire con .

I seguenti risultati sono il risultato della combinazione della condizione e della regola dei criteri di esempio e della matrice di valori esistenti sopra:

|Condizione |Risultato | Scenario |Spiegazione |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Nessuna corrispondenza |Un elemento della matrice restituisce come false (127.0.0.1 ! **notEquals** _false_ |
|`{<field>,"notEquals":"10.0.4.1"}` |Effetto politico |Nessuna corrispondenza |Entrambi gli elementi della matrice restituiscono valori versi (10.0.4.1 ! **notEquals** _true_ |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effetto politico |Una o più corrispondenze |Un elemento della matrice restituisce come false (127.0.0.1 ! **notEquals** _false_ L'operatore logico restituisce true (**non** _false_), pertanto viene attivato l'effetto. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Una o più corrispondenze |Entrambi gli elementi della matrice restituiscono valori vertici (10.0.4.1 ! **notEquals** _true_ L'operatore logico restituisce false (**non** _true_), pertanto l'effetto non viene attivato. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effetto politico |Non tutti corrispondono |Un elemento della matrice restituisce true (127.0.0.1 : 127.0.0.1) e uno come false (127.0.0.1 , 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), pertanto viene attivato l'effetto. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effetto politico |Non tutti corrispondono |Entrambi gli elementi della matrice restituiscono come false (10.0.4.1 - 127.0.0.1 e 10.0.4.1 - 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), pertanto viene attivato l'effetto. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Tutti corrispondono |Un elemento della matrice restituisce true (127.0.0.1 , 127.0.0.1) e uno come false (127.0.0.1 , 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Tutti corrispondono |Entrambi gli elementi della matrice restituiscono il valore false (10.0.4.1 - 127.0.0.1 e 10.0.4.1 - 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |

## <a name="the-append-effect-and-arrays"></a>L'effetto di aggiunta e le matrici

[L'effetto di aggiunta](../concepts/effects.md#append) si comporta in modo diverso ** \[ \* ** a seconda che il **campo details.field** sia o meno un alias.

- Quando non ** \[ \* ** è un alias, append sostituisce l'intera matrice con la proprietà **value**
- Quando ** \[ \* ** un alias, append aggiunge la proprietà **value** alla matrice esistente o crea la nuova matrice

Per ulteriori informazioni, vedere gli esempi di [aggiunta](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Rivedere [Informazioni sugli effetti dei criteri](../concepts/effects.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Informazioni su come [correggere le risorse non conformi.](remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
