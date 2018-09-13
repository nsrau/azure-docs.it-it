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
ms.openlocfilehash: d5dadd054f95e61626942a1cab7d95ba8c9182e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42141396"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Schema del linguaggio di definizione del flusso di lavoro in App per la logica di Azure - Informazioni di riferimento

Quando si crea un flusso di lavoro di app per la logica utilizzando [App per la logica di Azure](../logic-apps/logic-apps-overview.md), la definizione alla base del flusso di lavoro descrive la logica effettiva eseguita per l'app per la logica. Questa descrizione segue una struttura definita e convalidata dallo schema del linguaggio di definizione del flusso di lavoro che usa [JSON (JavaScript Object Notation)](https://www.json.org/). 
  
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

Alcune espressioni ottengono i rispettivi valori da azioni di runtime che potrebbero non esistere all'inizio dell'esecuzione di un'app per la logica. Per usare questi valori o farvi riferimento nelle espressioni, è possibile usare le [funzioni*fornite dal*Linguaggio di definizione del flusso di lavoro](../logic-apps/workflow-definition-language-functions-reference.md). 

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Azioni e trigger del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informazioni sulla creazione e la gestione di app per la logica a livello di codice con l'[API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
