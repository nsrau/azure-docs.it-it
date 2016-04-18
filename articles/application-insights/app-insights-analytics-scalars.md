<properties 
	pageTitle="Espressioni scalari in Analytics in Application Insights" 
	description="Numeri, stringhe, espressioni dinamiche e tipi in Analytics, lo strumento di ricerca avanzato per Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>

 
# Espressioni scalari in Analytics


[Analytics](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) <br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) <br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [itemCount](#itemCount) | [split](#split) | [sqrt](#sqrt) <br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



Il termine "scalare" indica valori come numeri o stringhe che possono occupare una singola cella in una tabella. Le espressioni scalari sono costituite da operatori e funzioni scalari e restituiscono valori scalari. `sqrt(score)/100 > target+2` è un'espressione scalare.

Il termine "scalare" si riferisce anche a matrici e oggetti compositi che possono essere memorizzati in una singola cella di database.

Le espressioni scalari differiscono dalle [query](app-insights-analytics-queries.md) i cui risultati sono tabelle.

## Scalari

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

I tipi supportati sono:

| Tipo | Nome/i aggiuntivo/i | Tipo .NET equivalente |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Cast

È possibile eseguire il cast da un tipo a un altro. In generale, se la conversione è possibile, avrà esito positivo:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### Confronti scalari

||
---|---
`<` |Minore
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non uguale a
`!=`|Non uguale a 
`in`| L'operando di destra è una matrice (dinamica) e l'operando di sinistra è uguale a uno dei relativi elementi.
`!in`| L'operando di destra è una matrice (dinamica) e l'operando di sinistra non è uguale a uno dei relativi elementi.




### gettype

**Restituisce**

Una stringa che rappresenta il tipo di archiviazione sottostante del relativo singolo argomento. Questo è particolarmente utile quando sono presenti valori di tipo `dynamic`: in questo caso `gettype()` indicherà come viene codificato un valore.

**esempi**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**Sintassi**

    hash(source [, mod])

**Argomenti**

* *source*: scalare di origine su cui viene calcolato l'hash.
* *mod*: valore modulo da applicare al risultato hash.

**Restituisce**

Il valore xxhash (long)value dello scalare specificato, modulo del valore mod specificato (se specificato).

**esempi**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

La funzione `iff()` calcola il primo argomento (predicato) e restituisce il valore del secondo o del terzo argomento a seconda che sia stato specificato il predicato `true` o `false`. Il secondo e terzo argomento devono essere dello stesso tipo.

**Sintassi**

    iff(predicate, ifTrue, ifFalse)


**Argomenti**

* *predicate:* espressione che restituisce un valore `boolean`.
* *ifTrue:* espressione calcolata il cui valore viene restituito dalla funzione se *predicate* restituisce `true`.
* *ifFalse:* espressione calcolata il cui valore viene restituito dalla funzione se *predicate* restituisce `false`.

**Restituisce**

Questa funzione restituisce il valore di *ifTrue* se *predicate* restituisce `true`. In caso contrario, restituisce il valore di *ifFalse*.

**Esempio**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Accetta un solo argomento e indica se è null.

**Sintassi**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Restituisce**

True o false a seconda se il valore è null o not null.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false

**Esempio**

    T | where isnotnull(PossiblyNull) | count

Si noti che esistono altri modi per ottenere questo risultato:

    T | summarize count(PossiblyNull)




## Boolean 

### Valori letterali booleani

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Operatori booleani

	and 
    or 

    

## Numeri

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Valori letterali numerici

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Operatori aritmetici

|| |
|---|-------------|
| + | Aggiungi |
| - | Sottrai | | * | Moltiplica | | / | Dividi | | % | Modulo | || |`<` |Minore |`<=`|Minore o uguale a |`>` |Maggiore |`>=`|Maggiore o uguale a |`<>`|Non uguale a |`!=`|Non uguale a




### bin

Arrotonda per difetto i valori fino a un numero intero multiplo della dimensione del contenitore specificata. Usato spesso nella query [`summarize by`](app-insights-analytics-queries.md#summarize-operator). Se si dispone di un set di valori sparsi, i valori verranno raggruppati in un set più piccolo di valori specifici.

Alias `floor`.

**Sintassi**

     bin(value, roundTo)

**Argomenti**

* *value:* numero, data o intervallo di tempo. 
* *roundTo:* dimensioni bin. Numero, data o intervallo di tempo per cui viene diviso *value*. 

**Restituisce**

Il multiplo più vicino di *roundTo* inferiore a *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**esempi**

Espressione | Risultato
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


L'espressione seguente calcola un istogramma di durate, con dimensione del bucket pari a 1 secondo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

Alias di [`bin()`](#bin).


### rand

Generatore di numeri casuali.

* `rand()`: numero reale compreso tra 0,0 e 1,0
* `rand(n)`: numero intero compreso tra 0 e n-1




### sqrt

Funzione della radice quadrata.

**Sintassi**

    sqrt(x)

**Argomenti**

* *x:* numero reale >= 0.

**Restituisce**

* Un numero positivo tale che `sqrt(x) * sqrt(x) == x`
* `null` se l'argomento è negativo o non può essere convertito in un valore `real`. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Data e ora


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### Valori letterali di data e ora

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Le ore sono sempre in formato UTC. L'omissione della data indica un'ora di oggi.
`now()`|Ora corrente.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 giorni
`1.5h`|1,5 ore 
`30m`|30 minuti
`10s`|10 secondi
`0.1s`|0,1 secondo
`100ms`| 100 millisecondi
`10microsecond`|
`1tick`|100 ns
`time("15 seconds")`|
`time("2")`| 2 giorni
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Espressioni di data e ora

Espressione |Risultato
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Minore
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non uguale a
`!=`|Non uguale a 




### ago

Sottrae l'intervallo di tempo specificato dall'ora UTC corrente. Analogamente a `now()`, questa funzione può essere usata più volte in un'istruzione e l'ora UTC a cui fa riferimento sarà la stessa per tutte le istanze.

**Sintassi**

    ago(a_timespan)

**Argomenti**

* *a\_timespan*: intervallo da sottrarre dall'ora UTC corrente (`now()`).

**Restituisce**

    now() - a_timespan

**Esempio**

Tutte le righe con un timestamp nell'ultima ora:

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Numero intero di giorni a partire dalla domenica precedente, come `timespan`.

**Sintassi**

    dayofweek(a_date)

**Argomenti**

* `a_date`: valore `datetime`.

**Restituisce**

`timespan` a partire dalla mezzanotte all'inizio della domenica precedente, arrotondato per difetto a un numero intero di giorni.

**esempi**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

Visualizzare il numero del mese (1-12) di un valore datetime.

**Esempio**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Visualizzare l'anno di un valore datetime.

**Esempio**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

Ora UTC corrente, con offset facoltativo di un intervallo di tempo specificato. Questa funzione può essere usata più volte in un'istruzione e l'ora a cui fa riferimento sarà la stessa per tutte le istanze.

**Sintassi**

    now([offset])

**Argomenti**

* *offset:* `timespan` aggiunto all'ora UTC corrente. Valore predefinito: 0.

**Restituisce**

L'ora UTC corrente come `datetime`.

    now() + offset

**Esempio**

Determina l'intervallo a partire dall'evento identificato da predicate:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

Inizio del mese contenente la data.

### startofyear

    startofyear(date)

Inizio dell'anno contenente la data.


### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Valori letterali di stringa

Le regole sono le stesse di JavaScript.

Le stringhe possono essere racchiuse tra virgolette singole o doppie.

La barra rovesciata (``) viene usata per i caratteri di escape, ad esempio `\t` (tabulazione), `\n` (nuova riga) e le istanze del carattere delle virgolette.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Valori letterali di stringhe offuscate

I valori letterali di stringhe offuscate sono stringhe che Analytics nasconde durante l'output della stringa (ad esempio, durante la traccia). Il processo di offuscamento sostituisce tutti i caratteri offuscati da un carattere (`*`) di inizio.

Per creare un valore letterale di stringa offuscata, anteporre `h` o "H". Ad esempio:

```
h'hello'
h@'world' 
h"hello"
```

### Confronti di stringhe

Operatore|Descrizione|Distinzione maiuscole/minuscole|Esempio true
---|---|---|---
`==`|Uguale a |Sì| `"aBc" == "aBc"`
`<>`|Non uguale a|Sì| `"abc" <> "ABC"`
`=~`|Uguale a |No| `"abc" =~ "ABC"`
`!~`|Non uguale a |No| `"aBc" !~ "xyz"`
`has`|RHS (Right-Hand-Side) è un termine intero in LHS (Left-Hand-Side)|No| `"North America" has "america"`
`!has`|RHS non è un termine completo in LHS|No|`"North America" !has "amer"` 
`contains` | RHS si verifica come sottosequenza di LHS|No| `"FabriKam" contains "BRik"`
`!contains`| RHS non si verifica in LHS|No| `"Fabrikam" !contains "xyz"`
`containscs` | RHS si verifica come sottosequenza di LHS|Sì| `"FabriKam" contains "Kam"`
`!containscs`| RHS non si verifica in LHS|Sì| `"Fabrikam" !contains "Kam"`
`startswith`|RHS è una sottosequenza iniziale di LHS.|No|`"Fabrikam" startswith "fab"`
`matches regex`|LHS contiene una corrispondenza per RHS|Sì| `"Fabrikam" matches regex "b.*k"`


Usare `has` o `in` se si sta testando la presenza di un termine lessicale intero, ovvero un simbolo o una parola alfanumerica delimitata da caratteri non alfanumerici o da inizio o fine del campo. `has` viene eseguito più rapidamente di `contains` o `startswith`. La prima delle query seguenti viene eseguita più rapidamente:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta le occorrenze di una sottostringa in una stringa. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**Sintassi**

    countof(text, search [, kind])

**Argomenti**

* *text:* stringa.
* *search:* stringa di testo normale o [espressione regolare](app-insights-analytics-reference.md#regular-expressions) da ricercare all'interno di *text*.
* *kind:* `"normal"|"regex"` Valore predefinito `normal`. 

**Restituisce**

Il numero di volte in cui la stringa di ricerca può essere trovata nel contenitore. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**esempi**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (non 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Recupera una corrispondenza di un'[espressione regolare](app-insights-analytics-reference.md#regular-expressions) da una stringa di testo. Facoltativamente, converte la sottostringa estratta nel tipo indicato.

**Sintassi**

    extract(regex, captureGroup, text [, typeLiteral])

**Argomenti**

* *regex:* [espressione regolare](app-insights-analytics-reference.md#regular-expressions).
* *captureGroup:* costante `int` positiva che indica il gruppo di acquisizione da estrarre. 0 indica la corrispondenza completa, 1 per il valore corrispondente alle prime '(' parentesi')' nell'espressione regolare, 2 o successivi per le parentesi successive.
* *text:* valore `string` da ricercare.
* *typeLiteral:* valore letterale di tipo facoltativo (ad esempio, `typeof(long)`). Se specificato, la sottostringa estratta viene convertita nel tipo. 

**Restituisce**

Se *regex* trova una corrispondenza in *text*: la sottostringa corrispondente nel gruppo di acquisizione indicato *captureGroup*, facoltativamente convertita in *typeLiteral*.

Se non esiste alcuna corrispondenza o la conversione del tipo non riesce: `null`.

**esempi**

Nella stringa di esempio `Trace` viene ricercata una definizione per `Duration`. La corrispondenza viene convertita in `real`, quindi viene moltiplicata per una costante di tempo (`1s`) in modo che `Duration` sia di tipo `timespan`. In questo esempio è uguale a 123,45 secondi:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Questo esempio è equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True se l'argomento è una stringa vuota o è null. Vedere anche [isnull](#isnull).


**Sintassi**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Restituisce**

Indica se l'argomento è una stringa vuota o isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false


**Esempio**


    T | where isempty(fieldName) | count




### replace

Sostituire tutte le corrispondenze di regex con un'altra stringa.

**Sintassi**

    replace(regex, rewrite, text)

**Argomenti**

* *regex:* [espressione regolare](https://github.com/google/re2/wiki/Syntax) per ricercare *text*. Può contenere gruppi di acquisizione tra '('parentesi')'. 
* *rewrite:* regex di sostituzione per ogni corrispondenza creata da *matchingRegex*. Usare `\0` per fare riferimento all'intera corrispondenza, `\1` per il primo gruppo di acquisizione, `\2` e così via per i gruppi di acquisizione successivi.
* *text:* stringa.

**Restituisce**

*text* dopo la sostituzione di tutte le corrispondenze di *regex* con calcoli di *rewrite*. Le corrispondenze non si sovrappongano.

**Esempio**

L'istruzione seguente:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Ha i seguenti risultati:

| x | str | valore sostituito|
|---|---|---|
| 1 | Number is 1.000000 | Number was: 1.000000|
| 2 | Number is 2.000000 | Number was: 2.000000|
| 3 | Number is 3.000000 | Number was: 3.000000|
| 4 | Number is 4.000000 | Number was: 4.000000|
| 5 | Number is 5.000000 | Number was: 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Suddivide una stringa specificata in base a un delimitatore specificato e restituisce una matrice di stringhe con le sottostringhe contenute. Facoltativamente, è possibile restituire una sottostringa specifica, se esistente.

**Sintassi**

    split(source, delimiter [, requestedIndex])

**Argomenti**

* *source*: stringa di origine che verrà suddivisa in base al delimitatore specificato.
* *delimiter*: delimitatore che verrà usato per suddividere la stringa di origine.
* *requestedIndex*: indice su base zero facoltativo `int`. Se specificato, la matrice di stringhe restituita conterrà la sottostringa richiesta, se esistente. 

**Restituisce**

Una matrice di stringhe che contiene le sottostringhe della stringa di origine specificata delimitate dal delimitatore specificato.

**esempi**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

Concatena da 1 a 16 argomenti, che devono essere stringhe.

### strlen

    strlen("hello") == 5

Lunghezza di una stringa.

### substring

    substring("abcdefg", 1, 2) == "bc"

Estrarre una sottostringa da una stringa di origine specificata a partire da un indice specificato. Facoltativamente, è possibile specificare la lunghezza della sottostringa richiesta.

**Sintassi**

    substring(source, startingIndex [, length])

**Argomenti**

* *source:* stringa di origine da cui viene ricavata la sottostringa.
* *startingIndex:* posizione del carattere iniziale su base zero della sottostringa richiesta.
* *length:* parametro facoltativo che può essere usato per specificare il numero di caratteri richiesto nella sottostringa. 

**Restituisce**

Una sottostringa ricavata dalla stringa specificata. La sottostringa inizia dalla posizione del carattere startingIndex (su base zero) e continua fino alla fine della stringa o dei caratteri di lunghezza, se specificati.

**esempi**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Converte una stringa in lettere minuscole.

### toupper

    toupper("hello") == "HELLO"

Converte una stringa in lettere maiuscole.



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## Matrici e oggetti - tipi dinamici

[valori letterali](#dynamic-literals) | [cast](#casting-dynamic-objects) | [operatori](#operators) | [clausole let](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Di seguito il risultato di una query su un'eccezione di Application Insights. Il valore in `details` è una matrice.

![](./media/app-insights-analytics-scalars/310.png)

**Indicizzazione** Indicizzare matrici e oggetti come in JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Usare però `arraylength` e altre funzioni di analisi. Non usare ".length".

**Cast** In alcuni casi è necessario eseguire il cast di un elemento estratto da un oggetto perché il tipo può variare. Ad esempio, `summarize...to` richiede un tipo specifico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Valori letterali** Per creare una matrice esplicita o un oggetto contenitore delle proprietà, scrivere l'elemento come stringa JSON ed eseguire il cast:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** per suddividere le proprietà di un oggetto in righe separate, usare mvexpand:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-scalars/410.png)


**treepath:** per trovare tutti i percorsi in un oggetto complesso:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-scalars/420.png)

**buildschema:** per trovare lo schema minimo che ammette tutti i valori dell'espressione nella tabella:

    exceptions | summarize buildschema(details)

Risultato:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Si noti che `indexer` indica il punto in cui è necessario usare un indice numerico. Per questo schema, alcuni percorsi validi potrebbero essere i seguenti (presupponendo che questi indici di esempio siano inclusi nell'intervallo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### Valori letterali di matrice e oggetto

Per creare un valore letterale dinamico, usare `parsejson` (alias `todynamic`) con un argomento di stringa JSON:

* `parsejson('[43, 21, 65]')`: una matrice di numeri
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`: un singolo valore di tipo dinamico che contiene un numero
* `parsejson('"21"')`: un singolo valore di tipo dinamico che contiene una stringa

Si noti che, a differenza di JavaScript, JSON impone l'uso di virgolette doppie (`"`) per racchiudere le stringhe. Per questa ragione, è in genere più semplice fare riferimento ai valori letterali di stringa con codifica JSON usando le virgolette singole (`'`).

Questo esempio crea un valore dinamico e quindi usa i campi:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Operatori e funzioni su tipi dinamici

|||
|---|---|
| *value* `in` *array*| True se è presente un elemento di *array* che è == *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True se non è presente un elemento di *array* che è == *value*
|[`arraylength(`array`)`](#arraylength)| Null se non è una matrice
|[`extractjson(`path,object`)`](#extractjson)|Usa path per navigare nell'oggetto.
|[`parsejson(`source`)`](#parsejson)| Converte una stringa JSON in un oggetto dinamico.
|[`range(`from,to,step`)`](#range)| Una matrice di valori
|[`mvexpand` listColumn](app-insights-analytics-queries.md#mvexpand-operator) | Replica una riga per ogni valore in un elenco in una cella specificata.
|[`summarize buildschema(`column`)`](app-insights-analytics-queries.md#summarize-operator) |Deduce lo schema del tipo dal contenuto della colonna
|[`summarize makelist(`column`)` ](app-insights-analytics-queries.md#summarize-operator)| Rende flat gruppi di righe e inserisce i valori della colonna in una matrice.
|[`summarize makeset(`column`)`](app-insights-analytics-queries.md#summarize-operator) | Rende flat gruppi di righe e inserisce i valori della colonna in una matrice, senza duplicazione.

### Oggetti dinamici nelle clausole let


Poiché le [clausole let](app-insights-analytics-queries.md#let-clause) memorizzano i valori dinamici come stringhe, le due clausole seguenti sono equivalenti e richiedono entrambe `parsejson` (o `todynamic`) prima di essere usate:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

Numero di elementi in una matrice dinamica.

**Sintassi**

    arraylength(array)

**Argomenti**

* *array:* un valore `dynamic`.

**Restituisce**

Il numero di elementi in *array* oppure `null` se *array* non è una matrice.

**esempi**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Recuperare un elemento specificato da un testo JSON usando un'espressione di percorso. Facoltativamente, convertire la stringa estratta in un tipo specifico.


**Sintassi**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Restituisce**

Questa funzione esegue una query JsonPath in dataSource che contiene una stringa JSON valida e, facoltativamente, la conversione di tale valore in un tipo diverso, a seconda del terzo argomento.



**Esempio**

La notazione [parentesi quadre] e la notazione punto sono equivalenti:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### Espressioni di percorso JSON

|||
|---|---|
|`$`|Oggetto radice|
|`@`|Oggetto corrente|
|`.` o `[ ]` | Figlio|
|`[ ]`|Indice inferiore di matrice|

*(Attualmente non vengono implementati caratteri jolly, ricorsione, unione o sezioni).*


**Suggerimenti per incrementare le prestazioni**

* Applicare le clausole where prima di usare `extractjson()`
* Si consiglia di usare una corrispondenza di espressione regolare con [extract](#extract). L'esecuzione risulta molto più rapida ed è efficace se JSON è prodotto in base a un modello.
* Usare `parsejson()` se è necessario estrarre più di un valore da JSON.
* Considerare la possibilità di analizzare JSON al momento dell'inserimento dichiarando il tipo della colonna come dinamico.



### parsejson

Interpreta un valore `string` come un [valore JSON](http://json.org/) e restituisce il valore come `dynamic`. È preferibile rispetto a `extractjson()` quando è necessario estrarre più di un elemento di un oggetto composto JSON.

**Sintassi**

    parsejson(json)

**Argomenti**

* *json:* un documento JSON.

**Restituisce**

Un oggetto di tipo `dynamic` specificato da *json*.

**Esempio**

Nell'esempio seguente quando `context_custom_metrics` è un valore `string` simile al seguente:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Il frammento seguente recupera il valore dello slot `duration` nell'oggetto e da tale valore recupera due slot, `duration.value` e `duration.min`, rispettivamente `118.0` e `110.0`.

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

La funzione `range()`, da non confondere con l'operatore `range`, genera una matrice dinamica contenente una serie di valori equidistanti.

**Sintassi**

    range(start, stop, step)

**Argomenti**

* *start:* valore del primo elemento nella matrice risultante. 
* *stop:* il valore dell'ultimo elemento nella matrice risultante o il valore minimo che è maggiore rispetto all'ultimo elemento nella matrice risultante e all'interno di un numero intero multiplo di *step* da *start*.
* *step:* la differenza tra due elementi consecutivi della matrice.

**esempi**

L'esempio seguente restituisce `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

L'esempio seguente restituisce una matrice che contiene tutti i giorni dell'anno 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte una stringa in un valore dinamico.

### treepath

    treepath(dynamic_object)

Enumera tutte le espressioni di percorso che identificano gli oggetti foglia in un oggetto dinamico.

**Restituisce**

Una matrice di espressioni di percorso.

**esempi**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Si noti che "[0]" indica la presenza di una matrice, ma non specifica l'indice usato da un percorso specifico.


## Campionamento

### itemCount

Quando il [campionamento](app-insights-sampling.md) è attivo, solo una frazione dei dati generati dall'SDK viene inserita nel portale di Application Insights. Il campionamento può essere applicato nell'SDK o durante l'inserimento nell'endpoint del portale.

`itemCount` indica il numero di eventi generati nell'SDK per ogni evento visualizzato nel flusso. Ad esempio, se la frequenza di campionamento corrente è del 25%, itemCount == 4.

Per contare gli eventi, usare `sum(itemCount)` invece di `count()` per ottenere una stima precisa dei numeri effettivi. Ad esempio:

* Numero di eccezioni che si sono verificate nelle ultime due ore:

    ![](./media/app-insights-analytics-scalars/510.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!----HONumber=AcomDC_0330_2016-->