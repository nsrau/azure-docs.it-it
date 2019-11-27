---
title: Espressioni e funzioni in Azure Data Factory
description: In questo articolo vengono fornite informazioni sulle espressioni e sulle funzioni che è possibile utilizzare per la creazione di entità data factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9ef4b569fd8413d2825374c963fb272dd450cf0e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533129"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Espressioni e funzioni in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-functions-variables.md)
> * [Versione corrente](control-flow-expression-language-functions.md)

Questo articolo offre informazioni dettagliate sulle espressioni e funzioni supportate da Azure Data Factory. 

## <a name="expressions"></a>Espressioni

I valori JSON nella definizione possono essere letterali o espressioni che vengono valutate in fase di esecuzione. ad esempio:  
  
```json
"name": "value"
```

 Oppure  
  
```json
"name": "@pipeline().parameters.password"
```

Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Se un valore JSON è un'espressione, il corpo dell'espressione viene estratto rimuovendo il simbolo di chiocciola (\@). Se è necessaria una stringa letterale che inizia con \@, occorre che la stringa sia preceduta da un carattere di escape \@\@. L'esempio seguente illustra la modalità di valutazione delle espressioni.  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"parameters"|Vengono restituiti i caratteri di tipo 'parameters'.|  
|"parameters[1]"|Vengono restituiti i caratteri di tipo 'parameters[1]'.|  
|"\@\@"|Viene restituita una stringa da 1 caratteri che contiene "\@".|  
|" \@"|Viene restituita una stringa da 2 caratteri che contiene "\@".|  
  
 Tramite una funzionalità denominata *interpolazione delle stringhe*, è possibile inserire le espressioni anche all'interno delle stringhe in cui viene eseguito il wrapping delle espressioni in `@{ ... }`. Ad esempio: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando l'interpolazione delle stringhe, il risultato è sempre una stringa. Si supponga di aver definito `myNumber` come `42` e `myString` come `foo`:  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Restituisce `foo` come stringa.|  
|"\@{pipeline().parameters.myString}"| Restituisce `foo` come stringa.|  
|"\@pipeline().parameters.myNumber"| Restituisce `42` come *numero*.|  
|"\@{pipeline().parameters.myNumber}"| Restituisce `42` come *stringa*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: 42`.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Restituisce la stringa `Answer is: 42`.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>esempi

### <a name="a-dataset-with-a-parameter"></a>Un set di dati con un parametro
Nell'esempio seguente BlobDataset accetta un parametro denominato **path**. Il valore viene usato per impostare un valore per la proprietà **folderPath** usando l'espressione: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Una pipeline con un parametro
Nell'esempio seguente la pipeline accetta i parametri **inputPath** e **outputPath**. Il parametro **path** per il set di dati del BLOB con parametri viene impostato usando i valori di questi parametri. La sintassi usata è: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Esercitazione
Questa [esercitazione](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) illustra come passare i parametri tra una pipeline e un'attività nonché tra le attività.

  
## <a name="functions"></a>Funzioni

È possibile chiamare le funzioni all'interno delle espressioni. Le sezioni seguenti forniscono informazioni sulle funzioni che è possibile usare in un'espressione.  

## <a name="string-functions"></a>Funzioni stringa  

Per eseguire operazioni con le stringhe, è possibile usare queste funzioni di stringa e alcune [funzioni di raccolta](#collection-functions).
Le funzioni di stringa funzionano solo sulle stringhe.

| Funzione di stringa | Attività |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combina due o più stringhe e restituisce la stringa combinata. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Verifica se una stringa termina con la sottostringa specificata. |
| [guid](control-flow-expression-language-functions.md#guid) | Generare un identificatore univoco globale (GUID) sotto forma di stringa. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Restituisce la posizione iniziale di una sottostringa. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Restituisce la posizione iniziale o il valore di indice per l'ultima occorrenza di una sottostringa. |
| [replace](control-flow-expression-language-functions.md#replace) | Sostituisce una sottostringa con la stringa specificata e restituisce la stringa aggiornata. |
| [split](control-flow-expression-language-functions.md#split) | Restituisce una matrice che contiene le sottostringhe, separate da virgole, da una stringa più grande in base al carattere delimitatore specificato nella stringa originale. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Verifica se una stringa inizia con una sottostringa specifica. |
| [substring](control-flow-expression-language-functions.md#substring) | Restituisce i caratteri di una stringa, partendo dalla posizione specificata. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Restituisce una stringa in formato minuscolo. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Restituisce una stringa in formato maiuscolo. |
| [Trim](control-flow-expression-language-functions.md#trim) | Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata. |

## <a name="collection-functions"></a>Funzioni di raccolta

Per eseguire operazioni con le raccolte, generalmente matrici, stringhe e talvolta dizionari, è possibile usare queste funzioni di raccolta.

| Funzione di raccolta | Attività |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Verifica se una raccolta ha un elemento specifico. |
| [empty](control-flow-expression-language-functions.md#empty) | Verifica se una raccolta è vuota. |
| [first](control-flow-expression-language-functions.md#first) | Restituisce il primo elemento di una raccolta. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate. |
| [join](control-flow-expression-language-functions.md#join) | Restituisce una stringa con *tutti* gli elementi di una matrice, separati dal carattere specificato. |
| [last](control-flow-expression-language-functions.md#last) | Restituisce l'ultimo elemento di una raccolta. |
| [length](control-flow-expression-language-functions.md#length) | Restituisce il numero di elementi in una stringa o matrice. |
| [skip](control-flow-expression-language-functions.md#skip) | Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi. |
| [take](control-flow-expression-language-functions.md#take) | Restituisce gli elementi dall'inizio di una raccolta. |
| [union](control-flow-expression-language-functions.md#union) | Restituisce una collezione che contiene *tutti* gli elementi delle raccolte specificate. | 

## <a name="logical-functions"></a>Funzioni logiche  

Queste funzioni sono utili all'interno delle condizioni e possono essere usate per valutare qualsiasi tipo di logica.  
  
| Funzione di confronto logico | Attività |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Verifica se tutte le espressioni sono true. |
| [equals](control-flow-expression-language-functions.md#equals) | Verifica se entrambi i valori sono equivalenti. |
| [greater](control-flow-expression-language-functions.md#greater) | Verifica se il primo valore è maggiore del secondo. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifica se il primo valore è maggiore o uguale al secondo valore. |
| [if](control-flow-expression-language-functions.md#if) | Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato. |
| [less](control-flow-expression-language-functions.md#less) | Verifica se il primo valore è minore del secondo. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verifica se il primo valore è minore o uguale al secondo valore. |
| [not](control-flow-expression-language-functions.md#not) | Verifica se un'espressione è false. |
| [or](control-flow-expression-language-functions.md#or) | Verifica se almeno un'espressione è true. |
  
## <a name="conversion-functions"></a>Funzioni di conversione  

 Queste funzioni vengono usate per la conversione tra ogni tipo nativo nel linguaggio:  
-   string
-   integer
-   float
-   boolean
-   matrici
-   dizionari

| Funzione di conversione | Attività |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Restituisce una matrice da un singolo input specificato. Per più input, vedere [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Restituisce la versione di una stringa con codifica base64. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Restituisce la versione binaria di una stringa con codifica base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Restituisce la versione stringa di una stringa con codifica base64. |
| [binary](control-flow-expression-language-functions.md#binary) | Restituisce la versione binaria di un valore di input. |
| [bool](control-flow-expression-language-functions.md#bool) | Restituisce la versione booleana di un valore di input. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Restituisce il primo valore non Null da uno o più parametri. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Restituisce una matrice da più input. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Restituisce l'URI dati di un valore di input. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Restituisce la versione binaria di un URI dati. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Restituisce la versione stringa di un URI dati. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Restituisce la versione stringa di una stringa con codifica base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Restituisce la versione binaria di un URI dati. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Restituisce una stringa che sostituisce i caratteri non sicuri dell'URL con caratteri di escape. |
| [float](control-flow-expression-language-functions.md#float) | Restituisce un numero a virgola mobile per un valore di input. |
| [int](control-flow-expression-language-functions.md#int) | Restituisce la versione intera di una stringa. |
| [json](control-flow-expression-language-functions.md#json) | Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML. |
| [string](control-flow-expression-language-functions.md#string) | Restituisce la versione stringa di un valore di input. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Restituisce la versione codificata in formato URI per un valore di input sostituendo i caratteri non sicuri dell'URL con caratteri di escape. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Restituisce la versione binaria di una stringa con codifica URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Restituisce la versione stringa di una stringa con codifica URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Restituisce la versione XML di una stringa. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o valori corrispondenti. |

## <a name="math-functions"></a>Funzioni matematiche  
 Queste funzioni possono essere usate per qualsiasi tipo di numero, ovvero **numeri interi** e **numeri a virgola mobile**.  

| Funzione matematica | Attività |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Restituisce il risultato della somma di due numeri. |
| [div](control-flow-expression-language-functions.md#div) | Restituisce il risultato della divisione di due numeri. |
| [max](control-flow-expression-language-functions.md#max) | Restituisce il valore più alto di un set di numeri o una matrice. |
| [min](control-flow-expression-language-functions.md#min) | Restituisce il valore più basso di un set di numeri o una matrice. |
| [mod](control-flow-expression-language-functions.md#mod) | Restituisce il resto della divisione di due numeri. |
| [mul](control-flow-expression-language-functions.md#mul) | Restituire il prodotto della moltiplicazione di due numeri. |
| [rand](control-flow-expression-language-functions.md#rand) | Restituisce un numero intero casuale da un intervallo specificato. |
| [range](control-flow-expression-language-functions.md#range) | Restituisce una matrice di numeri interi che inizia da un numero intero specificato. |
| [sub](control-flow-expression-language-functions.md#sub) | Restituisce il risultato della sottrazione del secondo numero dal primo. |
  
## <a name="date-functions"></a>Funzioni di data  

| Funzione di data e ora | Attività |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Aggiunge un numero di giorni a un timestamp. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Aggiunge un numero di ore a un timestamp. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Aggiunge un numero di minuti a un timestamp. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Aggiunge un numero di secondi a un timestamp. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Aggiunge un numero di unità di tempo a un timestamp. Vedere anche [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Restituisce il giorno del componente di mese di un timestamp. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Restituisce il giorno del componente settimana di un timestamp. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Restituisce il giorno del componente anno di un timestamp. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Restituisce la data di un timestamp. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Restituisce il timestamp corrente più le unità di tempo specificate. Vedere anche [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Restituisce il timestamp corrente meno le unità di tempo specificate. Vedere anche [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Restituisce l'inizio del giorno per un timestamp. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Restituisce l'inizio dell'ora per un timestamp. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Restituisce l'inizio del mese per un timestamp. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Sottrae un numero di unità di tempo da un timestamp. Vedere [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Restituisce il valore della proprietà `ticks` per un timestamp specificato. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Restituisce il timestamp corrente come stringa. |

## <a name="function-reference"></a>Riferimento alle funzioni

Questa sezione elenca tutte le funzioni disponibili in ordine alfabetico.

<a name="add"></a>

### <a name="add"></a>add

Restituisce il risultato della somma di due numeri.

```
add(<summand_1>, <summand_2>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | SÌ | Integer, float o misto | Numeri da sommare |
|||||

| Valore restituito | Type | Description |
| ------------ | -----| ----------- |
| <*result-sum*> | Integer o float | Risultato della somma dei numeri specificati |
||||

*Esempio*

Questo esempio somma i numeri specificati:

```
add(1, 1.5)
```

E viene restituito questo risultato: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Aggiunge un numero di giorni a un timestamp.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*days*> | SÌ | Integer | Numero positivo o negativo di giorni da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp con l'aggiunta del numero di giorni specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 giorni al timestamp specificato:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-25T00:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque giorni dal timestamp specificato:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Aggiunge un numero di ore a un timestamp.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*hours*> | SÌ | Integer | Numero positivo o negativo di ore da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp con l'aggiunta del numero di ore specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 ore al timestamp specificato:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T10:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque ore dal timestamp specificato:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Aggiunge un numero di minuti a un timestamp.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*minutes*> | SÌ | Integer | Numero positivo o negativo di minuti da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp con l'aggiunta del numero di minuti specificato |
||||

*Esempio 1*

Questo esempio aggiunge 10 minuti al timestamp specificato:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T00:20:00.0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque minuti dal timestamp specificato:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Aggiunge un numero di secondi a un timestamp.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*seconds*> | SÌ | Integer | Numero positivo o negativo di secondi da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp con l'aggiunta del numero di secondi specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 secondi al timestamp specificato:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T00:00:10.0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque secondi dal timestamp specificato:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Aggiunge un numero di unità di tempo a un timestamp.
Vedere anche [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*interval*> | SÌ | Integer | Numero di unità di tempo specificate da aggiungere |
| <*timeUnit*> | SÌ | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp con l'aggiunta del numero di unità di tempo specificato  |
||||

*Esempio 1*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E viene restituito questo risultato: `"2018-01-02T00:00:00.0000000Z"`

*Esempio 2*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E restituisce il risultato usando il formato "D" facoltativo: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifica se tutte le espressioni sono true.
Restituisce true se tutte le espressioni sono true o false se almeno un'espressione è false.

```
and(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | SÌ | boolean | Espressioni da verificare |
|||||

| Valore restituito | Type | Description |
| ------------ | -----| ----------- |
| true o false | boolean | Restituisce true se tutte le espressioni sono true. Restituisce false se almeno un'espressione è false. |
||||

*Esempio 1*

Questi esempi verificano se i valori booleani specificati sono tutti true:

```
and(true, true)
and(false, true)
and(false, false)
```

E vengono restituiti questi risultati:

* Primo esempio: entrambe le espressioni sono true, quindi restituisce `true`.
* Secondo esempio: una espressione è false, quindi restituisce `false`.
* Terzo esempio: entrambe le espressioni sono false, quindi restituisce `false`.

*Esempio 2*

Questi esempi verificano se le espressioni specificate sono tutte true:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: entrambe le espressioni sono true, quindi restituisce `true`.
* Secondo esempio: una espressione è false, quindi restituisce `false`.
* Terzo esempio: entrambe le espressioni sono false, quindi restituisce `false`.

<a name="array"></a>

### <a name="array"></a>array

Restituisce una matrice da un singolo input specificato.
Per più input, vedere [createArray()](#createArray).

```
array('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa per la creazione di una matrice |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | Matrice che contiene il singolo input specificato |
||||

*Esempio*

Questo esempio crea una matrice dalla stringa "hello":

```
array('hello')
```

E viene restituito questo risultato: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Restituisce la versione di una stringa con codifica base64.

```
base64('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa di input |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*base64-string*> | Stringa | Versione della stringa di input con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "hello" in una stringa con codifica base64:

```
base64('hello')
```

E viene restituito questo risultato: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Restituisce la versione binaria di una stringa con codifica base64.

```
base64ToBinary('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con codifica base64 da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Stringa | Versione binaria della stringa con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una stringa binaria:

```
base64ToBinary('aGVsbG8=')
```

E viene restituito questo risultato:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Restituisce la versione stringa di una stringa con codifica base64, decodificando in modo efficace la stringa base64.
Usare questa funzione al posto di [decodeBase64()](#decodeBase64).
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `base64ToString()`.

```
base64ToString('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con codifica base64 da decodificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Stringa | Versione stringa di una stringa con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una semplice stringa:

```
base64ToString('aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Restituisce la versione binaria di una stringa.

```
binary('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Stringa | Versione binaria della stringa specificata |
||||

*Esempio*

Questo esempio converte la stringa "hello" in una stringa binaria:

```
binary('hello')
```

E viene restituito questo risultato:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Restituisce la versione booleana di un valore.

```
bool(<value>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Qualsiasi | Valore da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Versione booleana del valore specificato |
||||

*Esempio*

Questi esempi convertono i valori specificati in valori booleani:

```
bool(1)
bool(0)
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Restituisce il primo valore non Null da uno o più parametri.
Stringhe vuote, matrici vuote e oggetti vuoti sono non null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | SÌ | Qualsiasi, è possibile una combinazione di tipi | Uno o più elementi da verificare per determinare se sono Null |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Qualsiasi | Primo elemento o valore non Null. Se tutti i parametri sono Null, questa funzione restituisce Null. |
||||

*Esempio*

Questi esempi restituiscono il primo valore non Null dai valori specificati oppure Null quando tutti i valori sono Null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `"hello"`
* Terzo esempio: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combina due o più stringhe e restituisce la stringa combinata.

```
concat('<text1>', '<text2>', ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | SÌ | Stringa | Almeno due stringhe da combinare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Stringa | Stringa creata dalle stringhe di input combinate |
||||

*Esempio*

Questo esempio combina le stringhe "Hello" e "World":

```
concat('Hello', 'World')
```

E viene restituito questo risultato: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Verifica se una raccolta ha un elemento specifico.
Restituisce true se l'elemento viene trovato o false se non viene trovato.
Questa funzione fa distinzione tra maiuscole e minuscole.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

In particolare, questa funzione può essere usata con questi tipi di raccolta:

* Una *stringa* per trovare una *sottostringa*
* Una *matrice* per trovare un *valore*
* Un *dizionario* per trovare una *chiave*

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String, array o dictionary | Raccolta da verificare |
| <*value*> | SÌ | String, array o dictionary, rispettivamente | Elemento da trovare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se l'elemento viene trovato. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica la stringa "hello world" per cercare la sottostringa "world" e restituisce true:

```
contains('hello world', 'world')
```

*Esempio 2*

Questo esempio verifica la stringa "hello world" per cercare la sottostringa "universe" e restituisce false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*destinationTimeZone*> | SÌ | Stringa | Nome del fuso orario di destinazione. Per i nomi dei fusi orari, vedere [valori di indice del fuso orario Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura dal nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Stringa | Timestamp convertito nel fuso orario di destinazione |
||||

*Esempio 1*

Questo esempio converte un timestamp nel fuso orario specificato:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00.0000000"`

*Esempio 2*

Questo esempio converte un timestamp nel fuso orario e nel formato specificati:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converte un timestamp dal fuso orario di origine al fuso orario di destinazione.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*sourceTimeZone*> | SÌ | Stringa | Nome del fuso orario di origine. Per i nomi dei fusi orari, vedere [valori di indice del fuso orario Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura dal nome del fuso orario. |
| <*destinationTimeZone*> | SÌ | Stringa | Nome del fuso orario di destinazione. Per i nomi dei fusi orari, vedere [valori di indice del fuso orario Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura dal nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Stringa | Timestamp convertito nel fuso orario di destinazione |
||||

*Esempio 1*

Questo esempio converte il fuso orario di origine nel fuso orario di destinazione:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00.0000000"`

*Esempio 2*

Questo esempio converte un fuso orario nel fuso orario e nel formato specificati:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*sourceTimeZone*> | SÌ | Stringa | Nome del fuso orario di origine. Per i nomi dei fusi orari, vedere [valori di indice del fuso orario Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura dal nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Stringa | Timestamp convertito nell'ora UTC |
||||

*Esempio 1*

Questo esempio converte un timestamp nell'ora UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T08:00:00.0000000Z"`

*Esempio 2*

Questo esempio converte un timestamp nell'ora UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Restituisce una matrice da più input.
Per le matrici con input singolo, vedere [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | SÌ | Qualsiasi, ma non sono possibili combinazioni di tipi | Almeno due elementi per creare la matrice |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | Matrice creata da tutti gli elementi di input |
||||

*Esempio*

Questo esempio crea una matrice da questi input:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E viene restituito questo risultato: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Restituisce un URI (Uniform Resource Identifier) di dati per una stringa.

```
dataUri('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*data-uri*> | Stringa | URI di dati per la stringa di input |
||||

*Esempio*

Questo esempio crea un URI di dati per la stringa "hello":

```
dataUri('hello')
```

E viene restituito questo risultato: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati.
Usare questa funzione al posto di [decodeDataUri()](#decodeDataUri).
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `dataUriBinary()`.

```
dataUriToBinary('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | URI di dati da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Stringa | Versione binaria dell'URI di dati |
||||

*Esempio*

Questo esempio crea una versione binaria di questo URI di dati:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Restituisce la versione stringa di un URI (Uniform Resource Identifier) di dati.

```
dataUriToString('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | URI di dati da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Stringa | Versione stringa dell'URI di dati |
||||

*Esempio*

Questo esempio crea una stringa per questo URI di dati:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Restituisce il giorno del mese da un timestamp.

```
dayOfMonth('<timestamp>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | Giorno del mese dal timestamp specificato |
||||

*Esempio*

Questo esempio restituisce il numero del giorno del mese da questo timestamp:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Restituisce il giorno della settimana da un timestamp.

```
dayOfWeek('<timestamp>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | Giorno della settimana dal timestamp specificato, dove domenica corrisponde a 0, lunedì a 1 e così via |
||||

*Esempio*

Questo esempio restituisce il numero del giorno della settimana da questo timestamp:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Restituisce il giorno dell'anno da un timestamp.

```
dayOfYear('<timestamp>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Giorno dell'anno dal timestamp specificato |
||||

*Esempio*

Questo esempio restituisce il numero del giorno dell'anno da questo timestamp:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Restituisce la versione stringa di una stringa con codifica base64, decodificando in modo efficace la stringa base64.
Usare [base64ToString()](#base64ToString) al posto di `decodeBase64()`.
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `base64ToString()`.

```
decodeBase64('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con codifica base64 da decodificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Stringa | Versione stringa di una stringa con codifica base64 |
||||

*Esempio*

Questo esempio crea una stringa per una stringa con codifica base64:

```
decodeBase64('aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati.
Usare [dataUriToBinary()](#dataUriToBinary) al posto di `decodeDataUri()`.
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `dataUriToBinary()`.

```
decodeDataUri('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa dell'URI di dati da decodificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Stringa | Versione binaria di una stringa di URI di dati |
||||

*Esempio*

Questo esempio crea la versione binaria per questo URI di dati:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate.

```
decodeUriComponent('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con i caratteri di escape da decodificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Stringa | Stringa aggiornata con i caratteri di escape decodificati |
||||

*Esempio*

Questo esempio sostituisce i caratteri di escape in questa stringa con le versioni decodificate:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Restituisce il risultato intero della divisione di due numeri.
Per ottenere il resto, vedere [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | SÌ | Integer o float | Numero da dividere per l'oggetto *divisor* |
| <*divisor*> | SÌ | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0 |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | Risultato intero della divisione del primo numero per il secondo numero |
||||

*Esempio*

Entrambi gli esempi dividono il primo numero per il secondo numero:

```
div(10, 5)
div(11, 5)
```

E viene restituito questo risultato: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape.
Usare [uriComponent()](#uriComponent) al posto di `encodeUriComponent()`.
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

```
encodeUriComponent('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa da convertire nel formato con codifica URI |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Stringa | Stringa con codifica URI con i caratteri di escape |
||||

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
encodeUriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Verifica se una raccolta è vuota.
Restituisce true se la raccolta è vuota o false se non lo è.

```
empty('<collection>')
empty([<collection>])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String, array o object | Raccolta da verificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se la raccolta è vuota. Restituisce false se non è vuota. |
||||

*Esempio*

Questi esempi verificano se le raccolte specificate sono vuote:

```
empty('')
empty('abc')
```

E vengono restituiti questi risultati:

* Primo esempio: passa una stringa vuota, quindi la funzione restituisce `true`.
* Secondo esempio: passa la stringa "abc", quindi la funzione restituisce `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifica se una stringa termina con una sottostringa specifica.
Restituisce true se la sottostringa viene trovata o false se non viene trovata.
Questa funzione non fa distinzione tra maiuscole e minuscole.

```
endsWith('<text>', '<searchText>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da verificare |
| <*searchText*> | SÌ | Stringa | Sottostringa finale da trovare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false  | boolean | Restituisce true se la sottostringa finale viene trovata. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica se la stringa "hello world" termina con la stringa "world":

```
endsWith('hello world', 'world')
```

E viene restituito questo risultato: `true`

*Esempio 2*

Questo esempio verifica se la stringa "hello world" termina con la stringa "universe":

```
endsWith('hello world', 'universe')
```

E viene restituito questo risultato: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifica se entrambi i valori, le espressioni o gli oggetti sono equivalenti.
Restituisce true se entrambi gli elementi sono equivalenti o false se non lo sono.

```
equals('<object1>', '<object2>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | SÌ | Vari | Valori, espressioni o oggetti da confrontare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se entrambi gli elementi sono equivalenti. Restituisce false se gli elementi non sono equivalenti. |
||||

*Esempio*

Questi esempi verificano se gli input specificati sono equivalenti.

```
equals(true, 1)
equals('abc', 'abcd')
```

E vengono restituiti questi risultati:

* Primo esempio: entrambi i valori sono equivalenti, quindi la funzione restituisce `true`.
* Secondo esempio: entrambi i valori non sono equivalenti, quindi la funzione restituisce `false`.

<a name="first"></a>

### <a name="first"></a>first

Restituisce il primo elemento di una stringa o una matrice.

```
first('<collection>')
first([<collection>])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String o array | Raccolta in cui trovare il primo elemento |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Qualsiasi | Primo elemento nella raccolta |
||||

*Esempio*

Questi esempi trovano il primo elemento in queste raccolte:

```
first('hello')
first(createArray(0, 1, 2))
```

E vengono restituiti questi risultati:

* Primo esempio: `"h"`
* Secondo esempio: `0`

<a name="float"></a>

### <a name="float"></a>float

Converte una versione stringa per un numero a virgola mobile in un numero a virgola mobile effettivo.

```
float('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con un numero a virgola mobile valido da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Numero a virgola mobile per la stringa specificata |
||||

*Esempio*

Questo esempio crea una versione stringa per questo numero a virgola mobile:

```
float('10.333')
```

E viene restituito questo risultato: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Restituisce un timestamp nel formato specificato.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | Stringa | Timestamp aggiornato nel formato specificato |
||||

*Esempio*

Questo esempio converte un timestamp nel formato specificato:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E viene restituito questo risultato: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Restituisce il timestamp corrente più le unità di tempo specificate.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | SÌ | Integer | Numero di unità di tempo specificate da aggiungere |
| <*timeUnit*> | SÌ | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp corrente con l'aggiunta del numero di unità di tempo specificato |
||||

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z".
Questo esempio aggiunge cinque giorni al timestamp:

```
getFutureTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-03-06T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z".
Questo esempio aggiunge cinque giorni e converte il risultato nel formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Restituisce il timestamp corrente meno le unità di tempo specificate.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | SÌ | Integer | Numero di unità di tempo specificate da sottrarre |
| <*timeUnit*> | SÌ | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp corrente meno il numero di unità di tempo specificato |
||||

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z".
Questo esempio sottrae cinque giorni dal timestamp:

```
getPastTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-01-27T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z".
Questo esempio sottrae cinque giorni e converte il risultato nel formato "D":

```
getPastTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifica se il primo valore è maggiore del secondo.
Restituisce true se il primo valore è maggiore o false se è minore.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Integer, float o string | Primo valore da verificare per determinare se è maggiore del secondo |
| <*compareTo*> | SÌ | Integer, float o string, rispettivamente | Valore di confronto |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se il primo valore è maggiore del secondo. Restituisce false se il primo valore è minore o uguale al secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è maggiore del secondo:

```
greater(10, 5)
greater('apple', 'banana')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifica se il primo valore è maggiore o uguale al secondo valore.
Restituisce true se il primo valore è maggiore o uguale o false se è minore.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Integer, float o string | Primo valore da verificare per determinare se è maggiore o uguale al secondo |
| <*compareTo*> | SÌ | Integer, float o string, rispettivamente | Valore di confronto |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se il primo valore è maggiore o uguale al secondo. Restituisce false se il primo valore è minore del secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è maggiore o uguale al secondo:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Genera un identificatore univoco globale (GUID) sotto forma di stringa, ad esempio "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

È anche possibile specificare un formato per il GUID diverso da quello predefinito, "D", che è costituito da 32 cifre separate da trattini.

```
guid('<format>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | No | Stringa | [Identificatore di formato](https://msdn.microsoft.com/library/97af8hh4) singolo per il GUID restituito. Per impostazione predefinita, il formato è "D", ma è possibile usare "N", "D", "B", "P" o "X". |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*GUID-value*> | Stringa | GUID generato in modo casuale |
||||

*Esempio*

Questo esempio genera lo stesso GUID, ma in formato a 32 cifre, separate da trattini e racchiuse tra parentesi:

```
guid('P')
```

E viene restituito questo risultato: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifica se un'espressione è true o false.
In base al risultato, restituisce un valore specificato.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | SÌ | boolean | Espressione da verificare |
| <*valueIfTrue*> | SÌ | Qualsiasi | Valore da restituire se l'espressione è true |
| <*valueIfFalse*> | SÌ | Qualsiasi | Valore da restituire se l'espressione è false |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Qualsiasi | Valore specificato restituito a seconda del fatto che l'espressione sia true o false |
||||

*Esempio*

Questo esempio restituisce `"yes"` perché l'espressione specificata restituisce true.
In caso contrario, l'esempio restituisce `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Restituisce la posizione iniziale o il valore di indice di una sottostringa.
Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0.

```
indexOf('<text>', '<searchText>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa che contiene la sottostringa da trovare |
| <*searchText*> | SÌ | Stringa | Sottostringa da trovare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Posizione iniziale o valore di indice per la sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. |
||||

*Esempio*

Questo esempio trova il valore di indice iniziale della sottostringa "world" nella stringa "hello world":

```
indexOf('hello world', 'world')
```

E viene restituito questo risultato: `6`

<a name="int"></a>

### <a name="int"></a>int

Restituisce la versione intera di una stringa.

```
int('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Versione integer della stringa specificata |
||||

*Esempio*

Questo esempio crea una versione integer per la stringa "10":

```
int('10')
```

E viene restituito questo risultato: `10`

<a name="json"></a>

### <a name="json"></a>json

Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML.

```
json('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa o elemento XML | Stringa o elemento XML da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Oggetto o tipo nativo JSON | Oggetto o valore del tipo nativo JSON per la stringa o l'elemento XML specificato. Se la stringa è Null, la funzione restituisce un oggetto vuoto. |
||||

*Esempio 1*

Questo esempio converte questa stringa nel valore JSON:

```
json('[1, 2, 3]')
```

E viene restituito questo risultato: `[1, 2, 3]`

*Esempio 2*

Questo esempio converte questa stringa in JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E viene restituito questo risultato:

```
{
  "fullName": "Sophia Owen"
}
```

*Esempio 3*

Questo esempio converte questo elemento XML in JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E viene restituito questo risultato:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersezione

Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate.
Per essere incluso nel risultato, un elemento deve essere presente in tutte le raccolte passate alla funzione.
Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | SÌ | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *solo* gli elementi comuni |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*common-items*> | Array o object, rispettivamente | Raccolta che contiene solo gli elementi comuni alle raccolte specificate |
||||

*Esempio*

Questo esempio trova gli elementi comuni tra queste matrici:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E restituisce una matrice con *solo* questi elementi: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Restituisce una stringa con tutti gli elementi di una matrice, in cui ogni carattere è separato da un *delimitatore*.

```
join([<collection>], '<delimiter>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | Array | Matrice contenente gli elementi da aggiungere |
| <*delimiter*> | SÌ | Stringa | Separatore visualizzato tra ogni carattere nella stringa risultante |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Stringa | Stringa risultante creata da tutti gli elementi nella matrice specificata |
||||

*Esempio*

Questo esempio crea una stringa da tutti gli elementi in questa matrice usando il carattere specificato come delimitatore:

```
join(createArray('a', 'b', 'c'), '.')
```

E viene restituito questo risultato: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Restituisce l'ultimo elemento di una raccolta.

```
last('<collection>')
last([<collection>])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String o array | Raccolta dove trovare l'ultimo elemento |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | String o array, rispettivamente | Ultimo elemento nella raccolta |
||||

*Esempio*

Questi esempi trovano l'ultimo elemento in queste raccolte:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: `"d"`
* Secondo esempio: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Restituire la posizione iniziale o il valore di indice per l'ultima occorrenza di una sottostringa.
Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa che contiene la sottostringa da trovare |
| <*searchText*> | SÌ | Stringa | Sottostringa da trovare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | La posizione iniziale o il valore di indice per l'ultima occorrenza della sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. |
||||

*Esempio*

Questo esempio trova il valore di indice iniziale per l'ultima occorrenza della sottostringa "world" nella stringa "hello world":

```
lastIndexOf('hello world', 'world')
```

E viene restituito questo risultato: `6`

<a name="length"></a>

### <a name="length"></a>length

Restituisce il numero di elementi in una raccolta.

```
length('<collection>')
length([<collection>])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String o array | Raccolta con gli elementi da contare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Numero di elementi nella raccolta |
||||

*Esempio*

Questi esempi contano il numero di elementi in queste raccolte:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E viene restituito questo risultato: `4`

<a name="less"></a>

### <a name="less"></a>less

Verifica se il primo valore è minore del secondo.
Restituisce true se il primo valore è minore o false se è maggiore.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Integer, float o string | Primo valore da verificare per determinare se è minore del secondo |
| <*compareTo*> | SÌ | Integer, float o string, rispettivamente | Elemento di confronto |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se il primo valore è minore del secondo. Restituisce false se il primo valore è maggiore o uguale al secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è minore del secondo.

```
less(5, 10)
less('banana', 'apple')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifica se il primo valore è minore o uguale al secondo valore.
Restituisce true se il primo valore è minore o uguale o false se è maggiore.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Integer, float o string | Primo valore da verificare per determinare se è minore o uguale al secondo |
| <*compareTo*> | SÌ | Integer, float o string, rispettivamente | Elemento di confronto |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false  | boolean | Restituisce true se il primo valore è minore o uguale al secondo. Restituisce false se il primo valore è maggiore del secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è minore o uguale al secondo valore.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="max"></a>

### <a name="max"></a>max

Restituisce il valore più alto di un elenco o una matrice con numeri che includono gli estremi.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | SÌ | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più alto |
| [<*number1*>, <*number2*>, ...] | SÌ | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più alto |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*max-value*> | Integer o float | Valore più alto nella matrice o nel set di numeri specificato |
||||

*Esempio*

Questi esempi ottengono il valore più alto dal set di numeri e dalla matrice:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E viene restituito questo risultato: `3`

<a name="min"></a>

### <a name="min"></a>min

Restituisce il valore più basso di un set di numeri o una matrice.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | SÌ | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più basso |
| [<*number1*>, <*number2*>, ...] | SÌ | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più basso |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*min-value*> | Integer o float | Valore più basso nella matrice o nel set di numeri specificato |
||||

*Esempio*

Questi esempi ottengono il valore più basso nel set di numeri e nella matrice:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E viene restituito questo risultato: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Restituisce il resto della divisione di due numeri.
Per ottenere il risultato intero, vedere [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | SÌ | Integer o float | Numero da dividere per l'oggetto *divisor* |
| <*divisor*> | SÌ | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Integer o float | Resto della divisione del primo numero per il secondo numero |
||||

*Esempio*

Questo esempio divide il primo numero per il secondo numero:

```
mod(3, 2)
```

E viene restituito questo risultato: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Restituire il prodotto della moltiplicazione di due numeri.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | SÌ | Integer o float | Numero da moltiplicare per *multiplicand2* |
| <*multiplicand2*> | SÌ | Integer o float | Numero che moltiplica *multiplicand1* |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*product-result*> | Integer o float | Prodotto della moltiplicazione del primo numero per il secondo numero |
||||

*Esempio*

Questi esempi moltiplicano il primo numero per il secondo numero:

```
mul(1, 2)
mul(1.5, 2)
```

E vengono restituiti questi risultati:

* Primo esempio: `2`
* Secondo esempio: `3`

<a name="not"></a>

### <a name="not"></a>not

Verifica se un'espressione è false.
Restituisce true se l'espressione è false o false se è true.

```json
not(<expression>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | SÌ | boolean | Espressione da verificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se l'espressione è false. Restituisce false se l'espressione è true. |
||||

*Esempio 1*

Questi esempi verificano se le espressioni specificate sono false:

```json
not(false)
not(true)
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se le espressioni specificate sono false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

<a name="or"></a>

### <a name="or"></a>Oppure

Verifica se almeno un'espressione è true.
Restituisce true se almeno un'espressione è true o false se tutte le espressioni sono false.

```
or(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | SÌ | boolean | Espressioni da verificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false | boolean | Restituisce true se almeno un'espressione è true. Restituisce false se tutte le espressioni sono false. |
||||

*Esempio 1*

Questi esempi verificano se almeno un'espressione è true:

```json
or(true, false)
or(false, false)
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se almeno un'espressione è true:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

<a name="rand"></a>

### <a name="rand"></a>rand

Restituisce un valore intero casuale da un intervallo specificato, che include solo l'estremo inziale.

```
rand(<minValue>, <maxValue>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | SÌ | Integer | Valore intero più basso nell'intervallo |
| <*maxValue*> | SÌ | Integer | Valore intero che segue il valore intero più alto nell'intervallo che la funzione può restituire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Valore intero casuale restituito dall'intervallo specificato |
||||

*Esempio*

Questo esempio ottiene un valore intero casuale dall'intervallo specificato, escluso il valore massimo:

```
rand(1, 5)
```

E restituisce uno di questi numeri come risultato: `1`, `2`, `3` o `4`

<a name="range"></a>

### <a name="range"></a>range

Restituisce una matrice di numeri interi che inizia da un numero intero specificato.

```
range(<startIndex>, <count>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | SÌ | Integer | Valore intero che avvia la matrice come primo elemento |
| <*count*> | SÌ | Integer | Numero di valori interi nella matrice |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | Matrice di valori interi a partire dall'indice specificato |
||||

*Esempio*

Questo esempio crea una matrice di valori interi che inizia dall'indice specificato e include il numero di valori interi specificato:

```
range(1, 4)
```

E viene restituito questo risultato: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

Sostituisce una sottostringa con la stringa specificata e restituisce la stringa risultante. Questa funzione fa distinzione tra maiuscole e minuscole.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa che contiene la sottostringa da sostituire |
| <*oldText*> | SÌ | Stringa | Sottostringa da sostituire |
| <*newText*> | SÌ | Stringa | Stringa da usare per la sostituzione |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-text*> | Stringa | Stringa aggiornata dopo la sostituzione della sottostringa <p>Se la sottostringa non viene trovata, viene restituita la stringa originale. |
||||

*Esempio*

Questo esempio trova la sottostringa "old" in "the old string" e sostituisce "old" con "new":

```
replace('the old string', 'old', 'new')
```

E viene restituito questo risultato: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi.

```
skip([<collection>], <count>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | Array | Raccolta da cui rimuovere gli elementi |
| <*count*> | SÌ | Integer | Valore intero positivo per il numero di elementi da rimuovere nella parte iniziale |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | Raccolta aggiornata dopo la rimozione degli elementi specificati |
||||

*Esempio*

Questo esempio rimuove un elemento, il numero 0, dall'inizio della matrice specificata:

```
skip(createArray(0, 1, 2, 3), 1)
```

E restituisce la matrice con gli elementi rimanenti: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Restituisce una matrice che contiene sottostringhe, separate da virgole, in base al carattere delimitatore specificato nella stringa originale.

```
split('<text>', '<delimiter>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da suddividere in sottostringhe in base al delimitatore specificato nella stringa originale |
| <*delimiter*> | SÌ | Stringa | Carattere nella stringa originale da usare come delimitatore |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Matrice che contiene le sottostringhe della stringa originale, separate da virgole |
||||

*Esempio*

Questo esempio crea una matrice con le sottostringhe della stringa specificata in base al carattere specificato come delimitatore:

```
split('a_b_c', '_')
```

E restituisce questa matrice come risultato: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Restituisce l'inizio del giorno per un timestamp.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp specificato, che inizia in corrispondenza della mezzanotte esatta per il giorno |
||||

*Esempio*

Questo esempio trova l'inizio del giorno per questo timestamp:

```
startOfDay('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Restituisce l'inizio dell'ora per un timestamp.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp specificato, che inizia in corrispondenza dei minuti esatti per l'ora |
||||

*Esempio*

Questo esempio trova l'inizio dell'ora per questo timestamp:

```
startOfHour('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Restituisce l'inizio del mese per un timestamp.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp specificato, che inizia il primo giorno del mese in corrispondenza della mezzanotte esatta |
||||

*Esempio*

Questo esempio restituisce l'inizio del mese per questo timestamp:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifica se una stringa inizia con una sottostringa specifica.
Restituisce true se la sottostringa viene trovata o false se non viene trovata.
Questa funzione non fa distinzione tra maiuscole e minuscole.

```
startsWith('<text>', '<searchText>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da verificare |
| <*searchText*> | SÌ | Stringa | Stringa iniziale da trovare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| true o false  | boolean | Restituisce true se la sottostringa iniziale viene trovata. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica se la stringa "hello world" inizia con la sottostringa "hello":

```
startsWith('hello world', 'hello')
```

E viene restituito questo risultato: `true`

*Esempio 2*

Questo esempio verifica se la stringa "hello world" inizia con la sottostringa "greetings":

```
startsWith('hello world', 'greetings')
```

E viene restituito questo risultato: `false`

<a name="string"></a>

### <a name="string"></a>string

Restituisce la versione stringa di un valore.

```
string(<value>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Qualsiasi | Valore da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*string-value*> | Stringa | Versione stringa del valore specificato |
||||

*Esempio 1*

Questo esempio crea la versione stringa per questo numero:

```
string(10)
```

E viene restituito questo risultato: `"10"`

*Esempio 2*

Questo esempio crea una stringa per l'oggetto JSON specificato e usa il carattere barra rovesciata (\\) come carattere di escape per le virgolette doppie (").

```
string( { "name": "Sophie Owen" } )
```

E viene restituito questo risultato: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Restituisce il risultato della sottrazione del secondo numero dal primo.

```
sub(<minuend>, <subtrahend>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | SÌ | Integer o float | Numero da cui sottrarre l'oggetto *subtrahend* |
| <*subtrahend*> | SÌ | Integer o float | Numero da sottrarre dall'oggetto *minuend* |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*result*> | Integer o float | Risultato della sottrazione del secondo numero dal primo |
||||

*Esempio*

Questo esempio sottrae il secondo numero dal primo:

```
sub(10.3, .3)
```

E viene restituito questo risultato: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Restituisce i caratteri di una stringa, partendo dalla posizione o dall'indice specificato.
I valori di Indice iniziano con il numero 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da cui ottenere i caratteri |
| <*startIndex*> | SÌ | Integer | Numero positivo maggiore o uguale a 0 che si desidera utilizzare come posizione iniziale o valore di indice. |
| <*length*> | SÌ | Integer | Numero positivo di caratteri da includere nella sottostringa |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*substring-result*> | Stringa | Sottostringa con il numero di caratteri specificato, che inizia in corrispondenza della posizione di indice specificata nella stringa di origine |
||||

*Esempio*

Questo esempio crea una sottostringa di cinque caratteri dalla stringa specificata, a partire dal valore di indice 6:

```
substring('hello world', 6, 5)
```

E viene restituito questo risultato: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Sottrae un numero di unità di tempo da un timestamp.
Vedere [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa contenente il timestamp |
| <*interval*> | SÌ | Integer | Numero di unità di tempo specificate da sottrarre |
| <*timeUnit*> | SÌ | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp meno il numero di unità di tempo specificato |
||||

*Esempio 1*

Questo esempio sottrae un giorno dal timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae un giorno dal timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E restituisce questo risultato usando il formato "D" facoltativo: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Restituisce gli elementi dall'inizio di una raccolta.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | SÌ | String o array | Raccolta da cui ottenere gli elementi |
| <*count*> | SÌ | Integer | Valore intero positivo per il numero di elementi da ottenere dalla parte iniziale |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*subset*> o [<*subset*>] | String o array, rispettivamente | Stringa o matrice con il numero specificato di elementi presi dall'inizio della raccolta originale |
||||

*Esempio*

Questi esempi ottengono il numero specificato di elementi presi dall'inizio di queste raccolte:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E vengono restituiti questi risultati:

* Primo esempio: `"abc"`
* Secondo esempio: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Restituisce il valore della proprietà `ticks` per un timestamp specificato.
Un *tick* è un intervallo di 100 nanosecondi.

```
ticks('<timestamp>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | SÌ | Stringa | Stringa per un timestamp |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Numero di tick dal timestamp specificato |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Restituisce una stringa in formato minuscolo. Se un carattere nella stringa non ha un equivalente minuscolo, viene incluso senza modifiche nella stringa restituita.

```
toLower('<text>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da restituire in formato minuscolo |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | Stringa | Stringa originale in formato minuscolo |
||||

*Esempio*

Questo esempio converte questa stringa in caratteri minuscoli:

```
toLower('Hello World')
```

E viene restituito questo risultato: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Restituisce una stringa in formato maiuscolo. Se un carattere nella stringa non ha un equivalente maiuscolo, viene incluso senza modifiche nella stringa restituita.

```
toUpper('<text>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da restituire in formato maiuscolo |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | Stringa | Stringa originale in formato maiuscolo |
||||

*Esempio*

Questo esempio converte questa stringa in caratteri maiuscoli:

```
toUpper('Hello World')
```

E viene restituito questo risultato: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Trim

Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata.

```
trim('<text>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | SÌ | Stringa | Stringa da cui rimuovere gli spazi iniziali e finali |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedText*> | Stringa | Versione aggiornata della stringa originale senza spazi iniziali o finali |
||||

*Esempio*

Questo esempio rimuove gli spazi iniziali e finali dalla stringa " Hello World  ":

```
trim(' Hello World  ')
```

E viene restituito questo risultato: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Restituisce una collezione che contiene *tutti* gli elementi delle raccolte specificate.
Per essere incluso nel risultato, un elemento può essere presente in qualsiasi raccolta passata alla funzione. Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | SÌ | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *tutti* gli elementi |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Array o object, rispettivamente | Raccolta con tutti gli elementi delle raccolte specificate e senza duplicati |
||||

*Esempio*

Questo esempio ottiene *tutti* gli elementi da queste raccolte:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E viene restituito questo risultato: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape.
Usare questa funzione al posto di [encodeUriComponent()](#encodeUriComponent).
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

```
uriComponent('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa da convertire nel formato con codifica URI |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Stringa | Stringa con codifica URI con i caratteri di escape |
||||

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
uriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Restituisce la versione binaria di un componente di un URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con codifica URI da convertire |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Stringa | Versione binaria della stringa con codifica URI. Il contenuto binario è con codifica base64 e rappresentato da `$content`. |
||||

*Esempio*

Questo esempio crea la versione binaria per questa stringa con codifica URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Restituisce la versione stringa di una stringa con codifica URI (Uniform Resource Identifier), decodificando in modo efficace la stringa con codifica URI.

```
uriComponentToString('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con codifica URI da decodificare |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Stringa | Versione decodificata della stringa con codifica URI |
||||

*Esempio*

Questo esempio crea la versione stringa decodificata per questa stringa con codifica URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Restituisce il timestamp corrente.

```
utcNow('<format>')
```

Facoltativamente, è possibile specificare un formato diverso con il parametro <*format*>.

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | No | Stringa | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | Stringa | Data e ora correnti |
||||

*Esempio 1*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00.
Questo esempio ottiene il timestamp corrente:

```
utcNow()
```

E viene restituito questo risultato: `"2018-04-15T13:00:00.0000000Z"`

*Esempio 2*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00.
Questo esempio ottiene il timestamp corrente usando il formato "D" facoltativo:

```
utcNow('D')
```

E viene restituito questo risultato: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>Xml

Restituisce la versione XML di una stringa contenente un oggetto JSON.

```
xml('<value>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | SÌ | Stringa | Stringa con l'oggetto JSON da convertire <p>L'oggetto JSON deve avere una sola proprietà radice, che non può essere una matrice. <br>Il carattere barra rovesciata (\\) viene usato come carattere di escape per le virgolette doppie ("). |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-version*> | Oggetto | Elemento XML codificato per la stringa o l'oggetto JSON specificato |
||||

*Esempio 1*

Questo esempio crea la versione XML per questa stringa, che contiene un oggetto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E viene restituito questo elemento XML:

```xml
<name>Sophia Owen</name>
```

*Esempio 2*

Si supponga di avere questo oggetto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Questo esempio crea la versione XML per una stringa contenente questo oggetto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E viene restituito questo elemento XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o valori corrispondenti. Un'espressione XPath, o semplicemente "XPath", aiuta a spostarsi nella struttura del documento XML in modo che sia possibile selezionare i nodi o i valori di calcolo nel contenuto XML.

```
xpath('<xml>', '<xpath>')
```

| Parametro | Obbligatoria | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*xml*> | SÌ | Qualsiasi | Stringa XML per eseguire la ricerca di nodi o valori che corrispondono a un valore dell'espressione XPath |
| <*xpath*> | SÌ | Qualsiasi | Espressione XPath usata per trovare i valori o i nodi XML corrispondenti |
|||||

| Valore restituito | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Nodo XML, quando solo un singolo nodo corrisponde all'espressione XPath specificata |
| <*value*> | Qualsiasi | Valore di un nodo XML, quando solo un singolo valore corrisponde all'espressione XPath specificata |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-oppure- </br>[<*value1*>, <*value2*>, ...] | Array | Matrice con tutti i valori o i nodi XML che corrispondono all'espressione XPath specificata |
||||

*Esempio 1*

Dopo l'esempio 1, questo esempio trova i nodi che corrispondono al nodo `<count></count>` e somma i valori di tali nodi con la funzione `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E viene restituito questo risultato: `30`

*Esempio 2*

Per questo esempio, entrambe le espressioni trovano i nodi che corrispondono al nodo `<location></location>`, negli argomenti specificati, che includono codice XML con uno spazio dei nomi. Le espressioni usano il carattere barra rovesciata (\\) come carattere di escape per le virgolette doppie (").

* *Espressione 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Espressione 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Ecco gli argomenti:

* Codice XML, che include lo spazio dei nomi del documento XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Una di queste espressioni XPath:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Ecco il nodo del risultato che corrisponde al nodo `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Esempio 3*

Dopo l'esempio 3, questo esempio trova il valore nel nodo `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E viene restituito questo risultato: `"Paris"`

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di variabili di sistema che è possibile usare nelle espressioni, vedere [Variabili di sistema](control-flow-system-variables.md).
