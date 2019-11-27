---
title: Espressioni di stile basate sui dati in Azure Maps Web SDK | Microsoft Docs
description: Come usare le espressioni di stile basate sui dati in Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 6cd69ba8abe243daadf5d517ab7c5a224953cc99
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480635"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Espressioni di stile basate sui dati (SDK Web)

Le espressioni consentono di applicare la logica di business alle opzioni di stile che osservano le proprietà definite in ogni forma di un'origine dati. Le espressioni possono inoltre essere utilizzate per filtrare i dati in un'origine dati o in un livello. Le espressioni possono essere costituite da logica condizionale, ad esempio If-statements, e possono essere usate anche per modificare i dati con. operatori di stringa, logici e matematici. 

Gli stili basati sui dati possono ridurre la quantità di codice necessario per implementare la logica di business per lo stile. Quando vengono usati con i livelli, le espressioni vengono valutate in fase di rendering in un thread separato che garantisce prestazioni migliori rispetto alla valutazione della logica di business nel thread dell'interfaccia utente.

Il video seguente offre una panoramica dello stile basato sui dati in Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le espressioni sono rappresentate come matrici JSON. Il primo elemento di un'espressione nella matrice è una stringa che specifica il nome dell'operatore di espressione. Ad esempio, "+" o "case". Gli elementi successivi (se presenti) sono gli argomenti dell'espressione. Ogni argomento è un valore letterale (stringa, numero, valore booleano o `null`) o un'altra matrice di espressioni. Lo pseudocodice seguente definisce la struttura di base di un'espressione. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK supporta molti tipi di che possono essere usati autonomamente o in combinazione con altre espressioni.

| Tipo di espressioni | DESCRIZIONE |
|---------------------|-------------|
| [Espressione di aggregazione](#aggregate-expression) | Espressione che definisce un calcolo elaborato su un set di dati e che può essere utilizzato con l'opzione `clusterProperties` di un `DataSource`. |
| [Espressioni booleane](#boolean-expressions) | Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani. |
| [Espressioni colore](#color-expressions) | Le espressioni colore semplificano la creazione e la modifica dei valori dei colori. |
| [Espressioni condizionali](#conditional-expressions) | Le espressioni condizionali forniscono operazioni logiche simili a If-Statements. |
| [Espressioni di dati](#data-expressions) | Consente di accedere ai dati delle proprietà in una funzionalità. |
| [Espressioni interpolate e Step](#interpolate-and-step-expressions) | Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. |
| [Espressioni specifiche del livello](#layer-specific-expressions) | Espressioni speciali applicabili solo a un singolo livello. |
| [Espressioni matematiche](#math-expressions) | Fornisce gli operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni. |
| [Espressioni operatore stringa](#string-operator-expressions) | Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. |
| [Espressioni di tipo](#type-expressions) | Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani. |
| [Espressioni di associazione variabili](#variable-binding-expressions) | Le espressioni di associazione di variabili consentono di archiviare i risultati di un calcolo in una variabile e di farvi riferimento altrove in un'espressione senza dover ricalcolare il valore archiviato. |
| [Espressione zoom](#zoom-expression) | Recupera il livello di zoom corrente della mappa in fase di rendering. |

Tutti gli esempi in questo documento utilizzeranno la seguente funzionalità per illustrare i diversi modi in cui è possibile usare i diversi tipi di espressioni. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
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

Le espressioni di dati consentono di accedere ai dati delle proprietà di una funzionalità. 

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['at', number, array]` | oggetto | Recupera un elemento da una matrice. |
| `['geometry-type']` | stringa | Ottiene il tipo di geometria della funzionalità: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | Valore | Ottiene il valore della proprietà dalle proprietà della funzionalità corrente. Restituisce null se la proprietà richiesta è mancante. |
| `['get', string, object]` | Valore | Ottiene il valore della proprietà dalle proprietà dell'oggetto specificato. Restituisce null se la proprietà richiesta è mancante. |
| `['has', string]` | boolean | Determina se le proprietà di una funzionalità dispongono della proprietà specificata. |
| `['has', string, object]` | boolean | Determina se le proprietà dell'oggetto dispongono della proprietà specificata. |
| `['id']` | Valore | Ottiene l'ID della funzionalità se ne è presente uno. |
| `['length', string | array]` | number | Ottiene la lunghezza di una stringa o di una matrice. |

**esempi**

È possibile accedere alle proprietà di una funzionalità direttamente in un'espressione usando un'espressione `get`. Nell'esempio seguente viene usato il valore "zoneColor" della funzionalità per specificare la proprietà Color di un livello Bubble. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

L'esempio precedente funzionerà correttamente se tutte le funzionalità del punto hanno la proprietà `zoneColor`, ma se non lo sono, il colore ritornerà a "nero". Per modificare il colore di fallback, è possibile usare un'espressione `case` in combinazione con l'espressione `has` per verificare se la proprietà esiste e se non restituisce invece un colore di fallback.

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

Per impostazione predefinita, i livelli Bubble e Symbol eseguiranno il rendering delle coordinate di tutte le forme di un'origine dati. Questa operazione può essere eseguita per evidenziare i vertici di un poligono o di una linea. L'opzione `filter` del livello può essere usata per limitare il tipo di geometria delle funzionalità di cui esegue il rendering usando un'espressione `['geometry-type']` all'interno di un'espressione booleana. Nell'esempio seguente viene limitato un livello Bubble in modo che venga eseguito il rendering solo delle funzionalità `Point`.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

L'esempio seguente consente di eseguire il rendering delle funzionalità `Point` e `MultiPoint`. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Analogamente, la struttura dei poligoni viene sottoposta a rendering nei livelli linea. Per disabilitare questo comportamento in un livello linea, aggiungere un filtro che consente solo le funzionalità di `LineString` e `MultiLineString`.  

## <a name="math-expressions"></a>Espressioni matematiche

Le espressioni matematiche forniscono operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni.

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | Calcola la somma dei numeri specificati. |
| `['-', number]` | number | Sottrae 0 per il numero specificato. |
| `['-', number, number]` | number | Sottrae i primi numeri per il secondo numero. |
| `['*', number, number, …]` | number | Moltiplica i numeri specificati insieme. |
| `['/', number, number]` | number | Divide il primo numero per il secondo numero. |
| `['%', number, number]` | number | Calcola il resto quando si divide il primo numero per il secondo numero. |
| `['^', number, number]` | number | Calcola il valore del primo valore elevato alla potenza del secondo numero. |
| `['abs', number]` | number | Calcola il valore assoluto del numero specificato. |
| `['acos', number]` | number | Calcola l'arcoseno del numero specificato. |
| `['asin', number]` | number | Calcola l'arcoseno del numero specificato. |
| `['atan', number]` | number | Calcola l'arcotangente del numero specificato. |
| `['ceil', number]` | number | Arrotonda il numero per eccesso al numero intero successivo. |
| `['cos', number]` | number | Calcola il coseno del numero specificato. |
| `['e']` | number | Restituisce la costante matematica `e`. |
| `['floor', number]` | number | Arrotonda il numero per difetto all'intero intero precedente. |
| `['ln', number]` | number | Calcola il logaritmo naturale del numero specificato. |
| `['ln2']` | number | Restituisce la costante matematica `ln(2)`. |
| `['log10', number]` | number | Calcola il logaritmo in base 10 del numero specificato. |
| `['log2', number]` | number | Calcola il logaritmo in base due del numero specificato. |
| `['max', number, number, …]` | number | Calcola il numero massimo nel set di numeri specificato. |
| `['min', number, number, …]` | number | Calcola il numero minimo nel set di numeri specificato. |
| `['pi']` | number | Restituisce la costante matematica `PI`. |
| `['round', number]` | number | Arrotonda il numero all'intero più vicino. I valori a metà vengono arrotondati per eccesso da zero. Ad esempio, `['round', -1.5]` restituisce-2. |
| `['sin', number]` | number | Calcola il seno del numero specificato. |
| `['sqrt', number]` | number | Calcola la radice quadrata del numero specificato. |
| `['tan', number]` | number | Calcola la tangente del numero specificato. |

## <a name="aggregate-expression"></a>Espressione di aggregazione

Un'espressione di aggregazione definisce un calcolo elaborato su un set di dati e può essere utilizzato con l'opzione `clusterProperties` di un `DataSource`. L'output di queste espressioni deve essere un numero o un valore booleano. 

Un'espressione di aggregazione accetta tre valori. un valore di operatore, un valore iniziale e un'espressione per recuperare una proprietà da ogni funzionalità di un dato a cui applicare l'operazione di aggregazione. Questa espressione ha il formato seguente:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- Operator: funzione di espressione a cui viene quindi applicata una funzione in base a tutti i valori calcolati dal `mapExpression` per ogni punto del cluster. Operatori supportati; 
    - Per i numeri: `+`, `*`, `max``min`
    - Per i valori booleani: `all`, `any`
- initialValue: valore iniziale in cui viene aggregato il primo valore calcolato.
- mapExpression: espressione applicata a ogni punto del set di dati.

**esempi**

Se tutte le funzionalità di un set di dati dispongono di una proprietà `revenue` che è un numero. Il ricavo totale di tutti i punti in un cluster creato dal set di dati può essere calcolato usando la seguente espressione di aggregazione: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Espressioni booleane

Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani.

Quando si confrontano i valori, il confronto è fortemente tipizzato. I valori di tipi diversi sono sempre considerati non uguali. I casi in cui i tipi sono noti come diversi in fase di analisi sono considerati non validi e genereranno un errore di analisi. 

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negazione logica. Restituisce `true` se l'input è `false`e `false` se l'input è `true`. |
| `['!= ', value, value]` | boolean | Restituisce `true` se i valori di input non sono uguali, `false` in caso contrario. |
| `['<', value, value]` | boolean | Restituisce `true` se il primo input è strettamente inferiore al secondo, `false` in caso contrario. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['<=', value, value]` | boolean | Restituisce `true` se il primo input è minore o uguale al secondo, `false` in caso contrario. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['==', value, value]` | boolean | Restituisce `true` se i valori di input sono uguali, `false` in caso contrario. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['>', value, value]` | boolean | Restituisce `true` se il primo input è rigorosamente maggiore del secondo, `false` in caso contrario. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['>=' value, value]` | boolean | Restituisce `true` se il primo input è maggiore o uguale al secondo, `false` in caso contrario. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `['all', boolean, boolean, …]` | boolean | Restituisce `true` se tutti gli input sono `true`, `false` in caso contrario. |
| `['any', boolean, boolean, …]` | boolean | Restituisce `true` se uno degli input è `true`, `false` in caso contrario. |

## <a name="conditional-expressions"></a>Espressioni condizionali

Le espressioni condizionali forniscono operazioni logiche simili a If-Statements.

Le espressioni seguenti eseguono operazioni di logica condizionale sui dati di input. Ad esempio, l'espressione `case` fornisce la logica "if/then/else" mentre l'espressione `match` è simile a "switch-Statement". 

### <a name="case-expression"></a>Espressione case

Un'espressione `case` è un tipo di espressione condizionale che fornisce If-statement come logica (if/then/else). Questo tipo di espressione segue un elenco di condizioni booleane e restituisce il valore di output della prima condizione booleana che è true.

Lo pseudocodice seguente definisce la struttura dell'espressione `case`. 

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

Nell'esempio seguente vengono illustrate le diverse condizioni booleane fino a quando non ne viene trovato uno che restituisce `true`, quindi viene restituito il valore associato. Se nessuna condizione booleana restituisce `true`, verrà restituito un valore di fallback. 

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

### <a name="match-expression"></a>Espressione di corrispondenza

Un'espressione `match` è un tipo di espressione condizionale che fornisce un'istruzione switch come la logica. L'input può essere qualsiasi espressione, ad esempio `['get', 'entityType']` che restituisce una stringa o un numero. Ogni etichetta deve essere un singolo valore letterale o una matrice di valori letterali, i cui valori devono essere costituiti da tutte le stringhe o da tutti i numeri. L'input corrisponde a se uno dei valori nella matrice corrisponde. Ogni etichetta deve essere univoca. Se il tipo di input non corrisponde al tipo delle etichette, il risultato sarà il valore di fallback.

Lo pseudocodice seguente definisce la struttura dell'espressione `match`. 

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

Nell'esempio seguente viene analizzata la proprietà `entityType` di una funzionalità punto in un livello Bubble che cerca una corrispondenza. Se viene trovata una corrispondenza, viene restituito il valore specificato o viene restituito il valore di fallback.

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

Nell'esempio seguente viene utilizzata una matrice per elencare un set di etichette che devono restituire tutti lo stesso valore. Questa operazione è molto più efficiente rispetto all'elenco di ogni etichetta singolarmente. In questo caso, se la proprietà `entityType` è "Restaurant" o "grocery_store", verrà restituito il colore "Red".

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

Nell'esempio seguente viene usata un'espressione di corrispondenza per eseguire un filtro di tipo "in Array" o "array contains", in questo caso filtrando i dati con un valore ID incluso in un elenco di ID consentiti. Quando si utilizzano espressioni con filtri, il risultato deve essere un valore booleano.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Espressione COALESCE

Un'espressione `coalesce` viene descritta in un set di espressioni fino a quando non viene ottenuto il primo valore non null e restituisce tale valore. 

Lo pseudocodice seguente definisce la struttura dell'espressione `coalesce`. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Esempio**

Nell'esempio seguente viene utilizzata un'espressione `coalesce` per impostare l'opzione `textField` di un livello di simboli. Se nella funzionalità manca la proprietà `title` o è impostata su `null`, l'espressione tenterà di cercare la proprietà `subtitle`, se manca o `null`, eseguirà il fallback a una stringa vuota. 

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

Nell'esempio seguente viene utilizzata un'espressione `coalesce` per recuperare la prima icona di immagine disponibile disponibile nello sprite della mappa da un elenco di nomi di immagine specificati.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Espressioni di tipo

Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani.

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | Array \| oggetto | Restituisce una matrice di valori letterali o un valore dell'oggetto. Utilizzare questa espressione per impedire la valutazione di una matrice o di un oggetto come espressione. Questa operazione è necessaria quando un'espressione deve restituire una matrice o un oggetto. |
| `['image', string]` | stringa | Verifica se un ID immagine specificato viene caricato nello sprite dell'immagine maps. Se è, viene restituito l'ID; in caso contrario, viene restituito null. |
| `['to-boolean', value]` | boolean | Converte il valore di input in un valore booleano. Il risultato è `false` quando l'input è una stringa vuota, `0`, `false`, `null`o `NaN`; in caso contrario, il `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte il valore di input in un colore. Se vengono specificati più valori, ognuno di essi viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se non è possibile convertire nessuno degli input, l'espressione è un errore. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | Converte il valore di input in un numero, se possibile. Se l'input è `null` o `false`, il risultato è 0. Se l'input è `true`, il risultato è 1. Se l'input è una stringa, viene convertito in un numero utilizzando la funzione di stringa [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) della specifica del linguaggio ECMAScript. Se vengono specificati più valori, ognuno di essi viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se non è possibile convertire nessuno degli input, l'espressione è un errore. |
| `['to-string', value]` | stringa | Converte il valore di input in una stringa. Se l'input è `null`, il risultato viene `""`. Se l'input è un valore booleano, il risultato è `"true"` o `"false"`. Se l'input è un numero, viene convertito in una stringa utilizzando la funzione numero [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) della specifica del linguaggio ECMAScript. Se l'input è un colore, viene convertito nella stringa CSS RGBA color `"rgba(r,g,b,a)"`. In caso contrario, l'input viene convertito in una stringa utilizzando la funzione [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) della specifica del linguaggio ECMAScript. |
| `['typeof', value]` | stringa | Restituisce una stringa che descrive il tipo del valore specificato. |

> [!TIP]
> Se nella console del browser viene visualizzato un messaggio di errore simile a `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`, significa che nel codice è presente un'espressione che contiene una matrice che non dispone di una stringa per il primo valore. Se si desidera che l'espressione restituisca una matrice, eseguire il wrapping della matrice con l'espressione `literal`. Nell'esempio seguente viene impostata l'opzione Icon `offset` di un livello Symbol, che deve essere una matrice contenente due numeri, usando un'espressione `match` per scegliere tra due valori di offset in base al valore della proprietà `entityType` della funzionalità Point.
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
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Espressioni colore

Le espressioni colore semplificano la creazione e la modifica dei valori dei colori.

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crea un valore di colore dai componenti *rosso*, *verde*e *blu* che devono variare tra `0` e `255`e un componente alfa di `1`. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['rgba', number, number, number, number]` | color | Crea un valore di colore dai componenti *rosso*, *verde*, *blu* che devono variare tra `0` e `255`e un componente alfa in un intervallo di `0` e `1`. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['to-rgba']` | \[numero, numero, numero, numero\] | Restituisce una matrice a quattro elementi contenente i componenti *rosso*, *verde*, *blu*e *alfa* del colore di input, in questo ordine. |

**Esempio**

Nell'esempio seguente viene creato un valore di colore RGB con un valore *rosso* `255`e i valori *verde* e *blu* calcolati moltiplicando `2.5` per il valore della proprietà `temperature`. Con la variazione della temperatura, il colore cambierà in tonalità diverse di *rosso*.

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

## <a name="string-operator-expressions"></a>Espressioni operatore stringa

Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. 

| Espressione | Tipo restituito | DESCRIZIONE |
|------------|-------------|-------------|
| `['concat', string, string, …]` | stringa | Concatena più stringhe insieme. Ogni valore deve essere una stringa. Se necessario, utilizzare l'espressione di tipo `to-string` per convertire altri tipi valore in stringa. |
| `['downcase', string]` | stringa | Converte la stringa specificata in caratteri minuscoli. |
| `['upcase', string]` | stringa | Converte la stringa specificata in lettere maiuscole. |

**Esempio**

Nell'esempio seguente la proprietà `temperature` della funzionalità Point viene convertita in una stringa e quindi viene concatenato "° f" alla fine.

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

L'espressione precedente esegue il rendering di un pin sulla mappa con il testo "64 ° f" sovrapposto, come illustrato nell'immagine seguente.

<center>

esempio di espressione dell'operatore stringa ![](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Espressioni interpolate e Step

Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. Queste espressioni accettano un'espressione che restituisce un valore numerico come input, ad esempio `['get',  'temperature']`. Il valore di input viene valutato a fronte di coppie di valori di input e output, denominate "Stops", per determinare il valore che meglio si adatta alla curva o alla funzione Step interpolata. I valori di input per ogni interruzione devono essere un numero ed essere in ordine crescente. I valori di output devono essere un numero, una matrice di numeri o un colore.

### <a name="interpolate-expression"></a>Espressione interpolate

È possibile utilizzare un'espressione `interpolate` per calcolare un set continuo e uniforme di valori mediante l'interpolazione tra i valori di interruzione. Un'espressione `interpolate` che restituisce i valori dei colori produce una sfumatura di colore in cui vengono selezionati i valori dei risultati.

Esistono tre tipi di metodi di interpolazione che possono essere utilizzati in un'espressione `interpolate`:
 
* `['linear']`: esegue l'interpolazione lineare tra la coppia di arresti.
* `['exponential', base]`: esegue l'interpolazione esponenziale tra le interruzioni. Il valore `base` controlla la frequenza con cui aumenta l'output. I valori più elevati rendono l'output maggiore verso l'estremità superiore dell'intervallo. Un valore `base` vicino a 1 produce un output che aumenta in modo lineare.
* `['cubic-bezier', x1, y1, x2, y2]`: esegue l'interpolazione utilizzando una [curva di Bezier cubica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definita dai punti di controllo specificati.

Di seguito è riportato un esempio di come appaiono questi diversi tipi di interpolazioni. 

| Lineare  | Esponenziale | Bezier cubica |
|---------|-------------|--------------|
| ![Grafico interpolazione lineare](media/how-to-expressions/linear-interpolation.png) | ![Grafico di interpolazione esponenziale](media/how-to-expressions/exponential-interpolation.png) | ![Grafico di interpolazione Bezier cubica](media/how-to-expressions/bezier-curve-interpolation.png) |

Lo pseudocodice seguente definisce la struttura dell'espressione `interpolate`. 

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

Nell'esempio seguente viene usata un'espressione `linear interpolate` per impostare la proprietà `color` di un livello Bubble in base alla proprietà `temperature` della funzionalità Point. Se il valore `temperature` è minore di 60, viene restituito "Blue", se è compreso tra 60 e minori di 70, viene restituito giallo, se compreso tra 70 e minore di 80, viene restituito "Orange", se 80 o versione successiva, viene restituito "Red".

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

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.
 
<center>

esempio di espressione di interpolazione ![](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Espressione Step

È possibile utilizzare un'espressione `step` per calcolare valori discreti di risultati con rientri mediante la valutazione di una [funzione a tratti-Constant](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definita da stops. 

Lo pseudocodice seguente definisce la struttura dell'espressione `step`. 

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

Le espressioni Step restituiscono il valore di output dell'arresto immediatamente prima del valore di input oppure il primo valore di input se l'input è minore del primo arresto. 

**Esempio**

Nell'esempio seguente viene usata un'espressione `step` per impostare la proprietà `color` di un livello Bubble in base alla proprietà `temperature` della funzionalità Point. Se il valore `temperature` è minore di 60, viene restituito "Blue", se è compreso tra 60 e minore di 70, viene restituito "Yellow", se è compreso tra 70 e minore di 80, viene restituito "Orange", se 80 o superiore, viene restituito "Red".

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

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.
 
<center>

esempio di espressione passaggio ![](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Espressioni specifiche del livello

Espressioni speciali che si applicano solo a livelli specifici.

### <a name="heat-map-density-expression"></a>Espressione densità mappa termica

Un'espressione di densità della mappa termica Recupera il valore della densità della mappa termica per ogni pixel in un livello mappa termica e viene definito come `['heatmap-density']`. Questo valore è un numero compreso tra `0` e `1` e viene usato in combinazione con un'espressione `interpolation` o `step` per definire la sfumatura di colore usata per colorare la mappa termica. Questa espressione può essere utilizzata solo nell' [opzione Color](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) del livello mappa termica.

> [!TIP]
> Il colore in corrispondenza dell'indice 0 in un'espressione di interpolazione o del colore predefinito del colore di un passaggio, definisce il colore dell'area in cui non sono presenti dati e può essere utilizzato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. 

**Esempio**

Questo esempio usa un'espressione di interpolazione Liner per creare una sfumatura di colore uniforme per il rendering della mappa termica. 

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

Oltre a usare una sfumatura uniforme per colorare una mappa termica, i colori possono essere specificati all'interno di un set di intervalli usando un'espressione `step`. L'uso di un'espressione `step` per colorare la mappa termica suddivide la densità in modo visivo in intervalli più simili a una mappa di contorno o di stile radar.  

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

Per ulteriori informazioni, vedere la documentazione relativa all' [aggiunta di un livello mappa termica](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Espressione avanzamento riga

Un'espressione di avanzamento riga recupera lo stato di avanzamento lungo una linea sfumata in un livello linea e viene definito come `['line-progress']`. Questo valore è un numero compreso tra 0 e 1 e viene utilizzato in combinazione con un'espressione `interpolation` o `step`. Questa espressione può essere utilizzata solo con l' [opzione strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) del livello linea. 

> [!NOTE]
> L'opzione `strokeGradient` del livello linea richiede che l'opzione `lineMetrics` dell'origine dati sia impostata su `true`.

**Esempio**

Nell'esempio seguente viene utilizzata l'espressione `['line-progress']` per applicare una sfumatura di colore al tratto di una linea.

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

[Vedere l'esempio in tempo reale](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Espressione formato campo testo

L'espressione del formato del campo di testo può essere usata con l'opzione `textField` della proprietà `textOptions` layer dei simboli per fornire la formattazione mista del testo. Questa espressione consente di specificare un set di stringhe di input e di opzioni di formattazione. È possibile specificare le opzioni seguenti per ogni stringa di input in questa espressione.

 * `'font-scale'`: specifica il fattore di scala per le dimensioni del carattere. Se specificato, questo valore sostituirà la proprietà `size` della `textOptions` per la singola stringa.
 * `'text-font'`: specifica una o più famiglie di caratteri da usare per la stringa. Se specificato, questo valore sostituirà la proprietà `font` della `textOptions` per la singola stringa.
 * `'text-color'`: specifica un colore da applicare a un testo durante il rendering. 

Lo pseudocodice seguente definisce la struttura dell'espressione del formato del campo di testo. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Esempio**

Nell'esempio seguente viene formattato il campo di testo aggiungendo un tipo di carattere in grassetto e scalando verticalmente le dimensioni del carattere della `title` proprietà della funzionalità. In questo esempio viene aggiunta anche la proprietà `subtitle` della funzionalità in una nuova riga, con una dimensione del carattere ridimensionata e un rosso colorato.

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
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Questo livello eseguirà il rendering della funzionalità punto, come illustrato nell'immagine seguente:
 
<center>

![immagine della funzionalità punto con il campo di testo formattato](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Espressione formato numero

L'espressione `number-format` può essere utilizzata solo con l'opzione `textField` di un livello di simboli. Questa espressione converte il numero fornito in una stringa formattata. Questa espressione esegue il wrapping della funzione [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) di JavaScript e supporta il set di opzioni seguente.

 * `locale`: specificare questa opzione per convertire i numeri in stringhe in modo da essere allineati alla lingua specificata. Passare un [tag di lingua BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) in questa opzione.
 * `currency`: per convertire il numero in una stringa che rappresenta una valuta. I valori possibili sono i [codici di valuta ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), ad esempio "USD" per il dollaro statunitense, "EUR" per l'euro, o "CNY" per il RMB cinese.
 * `'min-fraction-digits'`: specifica il numero minimo di posizioni decimali da includere nella versione della stringa del numero.
 * `'max-fraction-digits'`: specifica il numero massimo di posizioni decimali da includere nella versione della stringa del numero.

Lo pseudocodice seguente definisce la struttura dell'espressione del formato del campo di testo. 

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

Nell'esempio seguente viene utilizzata un'espressione `number-format` per modificare la modalità con cui viene eseguito il rendering della proprietà `revenue` della funzionalità Point nell'opzione `textField` di un livello di simboli in modo da visualizzare un valore in dollari USA.

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

Questo livello eseguirà il rendering della funzionalità punto, come illustrato nell'immagine seguente:

<center>

esempio di espressione di formato numero ![](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Espressione zoom

Un'espressione `zoom` viene utilizzata per recuperare il livello di zoom corrente della mappa in fase di rendering e viene definito come `['zoom']`. Questa espressione restituisce un numero compreso tra l'intervallo minimo e massimo del livello di zoom della mappa. L'utilizzo di questa espressione consente di modificare dinamicamente gli stili quando viene modificato il livello di zoom della mappa. L'espressione `zoom` può essere utilizzata solo con espressioni `interpolate` e `step`.

**Esempio**

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Poiché la mappa esegue lo zoom tra le aggregazioni di dati e il livello mappa termica è diverso. È possibile utilizzare un'espressione `zoom` per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copra la stessa area fisica della mappa. In questo modo il livello mappa termica risulta più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente. Il ridimensionamento del raggio in modo che raddoppi a ogni livello di zoom creerà una mappa termica che risulti coerente in tutti i livelli di zoom. Questa operazione può essere eseguita usando l'espressione `zoom` con un'espressione `base 2 exponential interpolation`, come illustrato di seguito. 

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

## <a name="variable-binding-expressions"></a>Espressioni di associazione variabili

Le espressioni di associazione variabili memorizzano i risultati di un calcolo in una variabile in modo che sia possibile farvi riferimento in un'altra posizione in un'espressione più volte senza dover ricalcolarla. Si tratta di un'ottimizzazione utile per le espressioni che coinvolgono molti calcoli

| Espressione | Tipo restituito | DESCRIZIONE |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: String,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: String,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Archivia uno o più valori come variabili per l'utilizzo da parte dell'espressione `var` nell'espressione figlio che restituisce il risultato. |
| `['var', name: string]` | qualsiasi | Fa riferimento a una variabile creata utilizzando l'espressione `let`. |

**Esempio**

In questo esempio viene utilizzata un'espressione che calcola i ricavi relativi al rapporto di temperatura, quindi utilizza un'espressione `case` per valutare operazioni booleane diverse su questo valore. L'espressione `let` viene utilizzata per archiviare i ricavi relativi al rapporto di temperatura, in modo che sia necessario calcolarla una sola volta e che l'espressione `var` faccia riferimento a questa variabile con la frequenza necessaria senza ricalcolarla.

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

Per altri esempi di codice che implementano espressioni, vedere gli articoli seguenti:

> [!div class="nextstepaction"] 
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello linea](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello poligono](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)

Altre informazioni sulle opzioni del livello che supportano le espressioni:

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
