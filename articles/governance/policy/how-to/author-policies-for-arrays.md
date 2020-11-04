---
title: Creare i criteri per le proprietà della matrice nelle risorse
description: Informazioni su come usare i parametri della matrice e le espressioni del linguaggio della matrice, valutare l'alias [*] e aggiungere elementi con le regole di definizione di Criteri di Azure.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 92339a6da4fd2061d66935cc8d04428c69822862
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323235"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Creare i criteri per le proprietà della matrice nelle risorse di Azure

Le proprietà di Azure Resource Manager sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse sono invece definite come matrici. In Criteri di Azure le matrici vengono usate in modi diversi:

- Tipo di un [parametro della definizione](../concepts/definition-structure.md#parameters) per offrire più opzioni
- Parte di una [regola dei criteri](../concepts/definition-structure.md#policy-rule) che usa le condizioni **in** o **notIn**
- Parte di una regola dei criteri che valuta l'[alias\[\*\]](../concepts/definition-structure.md#understanding-the--alias) per:
  - Scenari, ad esempio di tipo **None** , **Any** o **All**
  - Scenari complessi con l'espressione **count**
- Nell'[effetto Append](../concepts/effects.md#append) per sostituire o aggiungere a una matrice esistente

Questo articolo descrive tutti gli usi di Criteri di Azure e offre diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametri

La definizione di un parametro come matrice consente l'uso di criteri flessibili quando è necessario più di un valore.
La definizione dei criteri consente di usare un'unica località qualsiasi per il parametro **allowedLocations**. Il valore predefinito è _eastus2_ :

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

Il valore di **type** è _string_ , quindi è possibile impostare un solo valore quando si assegnano i criteri. Se si assegnano questi criteri, le risorse nell'ambito sono consentite solo all'interno di un'unica area di Azure. La maggior parte delle definizioni dei criteri deve consentire un elenco di opzioni approvate, ad esempio _eastus2_ , _eastus_ e _westus2_.

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

Questa nuova definizione dei parametri accetta più di un valore durante l'assegnazione dei criteri. Dopo aver definito la proprietà della matrice **allowedValues** , i valori disponibili durante l'assegnazione sono esclusivamente quelli disponibili nell'elenco predefinito delle opzioni. L'uso di **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare i valori a una matrice di parametri durante l'assegnazione

Quando si assegnano i criteri tramite il portale di Azure, un parametro di **type** _array_ viene visualizzato come unica casella di testo, in cui viene visualizzato il suggerimento "Usare il ; per separare i valori. Ad esempio: Londra;New York". Per passare i valori delle località consentite di _eastus2_ , _eastus_ e _westus2_ al parametro, usare la stringa seguente:

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

## <a name="array-conditions"></a>Condizioni delle matrici

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

Il valore **type** previsto della condizione `equals` è _string_. Poiché **allowedLocations** è definito come **type** _array_ , il motore dei criteri valuta l'espressione del linguaggio e genera l'errore. Con le condizioni `in` e `notIn`, il motore dei criteri prevede la presenza di **type** _array_ nell'espressione del linguaggio. Per correggere questo messaggio di errore, modificare `equals` in `in` o `notIn`.

## <a name="referencing-array-resource-properties"></a>Riferimento alle proprietà delle risorse della matrice

Molti casi d'uso richiedono l'utilizzo di proprietà di matrice nella risorsa valutata. Per alcuni scenari è necessario fare riferimento a un'intera matrice, ad esempio per verificarne la lunghezza. Altri richiedono l'applicazione di una condizione a ogni singolo membro della matrice (ad esempio, assicurarsi che tutte le regole del firewall blocchino l'accesso da Internet). Informazioni sui diversi modi in cui i criteri di Azure possono fare riferimento alle proprietà delle risorse e sul comportamento di questi riferimenti quando fanno riferimento a proprietà di matrice è la chiave per la scrittura di condizioni che coprono questi scenari.

### <a name="referencing-resource-properties"></a>Riferimento alle proprietà delle risorse
I criteri di Azure possono fare riferimento alle proprietà delle risorse usando gli [alias](../concepts/definition-structure.md#aliases) . Esistono due modi per fare riferimento ai valori di una proprietà di risorsa all'interno di criteri di Azure:

- Usare la condizione di [campo](../concepts/definition-structure.md#fields) per verificare se **tutte le** proprietà della risorsa selezionata soddisfano una condizione. Esempio:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Usare `field()` la funzione per accedere al valore di una proprietà. Esempio:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

La condizione di campo ha un comportamento "All" implicito. Se l'alias rappresenta una raccolta di valori, controlla se tutti i singoli valori soddisfano la condizione. La `field()` funzione restituisce i valori rappresentati dall'alias così come sono, che possono essere modificati da altre funzioni di modello.

### <a name="referencing-array-fields"></a>Riferimenti ai campi di matrice

Le proprietà delle risorse di matrice sono in genere rappresentate da due tipi diversi di alias. Un alias "normale" e gli [alias di matrice](../concepts/definition-structure.md#understanding-the--alias) `[*]` collegati:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Riferimento alla matrice

Il primo alias rappresenta un singolo valore, ovvero il valore della `stringArray` Proprietà dal contenuto della richiesta. Poiché il valore di tale proprietà è una matrice, non è molto utile nelle condizioni dei criteri. Ad esempio:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Questa condizione Confronta l'intera `stringArray` matrice con un solo valore stringa. La maggior parte delle condizioni, tra cui `equals` , accettano solo valori stringa, quindi non c'è molto da usare per il confronto di una matrice con una stringa. Lo scenario principale in cui viene fatto riferimento alla proprietà Array è utile quando si controlla se esiste:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Con la `field()` funzione, il valore restituito è la matrice dal contenuto della richiesta, che può quindi essere usato con una qualsiasi delle [funzioni di modello supportate](../concepts/definition-structure.md#policy-functions) che accettano argomenti di matrice. Ad esempio, la condizione seguente controlla se la lunghezza di `stringArray` è maggiore di 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Riferimento alla raccolta di membri della matrice

Gli alias che usano la `[*]` sintassi rappresentano una **raccolta di valori di proprietà selezionati da una proprietà di matrice** , che è diversa dalla selezione della proprietà della matrice stessa. Nel caso di `Microsoft.Test/resourceType/stringArray[*]` , restituisce una raccolta che contiene tutti i membri di `stringArray` . Come indicato in precedenza, una `field` condizione verifica che tutte le proprietà della risorsa selezionate soddisfino la condizione, pertanto la condizione seguente è vera solo se **tutti** i membri di `stringArray` sono uguali a "" valore "".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Se la matrice contiene oggetti, `[*]` è possibile utilizzare un alias per selezionare il valore di una proprietà specifica da ogni membro della matrice. Esempio:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Questa condizione è true se i valori di tutte le `property` Proprietà in `objectArray` sono uguali a `"value"` .

Quando si usa la `field()` funzione per fare riferimento a un alias di matrice, il valore restituito è una matrice di tutti i valori selezionati. Questo comportamento indica che il caso di utilizzo comune della `field()` funzione, la possibilità di applicare funzioni modello ai valori delle proprietà delle risorse, è molto limitato. Le uniche funzioni di modello che è possibile usare in questo caso sono quelle che accettano argomenti di matrice. Ad esempio, è possibile ottenere la lunghezza della matrice con `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Tuttavia, scenari più complessi, ad esempio l'applicazione di una funzione di modello a ogni membro della matrice e il confronto con un valore desiderato, sono possibili solo quando si usa l' `count` espressione. Per altre informazioni, vedere [espressione count](#count-expressions).

Per riepilogare, vedere il contenuto della risorsa di esempio seguente e i valori selezionati restituiti da diversi alias:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Quando si usa la condizione di campo nel contenuto della risorsa di esempio, i risultati sono i seguenti:

| Alias | Valori selezionati |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Raccolta di valori vuota. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Raccolta di valori vuota. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Quando si usa la `field()` funzione nel contenuto della risorsa di esempio, i risultati sono i seguenti:

| Expression | Valore restituito |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>Espressioni di conteggio

Le espressioni [count](../concepts/definition-structure.md#count) contano il numero di membri della matrice che soddisfano una condizione e confrontano il conteggio con un valore di destinazione. `Count` è più intuitivo e versatile per la valutazione delle matrici rispetto alle `field` condizioni. La sintassi è:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Se utilizzata senza una condizione ' Where ', `count` restituisce semplicemente la lunghezza di una matrice. Con il contenuto della risorsa di esempio della sezione precedente, l' `count` espressione seguente viene valutata `true` perché `stringArray` ha tre membri:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Questo comportamento funziona anche con matrici annidate. L'espressione seguente, ad esempio, `count` viene valutata `true` perché vi sono quattro membri di matrice nelle `nestedArray` matrici:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

La potenza di `count` si trova nella `where` condizione. Quando viene specificato, criteri di Azure enumera i membri della matrice e ne valuta ognuno rispetto alla condizione, conteggiando il numero di membri della matrice valutati `true` . In particolare, in ogni iterazione della `where` valutazione della condizione, criteri di Azure seleziona un singolo membro della matrice * **i** _ e valuta il contenuto della risorsa rispetto alla `where` condizione _* come se * *_i_*_ fosse l'unico membro del array_ *. Se è disponibile un solo membro della matrice in ogni iterazione, è possibile applicare condizioni complesse a ogni singolo membro della matrice.

Esempio:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Per valutare l' `count` espressione, i criteri di Azure valutano la `where` condizione 3 volte, una volta per ogni membro di `stringArray` , conteggiando il numero di volte in cui è stato valutato `true` . Quando la `where` condizione fa riferimento ai `Microsoft.Test/resourceType/stringArray[*]` membri della matrice, anziché selezionare tutti i membri di `stringArray` , seleziona ogni volta un singolo membro della matrice:

| Iterazione | `Microsoft.Test/resourceType/stringArray[*]`Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

E pertanto `count` restituirà `1` .

Ecco un'espressione più complessa:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iterazione | Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

E quindi `count` restituisce `1` .

Il fatto che l' `where` espressione venga valutata rispetto all' **intero** contenuto della richiesta (con modifiche solo al membro della matrice attualmente in corso di enumerazione) significa che la `where` condizione può anche fare riferimento a campi all'esterno della matrice:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iterazione | Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Sono consentite anche le espressioni di conteggio nidificate:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Iterazione ciclo esterno | Valori selezionati | Iterazione del ciclo interno | Valori selezionati |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`Funzione all'interno delle `where` condizioni

Il funzionamento delle `field()` funzioni quando si trova all'interno di una `where` condizione si basa sui concetti seguenti:
1. Gli alias di matrice vengono risolti in una raccolta di valori selezionati da tutti i membri della matrice.
1. `field()` le funzioni che fanno riferimento agli alias di matrice restituiscono una matrice con i valori selezionati.
1. Se si fa riferimento all'alias di matrice conteggiato all'interno della `where` condizione, viene restituita una raccolta con un solo valore selezionato dal membro della matrice valutato nell'iterazione corrente.

Questo comportamento indica che, quando si fa riferimento al membro della matrice conteggiata con una `field()` funzione all'interno della `where` condizione, **viene restituita una matrice con un singolo membro**. Sebbene questo potrebbe non essere intuitivo, è coerente con l'idea che gli alias di matrice restituiscano sempre una raccolta di proprietà selezionate. Ad esempio:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iterazione | Valori dell'espressione | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Pertanto, quando è necessario accedere al valore dell'alias della matrice conteggiata con una `field()` funzione, il modo per eseguire questa operazione consiste nell'eseguire il wrapping di tale oggetto con una `first()` funzione di modello:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iterazione | Valori dell'espressione | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Per esempi utili, vedere [esempi di conteggio](../concepts/definition-structure.md#count-examples).

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
