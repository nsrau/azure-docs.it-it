---
title: Esperienza di ricerca cognitiva condizionale (ricerca di Azure) | Microsoft Docs
description: La competenza condizionale consente il filtraggio, la creazione di impostazioni predefinite e unione di valori.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791509"
---
#   <a name="conditional-skill"></a>Competenze condizionale

Il *competenza condizionale* rende possibili scenari di ricerca di Azure che richiedono un'operazione booleana per determinare i dati da assegnare a un output. Questi scenari includono filtro, assegnando un valore predefinito e unione dei dati basati su una condizione.

Lo pseudocodice seguente viene illustrato ciò che consente di realizzare la competenza condizionale:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Questa esperienza non è associato a un'API di servizi cognitivi di Azure e non vengono effettuati addebiti per il relativo utilizzo. Tuttavia, è necessario comunque [collegare una risorsa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione resource "Gratuito" è limitato a un numero ridotto di miglioramenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campi valutati

Questa esperienza è speciale perché gli input vengono valutati i campi.

Gli elementi seguenti sono i valori validi di un'espressione:

-   I percorsi di annotazione (percorsi nelle espressioni devono essere delimitati da "$(" and ")")
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

-  Le espressioni che utilizzano operatori di confronto (= =,! =, > =, >, < =, <) <br/>
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

-   Le espressioni che utilizzano operatori numerici (+, -, \*, /, %) <br/>
    Esempi: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Poiché la competenza condizionale supporta la valutazione, è possibile utilizzarlo in scenari di minor-trasformazione. Ad esempio, vedere [definizioni delle competenze 4](#transformation-example).

## <a name="skill-inputs"></a>Input competenze
Gli input fanno distinzione tra maiuscole e minuscole.

| Input   | Descrizione |
|-------------|-------------|
| condition   | Questo input è un [valutata campo](#evaluated-fields) che rappresenta la condizione da valutare. Questa condizione deve restituire un valore booleano (*true* oppure *false*).   <br/>  Esempi: <br/> "= true" <br/> "= $(/document/language) = = 'fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata *true*. Le stringhe costanti devono essere restituite tra virgolette singole ('e'). <br/>Valori di esempio: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(odocumento/entità/\*)" <br/> |
| whenFalse   | Questo input è un [campo valutato](#evaluated-fields) che rappresenta il valore da restituire se la condizione viene valutata *false*. <br/>Valori di esempio: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(odocumento/entità/\*)" <br/>

## <a name="skill-outputs"></a>Output competenze
Non ci sia un output singolo che viene semplicemente chiamato "output". Restituisce il valore *whenFalse* se la condizione è false oppure *whenTrue* se la condizione è true.

## <a name="examples"></a>Esempi

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definizione di competenza di esempio 1: Filtrare i documenti in modo che restituisca solo i documenti in francese

L'output seguente restituisce una matrice di frasi ("documenti/frenchSentences") se la lingua del documento è il francese. Se non è la lingua francese, il valore è impostato su *null*.

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
Se "documenti/frenchSentences" viene usato come le *contesto* di un altro competenze, tale competenze viene eseguita solo se "documenti/frenchSentences" non è impostato su *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definizione di competenza di esempio 2: Impostare un valore predefinito per un valore che non esiste

L'output seguente viene creata un'annotazione ("documenti/languageWithDefault") che viene impostata per la lingua del documento o "es" se la lingua non è impostata.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definizione di competenza di esempio 3: Unire i valori da due campi in un unico

In questo esempio, alcuni frasi è necessario un *frenchSentiment* proprietà. Ogni volta che il *frenchSentiment* proprietà è null, è possibile usare i *englishSentiment* valore. L'output viene assegnato a un membro che viene chiamato *sentiment* ("/ documentare/sentiment / * / sentiment").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definizione di competenza di esempio 4: Trasformazione dei dati in un singolo campo

In questo esempio, viene restituito un *sentiment* che è compreso tra 0 e 1. Si vuole trasformare in modo che sia compreso tra -1 e 1. È possibile usare le competenze condizionali per eseguire questa trasformazione secondaria.

In questo esempio, non utilizziamo l'aspetto della competenza condizionale poiché la condizione è sempre *true*.

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
Alcuni parametri vengono valutati, pertanto è necessario prestare particolare attenzione a seguire il modello documentato. Le espressioni devono iniziare con un segno di uguale. Un percorso deve essere delimitato da "$(" and ")". Assicurarsi di inserire le stringhe tra virgolette singole. Che consente l'analizzatore di distinguere tra stringhe e i percorsi effettivi e operatori. Inoltre, assicurarsi di inserire spazi intorno agli operatori (ad esempio, un "*" in un percorso significa che qualcosa di diverso da multiply).


## <a name="next-steps"></a>Passaggi successivi

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
