---
title: Espressioni di stile basate sui dati in Azure Maps Web SDK Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come usare le espressioni di stile basate sui dati in Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3f15033095b02dd35c2d8d7bda60ca184df64c9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475020"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Espressioni di stile basate sui dati (Web SDK)

Le espressioni consentono di applicare la logica di business alle opzioni di stile che osservano le proprietà definite in ogni forma in un'origine dati. Le espressioni possono filtrare i dati in un'origine dati o in un layer. Le espressioni possono essere costituite da logica condizionale, ad esempio if-istruzioni. Inoltre, possono essere utilizzati per modificare i dati utilizzando: operatori stringa, operatori logici e operatori matematici.

Gli stili basati sui dati riducono la quantità di codice necessaria per implementare la logica di business per l'applicazione di stili. Se utilizzate con i livelli, le espressioni vengono valutate in fase di rendering su un thread separato. Questa funzionalità offre prestazioni migliori rispetto alla valutazione della logica di business nel thread dell'interfaccia utente.

Questo video offre una panoramica degli stili basati sui dati in Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le espressioni sono rappresentate come matrici JSON. Il primo elemento di un'espressione nella matrice è una stringa che specifica il nome dell'operatore di espressione. Ad esempio, "" o "caso". Gli elementi successivi (se presenti) sono gli argomenti dell'espressione. Ogni argomento è un valore letterale (stringa, `null`numero, booleano o ) o un'altra matrice di espressioni. Lo pseudocodice seguente definisce la struttura di base di un'espressione. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Il Web SDK di Azure Maps supporta molti tipi di espressioni. Le espressioni possono essere utilizzate da sole o in combinazione con altre espressioni.

| Tipo di espressioni | Descrizione |
|---------------------|-------------|
| [Espressione di aggregazione](#aggregate-expression) | Espressione che definisce un calcolo elaborato su un set di `clusterProperties` dati `DataSource`e che può essere utilizzata con l'opzione di un oggetto . |
| [Espressioni booleane](#boolean-expressions) | Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione dei confronti booleani. |
| [Espressioni di colore](#color-expressions) | Le espressioni di colore semplificano la creazione e la modifica dei valori di colore. |
| [Espressioni condizionali](#conditional-expressions) | Le espressioni condizionali forniscono operazioni logiche simili alle istruzioni if. |
| [Espressioni di datiData expressions](#data-expressions) | Fornisce l'accesso ai dati della proprietà in una funzionalità. |
| [Interpolazione e istruzione di espressioni](#interpolate-and-step-expressions) | Le espressioni di interpolazione e passo possono essere utilizzate per calcolare i valori lungo una funzione di curva o step interpolata. |
| [Espressioni specifiche per livelli](#layer-specific-expressions) | Espressioni speciali applicabili solo a un singolo livello. |
| [Espressioni matematiche](#math-expressions) | Fornisce operatori matematici per eseguire calcoli basati sui dati all'interno del framework dell'espressione. |
| [Espressioni dell'operatore stringa](#string-operator-expressions) | Le espressioni dell'operatore di stringa eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. |
| [Espressioni di tipo](#type-expressions) | Le espressioni di tipo forniscono strumenti per testare e convertire tipi di dati diversi, ad esempio stringhe, numeri e valori booleani. |
| [Espressioni di associazione variabiliVariable binding expressions](#variable-binding-expressions) | Le espressioni di associazione di variabili archiviano i risultati di un calcolo in una variabile e fanno riferimento in altre parti di un'espressione più volte senza dover ricalcolare il valore archiviato. |
| [Espressione di zoom](#zoom-expression) | Recupera il livello di zoom corrente della mappa in fase di rendering. |

Tutti gli esempi in questo documento usano la funzionalità seguente per illustrare diversi modi in cui è possibile utilizzare i diversi tipi di espressioni. 

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

## <a name="data-expressions"></a>Espressioni di datiData expressions

Le espressioni di dati consentono di accedere ai dati delle proprietà in una funzionalità. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['at', number, array]` | object | Recupera un elemento da una matrice. |
| `['geometry-type']` | string | Ottiene il tipo di geometria della funzionalità: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Ottiene il valore della proprietà dalle proprietà della funzionalità corrente. Restituisce null se la proprietà richiesta non è presente. |
| `['get', string, object]` | value | Ottiene il valore della proprietà dalle proprietà dell'oggetto fornito. Restituisce null se la proprietà richiesta non è presente. |
| `['has', string]` | boolean | Determina se le proprietà di una funzionalità hanno la proprietà specificata. |
| `['has', string, object]` | boolean | Determina se le proprietà dell'oggetto hanno la proprietà specificata. |
| `['id']` | value | Ottiene l'ID della funzionalità, se presente. |
| `['length', string | array]` | d'acquisto | Ottiene la lunghezza di una stringa o di una matrice. |
| `['in', boolean | string | number, array]` | boolean | Determina se un elemento esiste in una matrice |
| `['in', substring, string]` | boolean | Determina se in una stringa è presente una sottostringa |

**Esempi**

È possibile accedere alle proprietà di una `get` funzionalità direttamente in un'espressione utilizzando un'espressione. In questo esempio viene utilizzato il valore "zoneColor" della feature per specificare la proprietà color di un layer a bolle. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

L'esempio precedente funzionerà correttamente, se `zoneColor` tutte le feature punto hanno la proprietà. In caso contrario, il colore probabilmente riporterà a "nero". Per modificare il colore `case` di fallback, `has` utilizzare un'espressione in combinazione con l'espressione per verificare se la proprietà esiste. Se la proprietà non esiste, restituire un colore di fallback.

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

I livelli Bolla e Simbolo eseguiranno il rendering delle coordinate di tutte le forme in un'origine dati, per impostazione predefinita. Questo comportamento può evidenziare i vertici di un poligono o di una linea. L'opzione `filter` del layer può essere utilizzata per limitare il tipo `['geometry-type']` di geometria delle feature di cui esegue il rendering, utilizzando un'espressione all'interno di un'espressione booleana. L'esempio seguente limita un `Point` layer a bolle in modo che venga eseguito il rendering solo delle feature.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

L'esempio seguente `Point` `MultiPoint` consente il rendering di entrambe le funzionalità. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Analogamente, il contorno dei poligoni eseguirà il rendering in livelli linea. Per disattivare questo comportamento in un layer `LineString` linea, aggiungere un filtro che consenta e `MultiLineString` funzioni solo.  

## <a name="math-expressions"></a>Espressioni matematiche

Le espressioni matematiche forniscono operatori matematici per eseguire calcoli basati sui dati all'interno del framework di espressioni.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['+', number, number, …]` | d'acquisto | Calcola la somma dei numeri specificati. |
| `['-', number]` | d'acquisto | Sottrae 0 in base al numero specificato. |
| `['-', number, number]` | d'acquisto | Sottrae i primi numeri per il secondo numero. |
| `['*', number, number, …]` | d'acquisto | Moltiplica i numeri specificati insieme. |
| `['/', number, number]` | d'acquisto | Divide il primo numero per il secondo numero. |
| `['%', number, number]` | d'acquisto | Calcola il resto quando si divide il primo numero per il secondo numero. |
| `['^', number, number]` | d'acquisto | Calcola il valore del primo valore elevato alla potenza del secondo numero. |
| `['abs', number]` | d'acquisto | Calcola il valore assoluto del numero specificato. |
| `['acos', number]` | d'acquisto | Calcola l'arcocoseno del numero specificato. |
| `['asin', number]` | d'acquisto | Calcola l'arcoseno del numero specificato. |
| `['atan', number]` | d'acquisto | Calcola l'arcotangente del numero specificato. |
| `['ceil', number]` | d'acquisto | Arrotonda il numero per eperio al numero intero successivo. |
| `['cos', number]` | d'acquisto | Calcola il valore cos del numero specificato. |
| `['e']` | d'acquisto | Restituisce la `e`costante matematica . |
| `['floor', number]` | d'acquisto | Arrotonda il numero per edue all'intero intero precedente. |
| `['ln', number]` | d'acquisto | Calcola il logaritmo naturale del numero specificato. |
| `['ln2']` | d'acquisto | Restituisce la `ln(2)`costante matematica . |
| `['log10', number]` | d'acquisto | Calcola il logaritmo in base dieci del numero specificato. |
| `['log2', number]` | d'acquisto | Calcola il logaritmo in base due del numero specificato. |
| `['max', number, number, …]` | d'acquisto | Calcola il numero massimo nel set di numeri specificato. |
| `['min', number, number, …]` | d'acquisto | Calcola il numero minimo nel set di numeri specificato. |
| `['pi']` | d'acquisto | Restituisce la `PI`costante matematica . |
| `['round', number]` | d'acquisto | Arrotonda il numero all'intero più vicino. I valori a metà strada vengono arrotondati a partire da zero. Ad esempio, `['round', -1.5]` restituisce -2. |
| `['sin', number]` | d'acquisto | Calcola il sine del numero specificato. |
| `['sqrt', number]` | d'acquisto | Calcola la radice quadrata del numero specificato. |
| `['tan', number]` | d'acquisto | Calcola la tangente del numero specificato. |

## <a name="aggregate-expression"></a>Espressione di aggregazione

Un'espressione di aggregazione definisce un calcolo elaborato su un `clusterProperties` set `DataSource`di dati e che può essere utilizzato con l'opzione di un oggetto . L'output di queste espressioni deve essere un numero o un valore booleano. 

Un'espressione di aggregazione accetta tre valori: un valore dell'operatore e un valore iniziale e un'espressione per recuperare una proprietà da ogni funzionalità in un dato a cui applicare l'operazione di aggregazione. Questa espressione ha il seguente formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: funzione di espressione che viene quindi applicata `mapExpression` a su tutti i valori calcolati da per ogni punto del cluster. Operatori supportati: 
    - Per i `+` `*`numeri: , , `max`,`min`
    - Per i `all`valori booleani: ,`any`
- initialValue: valore iniziale in cui viene aggregato il primo valore calcolato.
- mapExpression: un'espressione applicata a ogni punto del set di dati.

**Esempi**

Se tutte le feature in `revenue` un set di dati hanno una proprietà, ovvero un numero. È quindi possibile calcolare le entrate totali di tutti i punti di un cluster, creati dal set di dati. Questo calcolo viene eseguito utilizzando l'espressione di aggregazione seguente:This calculation is done using the following aggregate expression:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Espressioni booleane

Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione dei confronti booleani.

Quando si confrontano i valori, il confronto è rigorosamente tipizzato. I valori di tipi diversi sono sempre considerati disuguali. I casi in cui i tipi sono noti per essere diversi in fase di analisi sono considerati non validi e genereranno un errore di analisi. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negazione logica. Restituisce `true` se `false`l'input è , e `false` se l'input è `true`. |
| `['!= ', value, value]` | boolean | Restituisce `true` se i valori `false` di input non sono uguali, in caso contrario. |
| `['<', value, value]` | boolean | Restituisce `true` se il primo input è `false` strettamente minore del secondo, in caso contrario. Gli argomenti devono essere sia stringhe che entrambi numeri. |
| `['<=', value, value]` | boolean | Restituisce `true` se il primo input è minore `false` o uguale al secondo, in caso contrario. Gli argomenti devono essere sia stringhe che entrambi numeri. |
| `['==', value, value]` | boolean | Restituisce `true` se i valori `false` di input sono uguali, in caso contrario. Gli argomenti devono essere sia stringhe che entrambi numeri. |
| `['>', value, value]` | boolean | Restituisce `true` se il primo input è `false` strettamente maggiore del secondo, in caso contrario. Gli argomenti devono essere sia stringhe che entrambi numeri. |
| `['>=' value, value]` | boolean | Restituisce `true` se il primo input è maggiore `false` o uguale al secondo, in caso contrario. Gli argomenti devono essere sia stringhe che entrambi numeri. |
| `['all', boolean, boolean, …]` | boolean | Restituisce `true` se tutti `true`gli `false` input sono , in caso contrario. |
| `['any', boolean, boolean, …]` | boolean | Restituisce `true` se uno degli `true` `false` input è , in caso contrario. |

## <a name="conditional-expressions"></a>Espressioni condizionali

Le espressioni condizionali forniscono operazioni logiche simili alle istruzioni if.

Le espressioni seguenti eseguono operazioni di logica condizionale sui dati di input. Ad esempio, `case` l'espressione fornisce la logica "if/then/else" mentre l'espressione `match` è simile a una "switch-statement". 

### <a name="case-expression"></a>Espressione case

Un'espressione `case` è un tipo di espressione condizionale che fornisce la logica "if/then/else". Questo tipo di espressione scorre un elenco di condizioni booleane. Restituisce il valore di output della prima condizione booleana da restituire true.

Lo pseudocodice seguente definisce `case` la struttura dell'espressione. 

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

Nell'esempio seguente viene eseguito un'operazione di `true`modifica di diverse condizioni booleane finché non ne viene trovata una che restituisce , quindi restituisce il valore associato. Se nessuna condizione `true`booleana restituisce , verrà restituito un valore di fallback. 

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

Un'espressione `match` è un tipo di espressione condizionale che fornisce un'istruzione switch come la logica. L'input può essere `['get', 'entityType']` qualsiasi espressione, ad esempio che restituisce una stringa o un numero. Ogni etichetta deve essere un singolo valore letterale o una matrice di valori letterali, i cui valori devono essere tutte le stringhe o tutti i numeri. L'input corrisponde se uno dei valori nella matrice corrisponde. Ogni etichetta deve essere univoca. Se il tipo di input non corrisponde al tipo delle etichette, il risultato sarà il valore di fallback.

Lo pseudocodice seguente definisce `match` la struttura dell'espressione. 

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

**Esempi**

L'esempio seguente `entityType` esamina la proprietà di una feature Point in un layer a bolle per cercare una corrispondenza. Se trova una corrispondenza, viene restituito il valore specificato o restituisce il valore di fallback.

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

Nell'esempio seguente viene utilizzata una matrice per elencare un set di etichette che devono restituire tutti lo stesso valore. Questo approccio è molto più efficiente rispetto all'elenco di ogni etichetta singolarmente. In questo caso, `entityType` se la proprietà è "ristorante" o "grocery_store", verrà restituito il colore "rosso".

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

Nell'esempio seguente viene utilizzata un'espressione di corrispondenza per eseguire un filtro di tipo "in matrice" o "array contains". In questo caso, l'espressione filtra i dati con un valore ID presente in un elenco di ID consentiti. Quando si utilizzano espressioni con filtri, il risultato deve essere un valore booleano.

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

### <a name="coalesce-expression"></a>Espressione Coalesce

Un'espressione `coalesce` esegue un set di espressioni fino a quando non viene ottenuto il primo valore non null e restituisce tale valore. 

Lo pseudocodice seguente definisce `coalesce` la struttura dell'espressione. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Esempio**

Nell'esempio seguente `coalesce` viene utilizzata `textField` un'espressione per impostare l'opzione di un livello simbolo. Se `title` la proprietà non è presente `null`nella funzionalità o è `subtitle` impostata su , `null`l'espressione tenterà di cercare la proprietà , se mancante o , verrà quindi riportata su una stringa vuota. 

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

L'esempio seguente `coalesce` usa un'espressione per recuperare la prima icona di immagine disponibile disponibile nello sprite della mappa da un elenco di nomi di immagini specificati.

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

Le espressioni di tipo forniscono strumenti per testare e convertire tipi di dati diversi, ad esempio stringhe, numeri e valori booleani.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | oggetto \| array | Restituisce una matrice letterale o un valore di oggetto. Utilizzare questa espressione per impedire che una matrice o un oggetto venga valutato come espressione. Ciò è necessario quando una matrice o un oggetto deve essere restituito da un'espressione. |
| `['image', string]` | string | Verifica se un ID immagine specificato viene caricato nello sprite dell'immagine delle mappe. In caso affermativo, viene restituito l'ID, altrimenti viene restituito null. |
| `['to-boolean', value]` | boolean | Converte il valore di input in un valore booleano. Il `false` risultato è quando l'input è una stringa vuota, `0`, `false`, `null`, o `NaN`; in `true`caso contrario, la sua . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Converte il valore di input in un colore. Se vengono forniti più valori, ognuno dei due viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se nessuno degli input può essere convertito, l'espressione è un errore. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | d'acquisto | Converte il valore di input in un numero, se possibile. Se l'input è `null` o `false`, il risultato è 0. Se l'input è `true`, il risultato è 1. Se l'input è una stringa, viene convertito in un numero utilizzando la funzione di stringa [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) della specifica del linguaggio ECMAScript. Se vengono forniti più valori, ognuno dei due viene valutato in ordine fino a quando non viene ottenuta la prima conversione riuscita. Se nessuno degli input può essere convertito, l'espressione è un errore. |
| `['to-string', value]` | string | Converte il valore di input in una stringa. Se l'input è `null` `""`, il risultato è . Se l'input è un `"true"` valore `"false"`booleano, il risultato è o . Se l'input è un numero, viene convertito in una stringa utilizzando la funzione [Numero ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) della specifica del linguaggio ECMAScript. Se l'input è un colore, viene convertito `"rgba(r,g,b,a)"`in stringa di colore RGBA CSS . In caso contrario, l'input viene convertito in una stringa utilizzando la funzione [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) della specifica del linguaggio ECMAScript. |
| `['typeof', value]` | string | Restituisce una stringa che descrive il tipo del valore specificato. |

> [!TIP]
> Se nella console `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` del browser viene visualizzato un messaggio di errore simile a, significa che nel codice è presente un'espressione che contiene una matrice che non dispone di una stringa per il primo valore. Se si desidera che l'espressione restituisca `literal` una matrice, racchiudere la matrice con l'espressione. L'esempio seguente imposta `offset` l'opzione dell'icona di un layer di simboli, `match` che deve essere una matrice contenente `entityType` due numeri, utilizzando un'espressione per scegliere tra due valori di offset in base al valore della proprietà della feature punto.
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

## <a name="color-expressions"></a>Espressioni di colore

Le espressioni di colore semplificano la creazione e la modifica dei valori di colore.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crea un valore di colore dai componenti *rosso* `0` , `255` *verde*e *blu* che devono essere compresi tra e , e un componente alfa di `1`. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['rgba', number, number, number, number]` | color | Crea un valore di colore da *rosso*, `255` *verde*, componenti *blu* che devono essere compresi tra `0` e , e un componente alfa all'interno di un intervallo di `0` e `1`. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `['to-rgba']` | \[numero, numero, numero, numero\] | Restituisce una matrice a quattro elementi contenente i componenti *rosso,* *verde*, *blu*e *alfa* del colore di input, in questo ordine. |

**Esempio**

Nell'esempio seguente viene creato un *red* valore `255`di colore RGB con valore rosso e *verde* e `temperature` *blu* calcolati moltiplicando `2.5` il valore della proprietà . Come la temperatura cambia, il colore cambierà in diverse tonalità di *rosso*.

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

## <a name="string-operator-expressions"></a>Espressioni dell'operatore stringa

Le espressioni dell'operatore di stringa eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena più stringhe. Ogni valore deve essere una stringa. Utilizzare `to-string` l'espressione di tipo per convertire altri tipi di valore in stringa, se necessario. |
| `['downcase', string]` | string | Converte la stringa specificata in minuscolo. |
| `['upcase', string]` | string | Converte la stringa specificata in maiuscolo. |

**Esempio**

Nell'esempio riportato `temperature` di seguito viene convertita la proprietà della feature punto in una stringa, quindi viene concatenata la concatenazione di "F" alla fine di essa.

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

L'espressione sopra esegue il rendering di un perno sulla mappa con il testo "64" sovrapposto sopra di esso, come mostrato nell'immagine qui sotto.

<center>

![Esempio di](media/how-to-expressions/string-operator-expression.png) espressione dell'operatore stringa</center>

## <a name="interpolate-and-step-expressions"></a>Interpolazione e istruzione di espressioni

Le espressioni di interpolazione e passo possono essere utilizzate per calcolare i valori lungo una funzione di curva o step interpolata. Queste espressioni accettano un'espressione che restituisce un `['get',  'temperature']`valore numerico come input, ad esempio . Il valore di input viene valutato in base a coppie di valori di input e output, per determinare il valore che meglio si adatta alla funzione di curva o step interpolata. I valori di output sono denominati "stop". I valori di input per ogni arresto devono essere un numero ed essere in ordine crescente. I valori di output devono essere un numero e una matrice di numeri o un colore.

### <a name="interpolate-expression"></a>Interpolare l'espressione

Un'espressione `interpolate` può essere utilizzata per calcolare un set continuo e uniforme di valori interpolando tra valori di interruzione. Un'espressione `interpolate` che restituisce valori di colore produce una sfumatura di colore da cui vengono selezionati i valori dei risultati.

Esistono tre tipi di metodi di interpolazione `interpolate` che possono essere utilizzati in un'espressione:
 
* `['linear']`- Esegue l'interpolazione linearmente tra la coppia di fermate.
* `['exponential', base]`- Esegue l'interpolazione esponenzialmente tra le fermate. Il `base` valore controlla la velocità di aumento dell'output. Valori più alti rendono l'aumento dell'uscita più verso l'estremità alta dell'intervallo. Un `base` valore vicino a 1 produce un output che aumenta in modo più lineare.
* `['cubic-bezier', x1, y1, x2, y2]`- Esegue l'interpolazione utilizzando una [curva di Bézier cubica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definita dai punti di controllo indicati.

Ecco un esempio di come appaiono questi diversi tipi di interpolazioni. 

| Lineari  | Esponenziali | Curva di Bézier cubica |
|---------|-------------|--------------|
| ![Grafico di interpolazione lineare](media/how-to-expressions/linear-interpolation.png) | ![Grafico dell'interpolazione esponenziale](media/how-to-expressions/exponential-interpolation.png) | ![Grafico di interpolazione di Bézier cubico](media/how-to-expressions/bezier-curve-interpolation.png) |

Lo pseudocodice seguente definisce `interpolate` la struttura dell'espressione. 

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

Nell'esempio seguente `linear interpolate` viene utilizzata `color` un'espressione per impostare la proprietà di un layer a bolle in base alla `temperature` proprietà della feature punto. Se `temperature` il valore è minore di 60, verrà restituito "blu". Se è compreso tra 60 e meno di 70, verrà restituito il giallo. Se è compreso tra 70 e meno di 80, verrà restituito "arancione". Se è 80 o superiore, verrà restituito "rosso".

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

L'immagine seguente mostra come vengono scelti i colori per l'espressione precedente.
 
<center>

![Esempio di interpolazione di espressione](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>Espressione passo

Un'espressione `step` può essere utilizzata per calcolare valori di risultato discreti e a gradini valutando una [funzione bit per costante](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definita da stop. 

Lo pseudocodice seguente definisce `step` la struttura dell'espressione. 

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

Le espressioni di passaggio restituiscono il valore di output dell'arresto appena prima del valore di input o il primo valore di input se l'input è minore del primo arresto. 

**Esempio**

Nell'esempio seguente `step` viene utilizzata `color` un'espressione per impostare la proprietà di un layer a bolle in base alla `temperature` proprietà della feature punto. Se `temperature` il valore è minore di 60, verrà restituito "blu". Se è compreso tra 60 e meno di 70, verrà restituito "giallo". Se è compreso tra 70 e meno di 80, verrà restituito "arancione". Se è 80 o superiore, verrà restituito "rosso".

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

L'immagine seguente mostra come vengono scelti i colori per l'espressione precedente.
 
<center>

![Esempio di espressione di passaggioStep expression example](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Espressioni specifiche del layer

Espressioni speciali che si applicano solo a livelli specifici.

### <a name="heat-map-density-expression"></a>Espressione densità mappa termica

Un'espressione di densità della mappa termica recupera il valore di densità `['heatmap-density']`della mappa termica per ogni pixel in un livello mappa termica ed è definita come . Questo valore è `0` un `1`numero compreso tra e . Viene utilizzato in combinazione `interpolation` con `step` un'espressione o per definire il gradiente di colore utilizzato per colorare la mappa termica. Questa espressione può essere utilizzata solo [nell'opzione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) di colore del layer della mappa termica.

> [!TIP]
> Il colore in corrispondenza dell'indice 0, in un'espressione di interpolazione o il colore predefinito di un colore di passo, definisce il colore dell'area in cui non sono presenti dati. Il colore in corrispondenza dell'indice 0 può essere utilizzato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente.

**Esempio**

Questo esempio usa un'espressione di interpolazione di linea per creare una sfumatura di colore uniforme per il rendering della mappa termica. 

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

Oltre a utilizzare una sfumatura uniforme per colorare una mappa termica, i `step` colori possono essere specificati all'interno di un set di intervalli utilizzando un'espressione. L'utilizzo di un'espressione `step` per colorare la mappa termica suddivide visivamente la densità in intervalli simili a una mappa di stile di contorno o radar.  

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

Per ulteriori informazioni, vedere la documentazione [Aggiungere un layer mappa termica.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Espressione di avanzamento linea

Un'espressione di avanzamento linea recupera lo stato di avanzamento lungo una linea di sfumatura in un livello linea ed è definita come `['line-progress']`. Questo valore è un numero compreso tra 0 e 1. Viene utilizzato in combinazione `interpolation` con `step` un'espressione o . Questa espressione può essere utilizzata solo con [l'opzione strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) del livello linea. 

> [!NOTE]
> L'opzione `strokeGradient` del livello `lineMetrics` linea richiede l'opzione dell'origine dati da impostare `true`su .

**Esempio**

Questo esempio `['line-progress']` usa l'espressione per applicare una sfumatura di colore al tratto di una linea.

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

[Vedi l'esempio dal vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Espressione formato campo di testo

L'espressione di formato del `textField` campo di testo `textOptions` può essere utilizzata con l'opzione della proprietà dei livelli simbolo per fornire una formattazione mista del testo. Questa espressione consente di specificare un set di stringhe di input e opzioni di formattazione. Le opzioni seguenti possono essere specificate per ogni stringa di input in questa espressione.

 * `'font-scale'`- Specifica il fattore di scala per la dimensione del carattere. Se specificato, questo valore `size` eseguirà `textOptions` l'override della proprietà di per la singola stringa.
 * `'text-font'`- Specifica una o più famiglie di caratteri che devono essere utilizzate per questa stringa. Se specificato, questo valore `font` eseguirà `textOptions` l'override della proprietà di per la singola stringa.
 * `'text-color'`- Specifica un colore da applicare a un testo durante il rendering. 

Lo pseudocodice seguente definisce la struttura dell'espressione di formato del campo di testo. 

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

Nell'esempio seguente viene formatta il campo di testo `title` aggiungendo un carattere in grassetto e aumentando la dimensione del carattere della proprietà della funzionalità. In questo esempio `subtitle` viene aggiunta anche la proprietà della feature in una nuova riga, con una dimensione del carattere ridotta e di colore rosso.

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

Questo livello eseguirà il rendering della feature punto come mostrato nell'immagine seguente:
 
<center>

![Immagine della funzione Punto con](media/how-to-expressions/text-field-format-expression.png) campo di testo formattato</center>

### <a name="number-format-expression"></a>Espressione formato numero

L'espressione `number-format` può essere `textField` utilizzata solo con l'opzione di un layer di simboli. Questa espressione converte il numero fornito in una stringa formattata. Questa espressione esegue il wrapping della funzione [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) di JavaScript e supporta il set di opzioni seguente.

 * `locale`- Specificare questa opzione per convertire i numeri in stringhe in modo da allinearsi con la lingua specificata. Passare un [tag di lingua BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) in questa opzione.
 * `currency`- Per convertire il numero in una stringa che rappresenta una valuta. I valori possibili sono i [codici di valuta ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), ad esempio "USD" per il dollaro statunitense, "EUR" per l'euro o "CNY" per il RMB cinese.
 * `'min-fraction-digits'`- Specifica il numero minimo di posizioni decimali da includere nella versione stringa del numero.
 * `'max-fraction-digits'`- Specifica il numero massimo di posizioni decimali da includere nella versione stringa del numero.

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

Nell'esempio seguente `number-format` viene utilizzata `revenue` un'espressione per modificare il `textField` rendering della proprietà della feature punto nell'opzione di un layer di simboli in modo che appaia un valore in dollari USA.

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

Questo livello eseguirà il rendering della feature punto come mostrato nell'immagine seguente:

<center>

![Esempio di](media/how-to-expressions/number-format-expression.png) espressione in formato numero</center>

### <a name="image-expression"></a>Espressione immagine

Un'espressione immagine può `image` essere `textField` utilizzata con le opzioni `fillPattern` e di un livello simbolo e l'opzione del livello poligono. Questa espressione verifica che l'immagine richiesta esista nello `null`stile e restituirà il nome dell'immagine risolta oppure , a seconda che l'immagine sia attualmente nello stile. Questo processo di convalida è sincrono e richiede che l'immagine sia stata aggiunta allo stile prima di richiederlo nell'argomento image.

**Esempio**

Nell'esempio seguente `image` viene utilizzata un'espressione per aggiungere un'icona in linea con il testo in un layer di simboli. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Questo livello eseguirà il rendering del campo di testo nel livello dei simboli, come mostrato nell'immagine seguente:

<center>

![Esempio di](media/how-to-expressions/image-expression.png) espressione immagine</center>

## <a name="zoom-expression"></a>Espressione di zoom

Un'espressione `zoom` viene utilizzata per recuperare il livello di zoom `['zoom']`corrente della mappa in fase di rendering ed è definita come . Questa espressione restituisce un numero compreso tra l'intervallo del livello di zoom minimo e massimo della mappa. I controlli mappa interattivi di Azure Maps per Web e Android supportano 25 livelli di zoom, numerati da 0 a 24.The Azure Maps interactive map controls for web and Android support 25 zoom levels, numbered 0 through 24. L'utilizzo dell'espressione `zoom` consente di modificare dinamicamente gli stili quando viene modificato il livello di zoom della mappa. L'espressione `zoom` può essere `interpolate` `step` utilizzata solo con e espressioni.

**Esempio**

Per impostazione predefinita, i raggi dei punti dati sottoposti a rendering nel layer della mappa termica hanno un raggio pixel fisso per tutti i livelli di zoom. Quando la mappa viene ingrandita, i dati vengono aggregati insieme e il layer della mappa termica ha un aspetto diverso. Un'espressione `zoom` può essere utilizzata per scalare il raggio per ogni livello di zoom in modo che ogni punto dati copra la stessa area fisica della mappa. Renderà il layer della mappa termica più statico e coerente. Ogni livello di zoom della mappa ha il doppio dei pixel verticalmente e orizzontalmente rispetto al livello di zoom precedente. Scalare il raggio, in modo che raddoppi con ogni livello di zoom, creerà una mappa termica che sembra coerente su tutti i livelli di zoom. Può essere eseguita `zoom` utilizzando l'espressione con un'espressione, `base 2 exponential interpolation` come illustrato di seguito. 

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

[Vedi l'esempio dal vivo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Espressioni di associazione variabiliVariable binding expressions

Le espressioni di associazione di variabili archiviano i risultati di un calcolo in una variabile. In questo modo, è possibile fare riferimento ai risultati del calcolo in un altro punto di un'espressione più volte. Si tratta di un'utile ottimizzazione per le espressioni che implicano molti calcoli.

| Expression | Tipo restituito | Descrizione |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'Let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valore1: qualsiasi,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: stringa,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valore2: qualsiasi,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Archivia uno o più valori `var` come variabili per l'utilizzo da parte dell'espressione nell'espressione figlio che restituisce il risultato. |
| `['var', name: string]` | any | Fa riferimento a una `let` variabile creata utilizzando l'espressione. |

**Esempio**

In questo esempio viene utilizzata un'espressione che calcola `case` il rapporto ricavi rispetto al rapporto di temperatura e quindi utilizza un'espressione per valutare diverse operazioni booleane su questo valore. L'espressione `let` viene utilizzata per archiviare i ricavi in relazione al rapporto di temperatura, in modo che debba essere calcolata una sola volta. L'espressione `var` fa riferimento a questa variabile tutte le volte che è necessario senza doverla ricalcolare.

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

Per altri esempi di codice che implementano espressioni, vedere gli articoli seguenti:See the following articles for more code samples that implement expressions:

> [!div class="nextstepaction"] 
> [Aggiungere un livello simbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello bolla](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)

Ulteriori informazioni sulle opzioni layer che supportano le espressioni:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions (Opzioni LineLayer)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOpzioni](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
