---
title: Esperienza di ricerca cognitiva condizionale (ricerca di Azure) | Microsoft Docs
description: Competenze condizionale che consente il filtraggio, la creazione di impostazioni predefinite e unione di valori.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028426"
---
#   <a name="conditional-skill"></a>Competenze condizionale

Il **competenza condizionale** consente un'ampia gamma di scenari che richiedono un'operazione booleana per stabilire i dati che devono essere assegnati a un output. Questi scenari includono: applicazione di filtri, assegnare il valore predefinito e unione dei dati basato su una condizione.

Lo pseudocodice seguente viene illustrato ciò che consente di realizzare la competenza condizionale:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Questa competenza non è associata a un'API Servizi cognitivi e non vengono addebitati costi in caso di utilizzo. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campi valutati

Questa esperienza è speciale perché gli input vengono valutati i campi.

Di seguito sono i valori validi di un'espressione:

-   I percorsi di annotazione (percorsi nelle espressioni devono essere delimitati da "$(" and ")") <br/>
    Esempi:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Valori letterali (stringhe, numeri, true, false, null) <br/>
    Esempi:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Le espressioni che usano un operatore di confronto (= =,! =, > =, >, < =, <) <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Le espressioni che utilizzano gli operatori booleani (& &, | |,!, ^) <br/>
    Esempi:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Le espressioni che usano un operatore numerico (+, -, \*, /, %) <br/>
    Esempi: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

A causa della valutazione supportata, la competenza condizionale è utilizzabile per gli scenari di trasformazione secondaria. Vedere l'esempio [definizioni delle competenze 4](#transformation-examples) per un esempio.

## <a name="skill-inputs"></a>Input competenze
Gli input fanno distinzione tra maiuscole e minuscole.

| Input      | DESCRIZIONE |
|-------------|-------------|
| condition   | Questo input è un [valutata campo](#evaluated-fields) che rappresenta la condizione da valutare. Questa condizione deve restituire un valore booleano (true o false).   <br/>  Esempi: <br/> "= true" <br/> "= $(/document/language) = = 'fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Questo input è un [valutata campo](#evaluated-fields). Il valore da restituire se la condizione viene valutata True. Le stringhe costanti devono essere restituite ' ' le virgolette. <br/>Valori di esempio: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(odocumento/entità/\*)" <br/> |
| whenFalse   | Questo input è un [valutata campo](#evaluated-fields). Il valore da restituire se la condizione viene valutata false.  <br/>Valori di esempio: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(odocumento/entità/\*)" <br/>

## <a name="skill-outputs"></a>Output competenze
Non ci sia un output singolo chiamato 'output'. Verrà restituito il valore di whenFalse se la condizione è false o whenTrue se la condizione è true.

## <a name="examples"></a>Esempi

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Definizione di competenza di esempio 1: Filtrare i documenti da restituire solo i documenti "Francesi"

L'output seguente restituirà una matrice di frasi ("documenti/frenchSentences") se la lingua del documento è in francese. Se la lingua non è in francese, tale valore verrà impostato su null.

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
Se "documenti/frenchSentences" viene usato come le *contesto* di competenze di un altro, tale competenze verranno eseguito solo se "documenti/frenchSentences" non è impostato su null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Definizione di competenza di esempio 2: Impostazione di un valore predefinito quando non esiste.

L'output seguente creerà un'annotazione ("documenti/languageWithDefault") che viene impostata per il linguaggio del documento, o "es" se la lingua non è impostata.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Definizione di competenza di esempio 3: Unione di valori di due campi diversi in un singolo campo

In questo esempio, alcuni frasi è necessario un *frenchSentiment* proprietà. Ogni volta che il *frenchSentiment* proprietà è null, si desidera utilizzare il *englishSentiment* valore. L'output viene assegnato a un membro denominato semplicemente *sentiment* ("/ documentare/sentiment / * / sentiment").

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

## <a name="transformation-examples"></a>Esempi di trasformazione
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Definizione di competenza di esempio 4: Eseguire trasformazioni di dati in un singolo campo

In questo esempio, riceviamo un sentiment compreso tra 0 e 1, e si desidera trasformarlo in modo che sia compreso tra -1 e 1. Si tratta di una trasformazione matematica di piccole dimensioni che è possibile eseguire usando le competenze condizionale.

In questo esempio specifico, utilizzeremo mai l'aspetto della competenza condizionale poiché la condizione è sempre true. 

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
Si noti che alcuni dei parametri vengono valutati, pertanto è necessario prestare particolare attenzione seguendo il modello documentato. Le espressioni devono iniziare con un segno di uguale a "=" e i percorsi devono essere delimitati da "$(" and ")". Assicurarsi di inserire le stringhe virgolette' ', quanto sarà utile l'analizzatore di distinguere tra stringhe e i percorsi effettivi e operatori. Inoltre, assicurarsi di inserire uno spazio vuoto intorno agli operatori (ad esempio un * in un percorso ha un significato diverso rispetto all'operatore di moltiplicazione).


## <a name="next-steps"></a>Passaggi successivi

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
