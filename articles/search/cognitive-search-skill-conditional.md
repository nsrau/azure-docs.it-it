---
title: Abilità cognitiva condizionale
titleSuffix: Azure Cognitive Search
description: La competenza condizionale in Ricerca cognitiva di Azure consente di filtrare, creare impostazioni predefinite e unire valori in una definizione di set di competenze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792038"
---
# <a name="conditional-cognitive-skill"></a>Abilità cognitiva condizionale

La competenza **Condizionale** consente scenari di Ricerca cognitiva di Azure che richiedono un'operazione booleana per determinare i dati da assegnare a un output. Questi scenari includono il filtro, l'assegnazione di un valore predefinito e l'unione dei dati in base a una condizione.

Lo pseudocodice seguente illustra le operazioni ottenute dalle competenze condizionali:The following pseudocode demonstrates what the conditional skill accomplishes:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Questa competenza non è associata a un'API di Servizi cognitivi di Azure e non viene addebitato alcun costo per l'uso. Tuttavia, è comunque necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione della risorsa "Gratuito" che limita l'utente a un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campi valutati

Questa abilità è speciale perché i suoi input sono campi valutati.

I seguenti elementi sono valori validi di un'espressione:

-   Percorsi di annotazione (i percorsi nelle espressioni devono essere delimitati da "" e ")
 <br/>
    Esempi:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Valori letterali (stringhe, numeri, true, false, null)Literals (strings, numbers, true, false, null) <br/>
    Esempi:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Espressioni che utilizzano gli operatori di confronto (Sezione , < <> >! <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Espressioni che utilizzano operatori booleani (&&, sz, !, ) <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Espressioni che utilizzano operatori numerici ( , -, \*, / , %) <br/>
    Esempi: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Poiché la competenza condizionale supporta la valutazione, è possibile usarla in scenari di trasformazione secondaria. Ad esempio, vedere [definizione della competenza 4](#transformation-example).

## <a name="skill-inputs"></a>Input competenze
Gli input fanno distinzione tra maiuscole e minuscole.

| Input   | Descrizione |
|-------------|-------------|
| condizione   | Questo input è un [campo valutato](#evaluated-fields) che rappresenta la condizione da valutare. Questa condizione deve restituire un valore booleano (*true* o *false*).   <br/>  Esempi: <br/> "Vero" <br/> "(/documento/lingua) <br/> "(/documenti/pagine/\*/lingua) : (/document/expectedLanguage)" <br/> |
| quandoVero    | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata su *true*. Le stringhe di costanti devono essere restituite tra virgolette singole (' e '). <br/>Valori di esempio: <br/> "Contratto""<br/>"(/documento/tipoContratto)" <br/> "(/documento/entità/\*)" <br/> |
| quandoFalse   | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata su *false*. <br/>Valori di esempio: <br/> "Contratto""<br/>"(/documento/tipoContratto)" <br/> "(/documento/entità/\*)" <br/>

## <a name="skill-outputs"></a>Output competenze
C'è un singolo output che è semplicemente chiamato "output". Restituisce il valore *whenFalse* se la condizione è false o *whenTrue* se la condizione è true.

## <a name="examples"></a>Esempi

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definizione di competenza di esempio 1: Filtrare i documenti per restituire solo i documenti in francese

L'output seguente restituisce una matrice di frasi ("/document/frenchSentences") se la lingua del documento è il francese. Se la lingua non è il francese, il valore è impostato su *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Se "/document/frenchSentences" viene utilizzato come *contesto* di un'altra competenza, tale competenza viene eseguita solo se "/document/frenchSentences" non è impostato su *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definizione di competenza di esempio 2: Impostare un valore predefinito per un valore che non esisteSample skill definition 2: Set a default value for a value that doesn't exist

L'output seguente crea un'annotazione ("/document/languageWithDefault") impostata sulla lingua del documento o su "es" se la lingua non è impostata.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definizione di competenza di esempio 3: Unire i valori di due campi in uno

In questo esempio, alcune frasi hanno una proprietà *frenchSentiment.* Ogni volta che la proprietà *frenchSentiment* è null, vogliamo usare il valore *englishSentiment.* L'output viene assegnato a un membro denominato *sentiment* ("/document/sentiment/z/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Esempio di trasformazione
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definizione di competenza di esempio 4: trasformazione dei dati in un singolo campoSample skill definition 4: Data transformation on a single field

In questo esempio viene visualizzato un *sentiment* compreso tra 0 e 1. Vogliamo trasformarlo in modo che sia compreso tra -1 e 1. Possiamo usare la competenza condizionale per eseguire questa trasformazione minore.

In questo esempio non viene usato l'aspetto condizionale della competenza perché la condizione è sempre *vera.*

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Considerazioni speciali
Alcuni parametri vengono valutati, quindi è necessario prestare particolare attenzione a seguire il modello documentato. Le espressioni devono iniziare con un segno di uguale. Un percorso deve essere delimitato da "" e ") .". Assicurarsi di inserire le stringhe tra virgolette singole. Ciò consente all'analizzatore di distinguere tra stringhe e percorsi e operatori effettivi. Inoltre, assicurarsi di inserire gli spazi vuoti intorno agli operatori (ad esempio, un "" in un percorso significa qualcosa di diverso da moltiplicare).


## <a name="next-steps"></a>Passaggi successivi

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
