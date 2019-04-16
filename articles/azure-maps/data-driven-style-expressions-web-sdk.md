---
title: Basate sui dati stile espressioni nelle mappe di Azure SDK per Web | Microsoft Docs
description: Come usare le espressioni di stile basato sui dati nelle mappe di Azure SDK per Web.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579496"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Espressioni di stile basato sui dati (Web SDK)

Le espressioni consentono di applicare la logica di business a opzioni di stile che osservare le proprietà definite in ogni forma in un'origine dati. Le espressioni sono anche utilizzabile per filtrare i dati in un'origine dati o un livello. Le espressioni possono essere costituiti da logica condizionale, ad esempio istruzioni if e consente inoltre di modificare i dati con; stringa, operatori logici e matematici. 

Stili basate sui dati possono ridurre la quantità di codice necessario per implementare la logica di business per lo stile. Se usato con i livelli, le espressioni vengono valutate in fase di rendering in un thread separato che offre un miglioramento delle prestazioni rispetto alla valutazione di logica di business sul thread UI.

Il video seguente offre una panoramica di stile basato sui dati nelle mappe di Azure SDK per Web.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le espressioni sono rappresentate come matrici JSON. Il primo elemento di un'espressione nella matrice è una stringa che specifica il nome dell'operatore di espressione. Ad esempio, "+" o "case". Gli elementi successivi (se presente) sono gli argomenti all'espressione. Ogni argomento è un valore letterale (una stringa, numero, valore booleano, o `null`), o un altro array di espressione. Lo pseudocodice seguente definisce la struttura di base di un'espressione. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Mappe di Azure SDK per Web supporta molti tipi di espressioni che possono essere utilizzate in modo autonomo o in combinazione con altre espressioni.

| Tipo di espressioni | DESCRIZIONE |
|---------------------|-------------|
| [Espressioni booleane](#boolean-expressions) | Espressioni booleane offrono un set di espressioni di operatori booleani per valutare i confronti booleani. |
| [Espressioni colori](#color-expressions) | Espressioni colori rendono più semplice creare e modificare i valori di colore. |
| [Espressioni condizionali](#conditional-expressions) | Espressioni condizionali forniscono operazioni per la logica che sono, ad esempio istruzioni if. |
| [Espressioni di dati](#data-expressions) | Fornisce accesso ai dati di proprietà in una funzionalità. |
| [Eseguire l'interpolazione ed eseguire un'istruzione di espressioni](#interpolate-and-step-expressions) | Eseguire l'interpolazione e passaggio espressioni possono essere utilizzate per calcolare i valori lungo una funzione di passaggio o curva interpolata. |
| [Livello di espressioni specifiche](#layer-specific-expressions) | Espressioni speciali che sono applicabili solo a un singolo livello. |
| [Espressioni matematiche](#math-expressions) | Fornisce operatori matematici per eseguire calcoli basati sui dati all'interno del framework di espressione. |
| [Espressioni di operatore di stringa](#string-operator-expressions) | Espressioni di operatore di stringa eseguono operazioni di conversione in stringhe come concatenazione e il caso di conversione. |
| [Espressioni di tipo](#type-expressions) | Espressioni di tipo forniscono strumenti per test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani. |
| [Espressioni di associazione di variabili](#variable-binding-expressions) | Espressioni di associazione variabile let i risultati di un calcolo di essere archiviato in una variabile e viene fatto riferimento altrove in un'espressione più volte senza dover ricalcolare il valore archiviato. |
| [Espressione zoom](#zoom-expression) | Recupera il livello di zoom corrente della mappa in fase di rendering. |

Tutti gli esempi in questo documento verranno utilizzato la funzionalità seguente per illustrare modi diversi in quanto i diversi tipi di espressioni possono essere usati. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Espressioni di dati

Le espressioni di dati forniscono accesso ai dati di proprietà in una funzionalità. 

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['at', number, array]` | object | Recupera un elemento da una matrice. |
| `['geometry-type']` | stringa | Ottiene il tipo di geometria della funzionalità: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Ottiene il valore della proprietà dalle proprietà della funzionalità corrente. Restituisce null se la proprietà richiesta è manca. |
| `['get', string, object]` | value | Ottiene il valore della proprietà dalle proprietà dell'oggetto fornito. Restituisce null se la proprietà richiesta è manca. |
| `['has', string]` | boolean | Determina se le proprietà di una funzionalità hanno la proprietà specificata. |
| `['has', string, object]` | boolean | Determina se le proprietà dell'oggetto hanno la proprietà specificata. |
| `['id']` | value | Ottiene l'ID della funzionalità se presente. |
| `['length', string | array]` | number | Ottiene la lunghezza di una stringa o matrice. |

**esempi**

Proprietà di una funzionalità è accessibile direttamente in un'espressione con un `get` espressione. Nell'esempio seguente usa il valore di "zoneColor" della funzionalità per specificare la proprietà di colore di un livello a bolle. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Nell'esempio precedente funzionerà correttamente se dispongono di tutte le funzionalità di punto di `zoneColor` proprietà, ma se non è così, il colore probabilmente tornerà al "black". Per modificare il colore di fallback, un `case` espressione può essere utilizzata in combinazione con il `has` espressione per verificare se la proprietà esiste e non restituisce invece un colore di fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

I livelli a bolle e il simbolo verranno eseguito il rendering le coordinate di tutte le forme in un'origine dati per impostazione predefinita. Questa operazione può essere eseguita per dare maggiore risalto i vertici di una linea o poligono. Il `filter` opzione del livello può essere usata per limitare il tipo di geometria delle funzionalità esegue il rendering utilizzando un `['geometry-type']` espressione all'interno di un'espressione booleana. Nell'esempio seguente limita in modo che solo un livello bolle `Point` funzionalità vengono sottoposti a rendering.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Nell'esempio seguente consentirà entrambe `Point` e `MultiPoint` funzionalità deve essere sottoposto a rendering. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Analogamente, verrà eseguito il rendering del contorno di poligoni nei livelli di riga. Per disabilitare questo comportamento in un livello di riga, aggiungere un filtro che consente solo `LineString` e `MultiLineString` funzionalità.  

## <a name="math-expressions"></a>Espressioni matematiche

Espressioni matematiche includono operatori matematici per eseguire calcoli basati sui dati all'interno del framework di espressione.

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | Calcola la somma dei numeri specificati. |
| `['-', number]` | number | Sottrae 0 per il numero specificato. |
| `['-', number, number]` | number | Sottrae i primo numeri per il secondo. |
| `['*', number, number, …]` | number | Moltiplica insieme i numeri specificati. |
| `['/', number, number]` | number | Divide il primo numero per il secondo numero. |
| `['%', number, number]` | number | Calcola il resto quando si divide il primo numero per il secondo numero. |
| `['^', number, number]` | number | Calcola il valore del primo valore elevato alla potenza del secondo numero. |
| `['abs', number]` | number | Calcola il valore assoluto del numero specificato. |
| `['acos', number]` | number | Calcola l'arcocoseno del numero specificato. |
| `['asin', number]` | number | Calcola l'arcoseno del numero specificato. |
| `['atan', number]` | number | Calcola l'arcotangente del numero specificato. |
| `['ceil', number]` | number | L'arrotondamento per eccesso al numero intero intero successivo. |
| `['cos', number]` | number | Calcola il coseno del numero specificato. |
| `['e']` | number | Restituisce la costante matematica `e`. |
| `['floor', number]` | number | L'arrotondamento per difetto all'intero intero precedente. |
| `['ln', number]` | number | Calcola il logaritmo naturale del numero specificato. |
| `['ln2']` | number | Restituisce la costante matematica `ln(2)`. |
| `['log10', number]` | number | Calcola il logaritmo in base 10 del numero specificato. |
| `['log2', number]` | number | Calcola il logaritmo di base-due del numero specificato. |
| `['max', number, number, …]` | number | Calcola il numero massimo nel set specificato di numeri. |
| `['min', number, number, …]` | number | Calcola il numero minimo nel set specificato di numeri. |
| `['pi']` | number | Restituisce la costante matematica `PI`. |
| `['round', number]` | number | Consente di arrotondare il numero all'intero più vicino. I valori a metà vengono arrotondati per eccesso. Ad esempio, `['round', -1.5]` restituisce -2. |
| `['sin', number]` | number | Calcola il seno del numero specificato. |
| `['sqrt', number]` | number | Calcola la radice quadrata del numero specificato. |
| `['tan', number]` | number | Calcola la tangente del numero specificato. |
## <a name="boolean-expressions"></a>Espressioni booleane

Espressioni booleane offrono un set di espressioni di operatori booleani per valutare i confronti booleani.

Quando si confrontano i valori, il confronto è fortemente tipizzato. Valori di tipi diversi vengono sempre considerati non uguali. Casi in cui i tipi sono noti sia diverso in fase di analisi vengono considerati non validi e producono un errore di analisi. 

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negazione logica. Restituisce `true` se l'input è `false`, e `false` se l'input è `true`. |
| `['!= ', value, value]` | boolean | Restituisce `true` se i valori di input non sono uguali, `false` in caso contrario. |
| `['<', value, value]` | boolean | Restituisce `true` se il primo input è rigorosamente minore del secondo, `false` in caso contrario. Gli argomenti devono essere stringhe o entrambi i numeri. |
| `['<=', value, value]` | boolean | Restituisce `true` se il primo input è minore o uguale alla seconda, `false` in caso contrario. Gli argomenti devono essere stringhe o entrambi i numeri. |
| `['==', value, value]` | boolean | Restituisce `true` se i valori di input sono uguali, `false` in caso contrario. Gli argomenti devono essere stringhe o entrambi i numeri. |
| `['>', value, value]` | boolean | Restituisce `true` se il primo input è rigorosamente maggiore del secondo, `false` in caso contrario. Gli argomenti devono essere stringhe o entrambi i numeri. |
| `['>=' value, value]` | boolean | Restituisce `true` se il primo input è maggiore o uguale alla seconda, `false` in caso contrario. Gli argomenti devono essere stringhe o entrambi i numeri. |
| `['all', boolean, boolean, …]` | boolean | Restituisce `true` se tutti gli input vengono `true`, `false` in caso contrario. |
| `['any', boolean, boolean, …]` | boolean | Restituisce `true` se uno degli input vengono `true`, `false` in caso contrario. |

## <a name="conditional-expressions"></a>Espressioni condizionali

Espressioni condizionali forniscono operazioni per la logica che sono, ad esempio istruzioni if.

Le espressioni seguenti per la logica condizionale operazioni sui dati di input. Ad esempio, il `case` espressione fornisce la logica "if/then/else" durante il `match` espressione è simile a una "switch-istruzione". 

### <a name="case-expression"></a>Espressione case

Oggetto `case` espressione è un tipo di espressione condizionale che fornisce l'istruzione if, ad esempio per la logica (if/then/else). Questo tipo di espressione illustra un elenco di condizioni booleane e restituisce il valore di output della condizione booleana prima che è true.

Lo pseudocodice seguente definisce la struttura del `case` espressione. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Esempio**

Nell'esempio seguente illustra diverse condizioni booleane fino a quando non ne viene trovato uno che restituisca `true`e quindi restituisce tale valore associato. Se nessuna condizione booleana restituisce `true`, verrà restituito un valore di fallback. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Espressione di ricerca

Oggetto `match` espressione è un tipo di espressione condizionale che fornisce l'istruzione switch, ad esempio per la logica. L'input può essere qualsiasi espressione, ad esempio `['get', 'entityType']` che restituisce una stringa o un numero. Ogni etichetta deve essere un singolo valore letterale o una matrice di valori letterali, i cui valori devono essere tutte le stringhe o tutti i numeri. L'input corrispondente se uno dei valori nella matrice corrispondono. Ogni etichetta deve essere univoco. Se il tipo di input non corrisponde al tipo delle etichette, il risultato sarà il valore di fallback.

Lo pseudocodice seguente definisce la struttura del `match` espressione. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**esempi**

L'esempio seguente esamina il `entityType` proprietà di una funzionalità del punto in un livello bolle Cerca una corrispondenza. Se viene trovata una corrispondenza, che specificato valore restituito o restituisce il valore di fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

L'esempio seguente usa una matrice per visualizzare l'elenco di un set di etichette che deve restituire tutte lo stesso valore. Ciò è molto più efficiente rispetto a elencare singolarmente ogni etichetta. In questo caso, se il `entityType` proprietà è "ristorante" o "grocery_store", verrà restituito il colore "red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Espressione COALESCE

Oggetto `coalesce` espressione illustra un set di espressioni finché non viene ottenuto il primo valore non null e non restituisce tale valore. 

Lo pseudocodice seguente definisce la struttura del `coalesce` espressione. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Esempio**

L'esempio seguente usa un' `coalesce` espressione per impostare il `textField` opzione di un livello di simboli. Se il `title` non è presente la funzionalità o il set di proprietà `null`, l'espressione tenta quindi cercando il `subtitle` proprietà, se relativo mancante o `null`, eseguirà quindi il fallback su una stringa vuota. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Espressioni di tipo

Espressioni di tipo forniscono strumenti per test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani.

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matrice \| oggetto | Restituisce un valore letterale di matrice o oggetto. Usare questa espressione per impedire che una matrice o oggetto valutato come espressione. Questo è necessario quando una matrice o oggetto deve essere restituito da un'espressione. |
| `['to-boolean', value]` | boolean | Converte il valore di input in un valore booleano. Il risultato viene `false` quando l'input è una stringa vuota `0`, `false`, `null`, o `NaN`; in caso contrario relativo `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte il valore di input in un colore. Se vengono specificati più valori, ognuno di essi viene valutato nell'ordine fino a quando non viene ottenuta della prima conversione ha esito positivo. Se nessuno degli input possono essere convertiti, l'espressione è un errore. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | Converte il valore di input in un numero, se possibile. Se l'input è `null` o `false`, il risultato è 0. Se l'input è `true`, il risultato è 1. Se l'input è una stringa, viene convertito in un numero utilizzando il [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) stringa funzione della specifica del linguaggio ECMAScript. Se vengono specificati più valori, ognuno di essi viene valutato nell'ordine fino a quando non viene ottenuta della prima conversione ha esito positivo. Se nessuno degli input possono essere convertiti, l'espressione è un errore. |
| `['to-string', value]` | stringa | Converte il valore di input in una stringa. Se l'input è `null`, il risultato è `""`. Se l'input è un valore booleano, il risultato viene `"true"` o `"false"`. Se l'input è un numero, viene convertito in una stringa usando il [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) numero funzione della specifica del linguaggio ECMAScript. Se l'input è un colore, questo viene convertito in stringa di colore CSS RGBA `"rgba(r,g,b,a)"`. In caso contrario, l'input viene convertito in stringa utilizzando il [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funzione della specifica del linguaggio ECMAScript. |
| `['typeof', value]` | stringa | Restituisce una stringa che descrive il tipo del valore specificato. |

> [!TIP]
> Se un messaggio di errore simile a `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` nella console del browser viene visualizzato, che vi sia un'espressione in una posizione nel codice che dispone di una matrice che non dispone di una stringa per il primo valore. Se si desidera che l'espressione per restituire una matrice, eseguire il wrapping con la matrice di `literal` espressione. Nell'esempio seguente imposta l'icona `offset` opzione di un livello di simboli, che deve essere una matrice contenente due numeri, utilizzando un `match` espressione scegliere tra due valori di offset in base al valore della `entityType` proprietà del punto funzionalità.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Espressioni colori

Espressioni colori rendono più semplice creare e modificare i valori di colore.

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crea un valore di colore da *rossa*, *verde*, e *blu* componenti che deve essere compresa tra `0` e `255`e un componente alfa di `1`. Se qualsiasi componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['rgba', number, number, number, number]` | color | Crea un valore di colore da *rossa*, *verde*, *blu* componenti che deve essere compresa tra `0` e `255`e un componente alfa con un valore compreso fra `0` e `1`. Se qualsiasi componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['to-rgba']` | \[number, number, number, number\] | Restituisce una matrice di quattro elementi che contiene il colore di input *rossa*, *verde*, *blu*, e *alfa* componenti, in quest'ordine. |

**Esempio**

Nell'esempio seguente viene creata e il valore di colore RGB che ha un *rossa* pari a `255`, e *verde* e *blu* valori calcolati moltiplicando `2.5` dal valore della `temperature` proprietà. Come i cambiamenti di temperatura in diverse gradazioni di verrà modificato il colore *rosso*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Espressioni di operatore di stringa

Espressioni di operatore di stringa eseguono operazioni di conversione in stringhe come concatenazione e il caso di conversione. 

| Expression | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['concat', string, string, …]` | stringa | Concatena più stringhe. Ogni valore deve essere una stringa. Usare il `to-string` digitare l'espressione per convertire altri tipi di valore in stringa, se necessario. |
| `['downcase', string]` | stringa | Converte la stringa specificata in minuscolo. |
| `['upcase', string]` | stringa | Converte la stringa specificata in maiuscolo. |

**Esempio**

L'esempio seguente converte la `temperature` proprietà del punto di funzionalità in una stringa e quindi concatenati "° F" alla fine di esso.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

L'espressione sopra indicata viene eseguito il rendering di un pin sulla mappa con il testo "64° F" da sovrapporre al suo interno come illustrato nell'immagine seguente.

<center>

![Esempio di espressione di operatore di stringa](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Eseguire l'interpolazione ed eseguire un'istruzione di espressioni

Eseguire l'interpolazione e passaggio espressioni possono essere utilizzate per calcolare i valori lungo una funzione di passaggio o curva interpolata. Eseguire queste espressioni in un'espressione che restituisce un valore numerico come input, ad esempio `['get',  'temperature']`. Il valore di input viene valutato rispetto a coppie di valori di input e outpui, denominati "Arresta", per determinare il valore che meglio si adatta la funzione interpolata curva o passaggio. I valori di input per ogni arresto devono essere un numero e in ordine crescente. I valori di output devono essere un numero e matrice di numeri o un colore.

### <a name="interpolate-expression"></a>Espressione di interpolazione

Un `interpolate` espressione può essere utilizzata per calcolare un set di valori continuo e smooth dall'interpolazione tra valori di arresto. Un `interpolate` espressione che restituisce valori di colore produce una sfumatura di colore in cui i valori sono selezionati da.

Esistono tre tipi di metodi di interpolazione che possono essere usati in un `interpolate` espressione:
 
* `['linear']` -Crea un'interpolazione lineare tra la coppia di arresto.
* `['exponential', base]` -Interpola in modo esponenziale tra l'arresto. Il `base` valore determina la frequenza con cui aumenta l'output. I valori più alti rendono l'output aumentare più verso la fine dell'intervallo alta. Oggetto `base` valore prossimo a 1 produce un output che aumenta in modo più lineare.
* `['cubic-bezier', x1, y1, x2, y2]` -Esegue l'interpolazione usando un [curva di Bézier cubica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definite dai punti di controllo specificato.

Di seguito è riportato un esempio dell'aspetto di questi diversi tipi di interpolazione. 

| Lineari  | Esponenziali | Curva di Bezier cubica |
|---------|-------------|--------------|
| ![Grafico l'interpolazione lineare](media/how-to-expressions/linear-interpolation.png) | ![Grafico di interpolazione esponenziale](media/how-to-expressions/exponential-interpolation.png) | ![Grafico di interpolazione curva di Bezier cubica](media/how-to-expressions/bezier-curve-interpolation.png) |

Lo pseudocodice seguente definisce la struttura del `interpolate` espressione. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Esempio**

L'esempio seguente usa un' `linear interpolate` espressione per impostare il `color` proprietà di un livello a bolle in base il `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, "blue" verrà restituito, se tra 60 e meno di 70, verrà restituito giallo, se tra 70 e inferiore a 80, "arancione" verrà restituito, se viene restituito, maggiore o uguale a 80 "red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

La figura seguente viene illustrato come i colori vengono scelti per l'espressione sopra indicata.
 
<center>

![Esempio di espressione di interpolazione](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Espressione Step

Oggetto `step` espressione può essere utilizzata per calcolare i valori discreti, con rientri risultato dalla valutazione di un [funzione costante a tratti](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definito da si arresta. 

Lo pseudocodice seguente definisce la struttura del `step` espressione. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Le espressioni di passaggio restituiscono il valore di output dell'arresto immediatamente prima del valore di input o il primo valore di input se l'input è inferiore al primo punto. 

**Esempio**

L'esempio seguente usa un' `step` espressione per impostare il `color` proprietà di un livello a bolle in base il `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, "blue" verrà restituito, se tra 60 e meno di 70, "giallo" verrà restituito, se tra 70 e inferiore a 80, "arancione" verrà restituito, se viene restituito, maggiore o uguale a 80 "red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

La figura seguente viene illustrato come i colori vengono scelti per l'espressione sopra indicata.
 
<center>

![Esempio di espressione di passaggio](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Livello di espressioni specifiche

Espressioni speciale che si applicano solo a specifici livelli.

### <a name="heat-map-density-expression"></a>Espressione di densità della mappa termica

Un'espressione di densità della mappa termica recupera il valore di densità della mappa termica per ogni pixel contenuto in un livello mappa termica e viene definita come `['heatmap-density']`. Questo valore è un numero compreso tra `0` e `1` e viene usato in combinazione con un `interpolation` o `step` espressione per definire la sfumatura di colore utilizzata per colorare la mappa termica. Questa espressione può essere utilizzata solo nel [colori opzione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) del livello mappa termica.

> [!TIP]
> Il colore in corrispondenza dell'indice 0 in un'espressione di interpolazione o il colore predefinito di un colore di passaggio, definisce il colore dell'area in cui è presente alcun dato e può essere usato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. 

**Esempio**

In questo esempio Usa un'espressione di interpolazione lineare per creare una sfumatura di colore uniforme per il rendering della mappa termica. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Oltre a usare una sfumatura smussata leggano una mappa termica, i colori possono essere specificati in un set di intervalli con una `step` espressione. Usando un `step` espressione per colorare la mappa termica suddivide la densità più in modo simile a una mappa di tipo radar o contour visivamente in intervalli.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Per altre informazioni, vedere la [aggiungere un livello mappa termica](map-add-heat-map-layer.md) documentazione.

### <a name="line-progress-expression"></a>Espressione stato di avanzamento riga

Un'espressione di stato di avanzamento riga recupera lo stato di avanzamento di una linea sfumata in un livello di riga e viene definita come `['line-progress']`. Questo valore è un numero compreso tra 0 e 1 e viene usato in combinazione con un `interpolation` o `step` espressione. Questa espressione può essere utilizzata solo con il [strokeGradient opzione]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) del livello di riga. 

> [!NOTE]
> Il `strokeGradient` l'opzione del livello di riga richiede la `lineMetrics` opzione dell'origine dati verrà impostato su `true`.

**Esempio**

L'esempio seguente usa il `['line-progress']` espressione per applicare una sfumatura di colore per il tratto di una riga.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Vedere l'esempio in tempo reale](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Espressione di formato di campo di testo

L'espressione di formato di campo di testo può essere utilizzato con il `textField` opzione dei livelli di simbolo `textOptions` proprietà per fornire una formattazione di testo misti. Questa espressione consente a un set di stringhe di input e di specificare opzioni di formattazione. Le opzioni seguenti possono essere specificate per ogni stringa di input in questa espressione.

 * `'font-scale'` -Specifica il fattore di scala per le dimensioni del carattere. Se specificato, questo valore sostituirà il `size` proprietà del `textOptions` per la stringa singola.
 * `'text-font'` : Specifica uno o più famiglie di caratteri che devono essere utilizzate per questa stringa. Se specificato, questo valore sostituirà il `font` proprietà del `textOptions` per la stringa singola.

Lo pseudocodice seguente definisce la struttura dell'espressione di formato del campo di testo. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Esempio**

L'esempio seguente formatta il campo di testo aggiungendo un carattere in grassetto e aumentare le dimensioni del carattere di `title` proprietà della funzionalità. Questo esempio viene inoltre aggiunto il `subtitle` proprietà della funzionalità in una nuova riga, con un fattore di scala verso il basso la dimensione del carattere.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Questo livello forniranno la funzionalità punto come illustrato nell'immagine seguente:
 
<center>

![Immagine della funzionalità punto con il campo di testo formattato](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Espressione di formato numerico

Il `number-format` espressione può essere utilizzata solo con il `textField` opzione di un livello di simboli. Questa espressione consente di convertire il numero specificato in una stringa formattata. Questa espressione esegue il wrapping di JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) funzionare e supporta il set di opzioni seguente.

 * `locale` -Specificare questa opzione per la conversione di numeri in stringhe in modo che si allinea con la lingua specificata. Passare un [tag di lingua BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) in questa opzione.
 * `currency` -Per convertire il numero in una stringa che rappresenta una valuta. I valori possibili sono i [codici di valuta ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), ad esempio "USD" per il dollaro Statunitense, "A" per il simbolo dell'euro, o "CNY" per il cinese RMB.
 * `'min-fraction-digits'` -Specifica il numero minimo di cifre decimali da includere nella versione di stringa del numero.
 * `'max-fraction-digits'` -Specifica il numero massimo di cifre decimali da includere nella versione di stringa del numero.

Lo pseudocodice seguente definisce la struttura dell'espressione di formato del campo di testo. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Esempio**

L'esempio seguente usa un `number-format` espressione di modificare il `revenue` proprietà della funzionalità punto viene eseguito il rendering nel `textField` opzione di un simbolo di livello in modo che venga visualizzato un valore in dollari US.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Questo livello forniranno la funzionalità punto come illustrato nell'immagine seguente:

<center>

![Esempio di espressione di formato numerico](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Espressione zoom

Oggetto `zoom` espressione viene usata per recuperare il livello di zoom corrente della mappa in fase di rendering e viene definita come `['zoom']`. Questa espressione restituisce un numero compreso tra l'intervallo a livello di zoom minimo e massimo della mappa. L'uso di questa espressione consente gli stili essere modificato in modo dinamico, poiché il livello di zoom della mappa viene modificato. Il `zoom` espressione può essere utilizzata solo con `interpolate` e `step` espressioni.

**Esempio**

Per impostazione predefinita, i raggi di punti dati viene eseguito il rendering nel livello mappa termica hanno un raggio fisso in pixel per tutti i livelli di zoom. Come la mappa viene ingrandita o ridotta di aggregazioni di dati contemporaneamente e il livello mappa termica con interruzioni ha un aspetto diverso. Oggetto `zoom` espressione può essere utilizzata per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copre la stessa area della mappa fisica. In questo modo il livello mappa termica aspetto più statico e coerente. Ogni livello di zoom della mappa ha due volte come numero di pixel verticalmente e orizzontalmente il livello di zoom precedente. Ridimensionamento di radius in modo che raddoppia a ogni livello di zoom creerà una mappa termica che un aspetto coerenza in tutti i livelli di zoom. Questa operazione può essere eseguita utilizzando il `zoom` espressione con un `base 2 exponential interpolation` espressione, come illustrato di seguito. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Vedere l'esempio in tempo reale](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Espressioni di associazione di variabili

Le espressioni di associazione variabile archiviano i risultati di un calcolo in una variabile in modo che è possibile farvi riferimento in un' posizione in un'espressione più volte senza dover ricalcolare lo. Si tratta di un'ottimizzazione utile per le espressioni che prevedono numerosi calcoli

| Expression | Tipo restituito | DESCRIZIONE |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome1: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: qualsiasi,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome2: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: qualsiasi,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Archivia uno o più valori come variabili da usare per il `var` dell'espressione figlio che restituisce il risultato. |
| `['var', name: string]` | qualsiasi | Fa riferimento a una variabile che è stata creata utilizzando il `let` espressione. |

**Esempio**

Questo esempio Usa un'espressione che calcola il ricavo rispetto al rapporto di temperatura e quindi viene usato un `case` espressione da valutare diverse operazioni booleane su questo valore. Il `let` espressione viene usata per archiviare i ricavi rispetto al rapporto di temperatura in modo che solo deve essere calcolata una sola volta e `var` espressione fa riferimento a questa variabile con la frequenza in base alle esigenze senza dover ricalcolare lo.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altri esempi di codice che implementano le espressioni:

> [!div class="nextstepaction"] 
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Aggiungere forme](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)

Altre informazioni sulle opzioni di livello che supportano le espressioni:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
