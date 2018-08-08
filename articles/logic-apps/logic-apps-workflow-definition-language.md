---
title: Informazioni di riferimento sullo schema per il linguaggio di definizione del flusso di lavoro - App per la logica di Azure | Microsoft Docs
description: Scrivere definizioni del flusso di lavoro personalizzate per App per la logica di Azure con il linguaggio di definizione del flusso di lavoro
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 0ac191f1191357ecc7292d51b298567f7f4e4786
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391225"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Informazioni di riferimento sullo schema per il linguaggio di definizione del flusso di lavoro in App per la logica di Azure

Quando si crea un flusso di lavoro di app per la logica con [App per la logica di Azure](../logic-apps/logic-apps-overview.md), la definizione alla base del flusso di lavoro descrive la logica effettiva eseguita per l'app per la logica. Questa descrizione segue una struttura definita e convalidata dallo schema del linguaggio di definizione del flusso di lavoro che usa [JSON (JavaScript Object Notation)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Struttura della definizione del flusso di lavoro

Una definizione del flusso di lavoro ha almeno un trigger che crea un'istanza dell'app per la logica, più una o più azioni eseguite dall'app per la logica. 

Di seguito è riportata la struttura generale di una definizione del flusso di lavoro:  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Elemento | Obbligatoria | DESCRIZIONE | 
|---------|----------|-------------| 
| Definizione | Yes | Elemento iniziale della definizione del flusso di lavoro | 
| $schema | Solo quando si fa riferimento esternamente a una definizione del flusso di lavoro | Percorso del file di schema JSON che descrive la versione del linguaggio di definizione del flusso di lavoro, disponibile qui: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | No  | Numero di versione della definizione del flusso di lavoro, per impostazione predefinita "1.0.0.0". Specificare un valore da usare per identificare e confermare la definizione corretta durante la distribuzione di un flusso di lavoro. | 
| Parametri | No  | Definizioni di uno o più parametri che trasferiscono i dati nel flusso di lavoro <p><p>Numero massimo di parametri: 50 | 
| trigger | No  | Definizioni di uno o più trigger che creano istanze del flusso di lavoro. È possibile definire più di un trigger, ma solo con il linguaggio di definizione del flusso di lavoro, non in modo visivo tramite Progettazione app per la logica. <p><p>Numero massimo di trigger: 10 | 
| Azioni | No  | Definizioni di una o più azioni da eseguire in fase di esecuzione del flusso di lavoro <p><p>Numero massimo di azioni: 250 | 
| outputs | No  | Definizioni degli output restituiti dall'esecuzione di un flusso di lavoro <p><p>Numero massimo di output: 10 |  
|||| 

## <a name="parameters"></a>Parametri

Nella sezione `parameters` definire tutti i parametri del flusso di lavoro usati dall'app per la logica durante l'implementazione per accettare gli input. Sia le dichiarazioni che i valori dei parametri sono necessari durante l'installazione. Prima di poter usare questi parametri in altre sezioni del flusso di lavoro, assicurarsi di aver dichiarato tutti i parametri in queste sezioni. 

Di seguito è riportata la struttura generale della definizione di un parametro:  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| Elemento | Obbligatoria | type | DESCRIZIONE |  
|---------|----------|------|-------------|  
| type | Yes | int, float, string, securestring, bool, array, JSON object, secureobject <p><p>**Nota**: Per tutte le password, le chiavi e i segreti, usare i tipi `securestring` e `secureobject` perché l'operazione `GET` non restituisce questi tipi. | Tipo di parametro |
| defaultValue | No  | Uguale a `type` | Valore del parametro predefinito quando non viene specificato alcun valore durante la creazione dell'istanza del flusso di lavoro | 
| allowedValues | No  | Uguale a `type` | Matrice con valori che il parametro può accettare |  
| metadata | No  | Oggetto JSON | Qualsiasi altro parametro, ad esempio il nome o una descrizione leggibile dell'app per la logica o i dati di progettazione usati da Visual Studio o altri strumenti |  
||||

## <a name="triggers-and-actions"></a>Trigger e azioni  

In una definizione del flusso di lavoro, le sezioni `triggers` e `actions` definiscono le chiamate che si verificano durante l'esecuzione del flusso di lavoro. Per la sintassi e altre informazioni su queste sezioni, vedere [Trigger e azioni del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Output 

Nella sezione `outputs` definire i dati che il flusso di lavoro può restituire al termine dell'esecuzione. Ad esempio, per tenere traccia di uno stato o di un valore specifico per ogni esecuzione, specificare che l'output del flusso di lavoro restituisca tali dati. 

> [!NOTE]
> Quando si risponde a richieste in entrata dall'API REST di un servizio, non usare `outputs`, ma il tipo di azione `Response`. Per altre informazioni, vedere [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md).

Di seguito è riportata la struttura generale della definizione di un output: 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| Elemento | Obbligatoria | type | DESCRIZIONE | 
|---------|----------|------|-------------| 
| <*key-name*> | Yes | string | Valore chiave del valore di output restituito |  
| type | Yes | int, float, string, securestring, bool, array, JSON object | Tipo di valore di output restituito | 
| value | Yes | Uguale a `type` | Valore di output restituito |  
||||| 

Per ottenere l'output dall'esecuzione di un flusso di lavoro, esaminare la cronologia di esecuzione e i dettagli dell'app per la logica nel portale di Azure oppure usare l'[API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows). È anche possibile passare l'output a sistemi esterni, ad esempio Power BI, per creare dashboard. 

<a name="expressions"></a>

## <a name="expressions"></a>Espressioni

Con JSON è possibile avere valori letterali già esistenti in fase di progettazione, ad esempio:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

È anche possibile avere valori che non esistono fino all'esecuzione. Per rappresentare questi valori è possibile usare *espressioni* che vengono valutate in fase di esecuzione. Un'espressione è una sequenza che può contenere una o più [funzioni](#functions), [operatori](#operators), variabili, valori espliciti o costanti. Nella definizione del flusso di lavoro è possibile usare un'espressione in qualsiasi punto di un valore stringa JSON aggiungendo all'espressione il prefisso (\@). Quando si valuta un'espressione che rappresenta un valore JSON, il corpo dell'espressione viene estratto rimuovendo il carattere \@ e si ottiene sempre un altro valore JSON. 

Ad esempio, per la proprietà `customerName` definita in precedenza è possibile ottenere il valore usando la funzione [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) in un'espressione e assegnare tale valore alla proprietà `accountName`:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

Anche l'*interpolazione delle stringhe* consente di usare più espressioni all'interno delle stringhe, racchiuse dal carattere \@ e da parentesi graffe ({}). La sintassi è la seguente:

```json
@{ "<expression1>", "<expression2>" }
```

Il risultato è sempre una stringa, rendendo questa funzionalità simile alla funzione `concat()`, ad esempio: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

In presenza di una stringa letterale che inizia con il carattere \@, prefissare il carattere \@ con un altro carattere \@ come carattere di escape: \@\@

Questi esempi mostrano come vengono valutate le espressioni:

| Valore JSON | Risultato |
|------------|--------| 
| "Sophia Owen" | Restituisce questi caratteri: 'Sophia Owen' |
| "array[1]" | Restituisce questi caratteri: 'array[1]' |
| "\@\@" | Restituisce questi caratteri come stringa di un solo carattere: "\@" |   
| " \@" | Restituisce questi caratteri come stringa di due caratteri: "\@" |
|||

Per questi esempi si supponga di definire "myBirthMonth" uguale a "January" e "myAge" uguale al numero 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Questi esempi mostrano come vengono valutate le espressioni seguenti:

| Espressione JSON | Risultato |
|-----------------|--------| 
| "\@parameters('myBirthMonth')" | Restituisce la stringa: "January" |  
| "\@{parameters('myBirthMonth')}" | Restituisce la stringa: "January" |  
| "\@parameters('myAge')" | Restituisce il numero: 42 |  
| "\@{parameters('myAge')}" | Restituisce questo numero come stringa: "42" |  
| "My age is \@{parameters('myAge')}" | Restituisce la stringa: "My age is 42" |  
| "\@('My age is ', string(parameters('myAge')))" | Restituisce la stringa: "My age is 42" |  
| "My age is \@\@{parameters('myAge')}" | Restituisce questa stringa, che include l'espressione: "My age is \@{parameters('myAge')}` | 
||| 

Quando si lavora in modo visivo in Progettazione app per la logica, è possibile creare espressioni tramite il generatore di espressioni, ad esempio: 

![Progettazione app per la logica > Generatore di espressioni](./media/logic-apps-workflow-definition-language/expression-builder.png)

Al termine viene visualizzata l'espressione per la proprietà corrispondente nella definizione del flusso di lavoro, ad esempio la proprietà `searchQuery`:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operatori

Nelle [espressioni](#expressions) e nelle [funzioni](#functions), gli operatori eseguono attività specifiche, ad esempio fanno riferimento a una proprietà o un valore in una matrice. 

| Operatore | Attività | 
|----------|------|
| ' | Per usare un valore letterale di stringa come input o in espressioni e funzioni, racchiudere la stringa solo tra virgolette singole, ad esempio `'<myString>'`. Non usare le virgolette doppie (""), che sono in conflitto con la formattazione JSON per racchiudere un'espressione intera. Ad esempio:  <p>**Yes**: length('Hello') </br>**No**: length("Hello") <p>Quando si passano matrici o numeri, non è necessario il wrapping della punteggiatura. Ad esempio:  <p>**Yes**: length([1, 2, 3]) </br>**No**: length("[1, 2, 3]") | 
| [] | Per fare riferimento a un valore in una posizione specifica (indice) di una matrice, usare le parentesi quadre. Ad esempio, per ottenere il secondo elemento in una matrice: <p>`myArray[1]` | 
| . | Per fare riferimento a una proprietà in un oggetto, usare l'operatore punto. Ad esempio, per ottenere la proprietà `name` di un oggetto JSON `customer`: <p>`"@parameters('customer').name"` | 
| ? | Per fare riferimento alle proprietà Null di un oggetto senza un errore di runtime, usare l'operatore punto interrogativo. È ad esempio possibile usare questa espressione per gestire gli output di trigger Null: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Funzioni

Alcune espressioni ottengono i rispettivi valori da azioni di runtime che potrebbero non esistere all'inizio dell'esecuzione di un'app per la logica. Per usare questi valori o farvi riferimento nelle espressioni, è possibile usare le [*funzioni*](../logic-apps/workflow-definition-language-functions-reference.md). È ad esempio possibile usare le funzioni matematiche per i calcoli, ad esempio la funzione [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) che restituisce la somma di numeri interi o float. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).
In alternativa, vedere altre informazioni sulle funzioni e il loro utilizzo generico.

Ecco un paio esempi di attività che possono essere eseguite con le funzioni: 

| Attività | Sintassi della funzione | Risultato | 
| ---- | --------------- | ------ | 
| Restituisce una stringa in formato minuscolo. | toLower('<*text*>') <p>Ad esempio toLower('Hello') | "hello" | 
| Restituisce un identificatore univoco globale (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Questo esempio mostra come ottenere il valore dal parametro `customerName` e assegnarlo alla proprietà `accountName` usando la funzione [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) in un'espressione:

```json
"accountName": "@parameters('customerName')"
```

Di seguito sono indicati altri modi generali per l'uso delle funzioni nelle espressioni:

| Attività | Sintassi della funzione in un'espressione | 
| ---- | -------------------------------- | 
| Eseguire operazioni con un elemento passandolo a una funzione. | "\@<*functionName*>(<*item*>)" | 
| 1. Ottenere il valore di *parameterName* usando la funzione `parameters()` annidata. </br>2. Eseguire operazioni con il risultato passando tale valore a *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1. Ottenere il risultato dalla funzione interna annidata *functionName*. </br>2. Passare il risultato alla funzione esterna *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1. Ottenere il risultato da *functionName*. </br>2. Dal momento che il risultato è un oggetto con proprietà *propertyName*, ottenere il valore della proprietà. | "\@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

Ad esempio, la funzione `concat()` può accettare due o più valori di stringa come parametri. Questa funzione combina queste stringhe in un'unica stringa. È possibile passare valori letterali di stringa, ad esempio, "Sophia" e "Owen", in modo da ottenere una stringa combinata, ovvero "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

In alternativa, è possibile ottenere i valori delle stringhe dai parametri. Questo esempio usa la funzione `parameters()` in ogni parametro `concat()` e i parametri `firstName` e `lastName`. Si passano quindi le stringhe risultanti alla funzione `concat()` in modo da ottenere una stringa combinata, ad esempio "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

In entrambi i casi, gli esempi assegnano il risultato alla proprietà `customerName`. 

Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).
In alternativa, vedere altre informazioni sulle funzioni in base al loro scopo generale.

<a name="string-functions"></a>

### <a name="string-functions"></a>Funzioni stringa

Per eseguire operazioni con le stringhe, è possibile usare queste funzioni di stringa e alcune [funzioni di raccolta](#collection-functions). Le funzioni di stringa funzionano solo sulle stringhe. 

| Funzione di stringa | Attività | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combina due o più stringhe e restituisce la stringa combinata. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifica se una stringa termina con la sottostringa specificata. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generare un identificatore univoco globale (GUID) sotto forma di stringa. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Restituisce la posizione iniziale di una sottostringa. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Restituisce la posizione finale di una sottostringa. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Sostituisce una sottostringa con la stringa specificata e restituisce la stringa aggiornata. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Restituisce una matrice che ha tutti i caratteri di una stringa e separa ogni carattere con il delimitatore specificato. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verifica se una stringa inizia con una sottostringa specifica. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Restituisce i caratteri di una stringa, partendo dalla posizione specificata. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Restituisce una stringa in formato minuscolo. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Restituisce una stringa in formato maiuscolo. | 
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Funzioni di raccolta

Per eseguire operazioni con le raccolte, generalmente matrici, stringhe e talvolta dizionari, è possibile usare queste funzioni di raccolta. 

| Funzione di raccolta | Attività | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifica se una raccolta include un elemento specifico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifica se una raccolta è vuota. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Restituisce il primo elemento di una raccolta. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Restituisce una stringa con *tutti* gli elementi di una matrice, separati dal carattere specificato. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Restituisce l'ultimo elemento di una raccolta. | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Restituisce il numero di elementi in una stringa o matrice. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Restituisce gli elementi dall'inizio di una raccolta. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Restituisce una raccolta che contiene *tutti* gli elementi delle raccolte specificate. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Funzioni di confronto

Per eseguire operazioni con le condizioni, confrontare i valori e i risultati delle espressioni o valutare vari tipi di logica, è possibile usare queste funzioni di confronto. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione di confronto | Attività | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifica se tutte le espressioni sono true. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifica se entrambi i valori sono equivalenti. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifica se il primo valore è maggiore del secondo valore. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifica se il primo valore è maggiore o uguale al secondo valore. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifica se il primo valore è minore del secondo valore. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifica se il primo valore è minore o uguale al secondo valore. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifica se un'espressione è false. | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifica se almeno un'espressione è true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Funzioni di conversione

Per modificare il tipo o il formato di un valore, è possibile usare queste funzioni di conversione. Ad esempio, è possibile modificare un valore da booleano a intero. Per informazioni sul modo in cui App per la logica gestisce i tipi di contenuto durante la conversione, vedere [Gestire i tipi di contenuti](../logic-apps/logic-apps-content-type.md). Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione di conversione | Attività | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Restituisce una matrice da un singolo input specificato. Per più input, vedere [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Restituisce la versione di una stringa con codifica base64. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Restituisce la versione binaria di una stringa con codifica base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Restituisce la versione stringa di una stringa con codifica base64. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Restituisce la versione binaria di un valore di input. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Restituisce la versione booleana di un valore di input. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Restituisce una matrice da più input. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Restituisce l'URI dati di un valore di input. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Restituisce la versione binaria di un URI dati. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Restituisce la versione stringa di un URI dati. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Restituisce la versione stringa di una stringa con codifica base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Restituisce la versione binaria di un URI dati. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Restituisce una stringa che sostituisce i caratteri non sicuri dell'URL con caratteri di escape. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Restituisce un numero a virgola mobile per un valore di input. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Restituisce la versione integer di una stringa. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Restituisce la versione stringa di un valore di input. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Restituisce la versione codificata in formato URI per un valore di input sostituendo i caratteri non sicuri dell'URL con caratteri di escape. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Restituisce la versione binaria di una stringa con codifica URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Restituisce la versione stringa di una stringa con codifica URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Restituisce la versione XML di una stringa. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Funzioni matematiche

Per eseguire operazioni con numeri interi o float, è possibile usare queste funzioni matematiche. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione matematica | Attività | 
| ------------- | ---- | 
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Restituisce il risultato della somma di due numeri. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Restituisce il risultato della divisione di due numeri. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Restituisce il valore più alto di un set di numeri o una matrice. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Restituisce il valore più basso di un set di numeri o una matrice. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Restituisce il resto della divisione di due numeri. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Restituisce il prodotto della moltiplicazione di due numeri. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Restituisce un numero intero casuale da un intervallo specificato. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Restituisce una matrice di valori interi che inizia da un valore intero specificato. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Restituisce il risultato della sottrazione del secondo numero dal primo. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funzioni di data e ora

Per eseguire operazioni con date e ore, è possibile usare queste funzioni di data e ora.
Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione di data e ora | Attività | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Aggiunge un numero di giorni a un timestamp. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Aggiunge un numero di ore a un timestamp. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Aggiunge un numero di minuti a un timestamp. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Aggiunge un numero di secondi a un timestamp. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Aggiunge un numero di unità di tempo a un timestamp. Vedere anche [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Restituisce il giorno del componente di mese di un timestamp. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Restituisce il giorno del componente settimana di un timestamp. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Restituisce il giorno del componente anno di un timestamp. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Restituisce la data di un timestamp. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Restituisce il timestamp corrente più le unità di tempo specificate. Vedere anche [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Restituisce il timestamp corrente meno le unità di tempo specificate. Vedere anche [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Restituisce l'inizio del giorno per un timestamp. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Restituisce l'inizio dell'ora per un timestamp. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Restituisce l'inizio del mese per un timestamp. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Sottrae un numero di unità di tempo da un timestamp. Vedere [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Restituisce il valore della proprietà `ticks` per un timestamp specificato. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Restituisce il timestamp corrente come stringa. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funzioni del flusso di lavoro

Queste funzioni del flusso di lavoro consentono di eseguire queste operazioni:

* Ottenere dettagli su un'istanza del flusso di lavoro al momento dell'esecuzione. 
* Eseguire operazioni con gli input usati per creare istanze di app per la logica.
* Fare riferimento agli output da azioni e trigger.

Ad esempio, è possibile fare riferimento agli output di un'azione e usare i dati in un'azione successiva. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione del flusso di lavoro | Attività | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Restituisce l'output dell'azione corrente in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Restituisce l'output `body` di un'azione in fase di esecuzione. Vedere anche [body](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Restituisce l'output di un'azione in fase di esecuzione. Vedere [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Restituisce l'output di un'azione in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione. Vedere anche [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Creare una matrice con i valori corrispondenti a un nome chiave negli ouput di azione *form-data* o *form-encoded*. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Restituisce un singolo valore che corrisponde a un nome chiave nell'output *form-data* o *form-encoded* di un'azione. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando si trova all'interno di un'azione ripetuta su una matrice, restituisce l'elemento corrente nella matrice durante l'iterazione corrente dell'azione. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando si trova all'interno di un ciclo for-each o do-until, restituisce l'elemento corrente dal ciclo specificato.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Restituisce l'URL callback che chiama un trigger o un'azione. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Restituisce il corpo per una parte specifica dell'output di un'azione con più parti. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Restituisce il valore per un parametro descritto nella definizione dell'app per la logica. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Restituisce l'output `body` di un trigger in fase di esecuzione. Vedere [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Restituisce un singolo valore corrispondente a un nome di chiave negli output *form-data* o *form-encoded* del trigger. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Restituisce il corpo di una parte specifica di un output a più parti di un trigger. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crea una matrice i cui valori corrispondono a un nome di chiave negli output *form-data* o *form-encoded* di un trigger. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Restituisce il valore per una variabile specificata. | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Restituisce tutti i dettagli del flusso di lavoro stesso in fase di esecuzione. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funzioni di analisi dell'URI

Per eseguire operazioni con URI (Uniform Resource Identifier) e ottenere vari valori di proprietà per questi URI, è possibile usare queste funzioni di analisi dell'URI. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione di analisi dell'URI | Attività | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Restituisce il valore `host` per un URI. | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Restituisce il valore `path` per un URI. | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Restituisce i valori `path` e `query` per un URI. | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Restituisce il valore `port` per un URI. | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Restituisce il valore `query` per un URI. | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Restituisce il valore `scheme` per un URI. | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funzioni JSON e XML

Per eseguire operazioni con gli oggetti JSON e i nodi XML, è possibile usare queste funzioni di manipolazione. Per informazioni dettagliate su ogni funzione, vedere l'[articolo di riferimento in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md).

| Funzione di manipolazione | Attività | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Aggiunge una proprietà e il suo valore o una coppia nome-valore a un oggetto JSON e restituisce l'oggetto aggiornato. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Restituisce il primo valore non Null da uno o più parametri. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Rimuove una proprietà da un oggetto JSON e restituisce l'oggetto aggiornato. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Imposta il valore per la proprietà di un oggetto JSON e restituisce l'oggetto aggiornato. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o i valori corrispondenti. | 
||| 

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Azioni e trigger del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informazioni sulla creazione e la gestione di app per la logica a livello di codice con l'[API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
