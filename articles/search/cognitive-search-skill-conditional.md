---
title: Abilità di ricerca cognitiva condizionale (ricerca di Azure) | Microsoft Docs
description: L'abilità condizionale consente di filtrare, creare valori predefiniti e unire i valori.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 21a36988b31571f2110fe4fd2802aa5d84ee0216
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635918"
---
#   <a name="conditional-skill"></a>Competenza condizionale

La *competenza condizionale* consente agli scenari di ricerca di Azure che richiedono un'operazione booleana di determinare i dati da assegnare a un output. Questi scenari includono l'applicazione di filtri, l'assegnazione di un valore predefinito e l'Unione di dati in base a una condizione.

Lo pseudocodice seguente illustra il risultato della competenza condizionale:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Questa competenza non è associata a un'API servizi cognitivi di Azure e non viene addebitata l'utilizzo. Tuttavia, è comunque necessario [alleghi una risorsa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) per sostituire l'opzione della risorsa "gratuita" che limita l'utente a un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campi valutati

Questa competenza è speciale perché i relativi input sono campi valutati.

Gli elementi seguenti sono valori validi di un'espressione:

-   Percorsi di annotazione (i percorsi nelle espressioni devono essere delimitati da "$ (" e ")")
 <br/>
    Esempi:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Valori letterali (stringhe, numeri, true, false, null) <br/>
    Esempi:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Espressioni che usano operatori di confronto (= =,! =, > =, >, < =, <) <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Espressioni che usano operatori booleani (& &, | |,!, ^) <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Espressioni che usano operatori numerici (+,- \*,,/,%) <br/>
    Esempi: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Poiché la competenza condizionale supporta la valutazione, è possibile utilizzarla in scenari di trasformazione secondaria. Vedere, ad esempio, la [definizione Skill 4](#transformation-example).

## <a name="skill-inputs"></a>Input competenze
Gli input fanno distinzione tra maiuscole e minuscole.

| Input   | Descrizione |
|-------------|-------------|
| condition   | Questo input è un [campo valutato](#evaluated-fields) che rappresenta la condizione da valutare. Questa condizione deve restituire un valore booleano (*true* o *false*).   <br/>  Esempi: <br/> "= true" <br/> "= $ (/Document/Language) = =' fr '" <br/> "= $ (/Document/pages/\*/Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata come *true*. Le stringhe delle costanti devono essere restituite tra virgolette singole (' and '). <br/>Valori di esempio: <br/> "=" contratto ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/\*)" <br/> |
| whenFalse   | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata come *false*. <br/>Valori di esempio: <br/> "=" contratto ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/\*)" <br/>

## <a name="skill-outputs"></a>Output competenze
C'è un singolo output denominato semplicemente "output". Restituisce il valore *whenFalse* se la condizione è false o *whenTrue* se la condizione è true.

## <a name="examples"></a>Esempi

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Esempio di definizione di competenze 1: Filtrare i documenti per restituire solo i documenti in francese

L'output seguente restituisce una matrice di frasi ("/document/frenchSentences") se la lingua del documento è francese. Se la lingua non è francese, il valore viene impostato su *null*.

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
Se si usa "/document/frenchSentences" come *contesto* di un'altra competenza, questa skill viene eseguita solo se "/Document/frenchSentences" non è impostato su *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Esempio di definizione di competenze 2: Imposta un valore predefinito per un valore che non esiste

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definizione di competenze di esempio 3: Unire i valori di due campi in uno

In questo esempio alcune frasi hanno una proprietà *frenchSentiment* . Quando la proprietà *frenchSentiment* è null, si vuole usare il valore *englishSentiment* . L'output viene assegnato a un membro denominato sentimentalità ("/Document/sentiment/*/sentiment").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Esempio di definizione di competenze 4: Trasformazione dei dati in un singolo campo

In questo esempio si riceve un *sentimento* compreso tra 0 e 1. Si vuole trasformarla in modo che sia compresa tra-1 e 1. Per eseguire questa trasformazione secondaria, è possibile usare l'abilità condizionale.

In questo esempio non viene usato l'aspetto condizionale della competenza perché la condizione è sempre *true*.

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
Alcuni parametri vengono valutati, pertanto è necessario prestare particolare attenzione a seguire il modello documentato. Le espressioni devono iniziare con un segno di uguale. Un percorso deve essere delimitato da "$ (" e ")". Assicurarsi di inserire le stringhe tra virgolette singole. Che consente all'analizzatore di distinguere tra le stringhe e i percorsi e gli operatori effettivi. Inoltre, assicurarsi di inserire uno spazio vuoto intorno agli operatori (ad esempio, un "*" in un percorso indica un valore diverso da Multiply).


## <a name="next-steps"></a>Passaggi successivi

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
