---
title: Riferimento dello schema per il linguaggio di definizione del flusso di lavoro - App per la logica di Azure
description: Guida di riferimento per lo schema del linguaggio di definizione del flusso di lavoro nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 3b0ad33ea6348f24079b3c88f972437244c0bc93
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596753"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Schema del linguaggio di definizione del flusso di lavoro in App per la logica di Azure - Informazioni di riferimento

Quando si crea un'app per la logica nel [Azure Logic Apps](../logic-apps/logic-apps-overview.md), app per la logica ha una definizione del flusso di lavoro sottostante che descrive la logica effettiva che viene eseguito in app per la logica. Tale definizione del flusso di lavoro viene utilizzata [JSON](https://www.json.org/) e segue una struttura che viene convalidata dallo schema del linguaggio di definizione del flusso di lavoro. Questo riferimento fornisce una panoramica di questa struttura e il modo in cui lo schema definisce gli attributi nella definizione del flusso di lavoro.

## <a name="workflow-definition-structure"></a>Struttura della definizione del flusso di lavoro

Sempre una definizione del flusso di lavoro include un trigger per creare un'istanza di app per la logica, oltre a uno o più azioni da eseguire dopo l'attivazione del trigger.

Di seguito è riportata la struttura generale di una definizione del flusso di lavoro:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attributo | Obbligatorio | Descrizione |
|-----------|----------|-------------|
| `definition` | Yes | Elemento iniziale della definizione del flusso di lavoro |
| `$schema` | Solo quando si fa riferimento esternamente a una definizione del flusso di lavoro | Percorso del file di schema JSON che descrive la versione del linguaggio di definizione del flusso di lavoro, disponibile qui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | Le definizioni per uno o più azioni da eseguire in fase di esecuzione del flusso di lavoro. Per altre informazioni, vedere [trigger e azioni](#triggers-actions). <p><p>Numero massimo di azioni: 250 |
| `contentVersion` | No | Numero di versione della definizione del flusso di lavoro, per impostazione predefinita "1.0.0.0". Specificare un valore da usare per identificare e confermare la definizione corretta durante la distribuzione di un flusso di lavoro. |
| `outputs` | No | Le definizioni per gli output che restituiscono un flusso di lavoro. Per altre informazioni, vedere [output](#outputs). <p><p>Numero massimo di output: 10 |
| `parameters` | No | Le definizioni per uno o più parametri che passare i dati nel flusso di lavoro. Per altre informazioni, vedere [parametri](#parameters). <p><p>Numero massimo di parametri: 50 |
| `staticResults` | No | Le definizioni per uno o più risultati statici restituiti dalle azioni come output fittizio quando risultati statici sono abilitati su tali azioni. Nella definizione di ogni azione, il `runtimeConfiguration.staticResult.name` attributo fa riferimento la definizione corrispondente all'interno di `staticResults`. Per altre informazioni, vedere [risultati statici](#static-results). |
| `triggers` | No | Definizioni di uno o più trigger che creano istanze del flusso di lavoro. È possibile definire più di un trigger, ma solo con il linguaggio di definizione del flusso di lavoro, non in modo visivo tramite Progettazione app per la logica. Per altre informazioni, vedere [trigger e azioni](#triggers-actions). <p><p>Numero massimo di trigger: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Trigger e azioni

In una definizione del flusso di lavoro, le sezioni `triggers` e `actions` definiscono le chiamate che si verificano durante l'esecuzione del flusso di lavoro. Per la sintassi e altre informazioni su queste sezioni, vedere [Trigger e azioni del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="outputs"></a>

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

| Attributo | Obbligatorio | Type | Descrizione |
|-----------|----------|------|-------------|
| <*key-name*> | Yes | String | Valore chiave del valore di output restituito |
| <*key-type*> | Yes | int, float, string, securestring, bool, array, JSON object | Tipo di valore di output restituito |
| <*key-value*> | Yes | Uguale a < *-tipo di chiave*> | Valore di output restituito |
|||||

Per ottenere l'output da un flusso di lavoro, esaminare la cronologia di esecuzione dell'app per la logica e i dettagli nel portale di Azure o usare il [API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows). È anche possibile passare l'output a sistemi esterni, ad esempio Power BI, per creare dashboard.

<a name="parameters"></a>

## <a name="parameters"></a>Parametri

Nel `parameters` sezione, definire tutti i parametri del flusso di lavoro che usa la definizione del flusso di lavoro in fase di distribuzione per accettare l'input. Sia le dichiarazioni che i valori dei parametri sono necessari durante l'installazione. Prima di poter usare questi parametri in altre sezioni del flusso di lavoro, assicurarsi di aver dichiarato tutti i parametri in queste sezioni. 

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

| Attributo | Obbligatorio | Type | Descrizione |
|-----------|----------|------|-------------|
| <*tipo di parametro*> | Yes | int, float, string, securestring, bool, array, JSON object, secureobject <p><p>**Nota**: Per tutte le password, le chiavi e i segreti, usare i tipi `securestring` e `secureobject` perché l'operazione `GET` non restituisce questi tipi. Per altre informazioni sulla protezione dei parametri, vedere [proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Tipo di parametro |
| <*default-parameter-values*> | Yes | Uguale a `type` | Valore del parametro predefinito quando non viene specificato alcun valore durante la creazione dell'istanza del flusso di lavoro |
| <*array-with-permitted-parameter-values*> | No | Array | Matrice con valori che il parametro può accettare |
| `metadata` | No | Oggetto JSON | Qualsiasi altro parametro informazioni dettagliate, ad esempio, il nome o una descrizione leggibile per app per la logica o flusso di dati design-time usati da Visual Studio o altri strumenti |
||||

<a name="static-results"></a>

## <a name="static-results"></a>Risultati statici

Nel `staticResults` dell'attributo, definire simulazione di un'azione `outputs` e `status` che l'azione restituisce quando è attivata l'impostazione del risultato statico dell'azione. Nella definizione dell'azione, il `runtimeConfiguration.staticResult.name` attributo fa riferimento al nome per la definizione del risultato statico all'interno di `staticResults`. Informazioni su come è possibile [testare le App per la logica con dati fittizi configurando risultati statici](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attributo | Obbligatorio | Type | Descrizione |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | Yes | String | Il nome per una definizione del risultato statico che può fare riferimento a una definizione di azione tramite un `runtimeConfiguration.staticResult` oggetto. Per altre informazioni vedere [Impostazioni di configurazione di runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>È possibile usare qualsiasi nome univoco che si desidera. Per impostazione predefinita, questo nome univoco viene aggiunto con un numero che viene incrementato. |
| <*output-attributes-and-values-returned*> | Yes | Variabile | I requisiti per questi attributi variano in base a condizioni diverse. Ad esempio, quando la `status` viene `Succeeded`, il `outputs` attributo include gli attributi e valori restituiti gli output di esempio fittizi dall'azione. Se il `status` viene `Failed`, il `outputs` attributo include il `errors` attributo, ovvero una matrice con uno o più errori `message` oggetti con le informazioni sull'errore. |
| <*header-values*> | No | JSON | I valori di intestazione restituiti dall'azione |
| <*status-code-returned*> | Yes | String | Il codice di stato restituito dall'azione |
| <*action-status*> | Yes | String | Lo stato dell'azione, ad esempio, `Succeeded` o `Failed` |
|||||

Ad esempio, in questa definizione di azione HTTP, il `runtimeConfiguration.staticResult.name` i riferimenti dell'attributo `HTTP0` all'interno di `staticResults` attributo in cui vengono definiti gli output fittizi per l'azione. Il `runtimeConfiguration.staticResult.staticResultOptions` attributo specifica che l'impostazione del risultato statico è `Enabled` dell'azione HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

L'azione HTTP restituisce gli output nel `HTTP0` definizione all'interno di `staticResults`. In questo esempio, per il codice di stato, l'output fittizio è `OK`. Per i valori di intestazione, l'output fittizio è `"Content-Type": "application/JSON"`. Per lo stato dell'azione, l'output fittizio è `Succeeded`.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

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
| "Sophia Owen" | Restituisce questi caratteri: "Sophia Owen" |
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
| "My age is \@\@{parameters('myAge')}" | Restituisce questa stringa, che include l'espressione: "My age is \@{parameters('myAge')}" |
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

| Operator | Attività |
|----------|------|
| ' | Per usare un valore letterale di stringa come input o in espressioni e funzioni, racchiudere la stringa solo tra virgolette singole, ad esempio `'<myString>'`. Non usare le virgolette doppie (""), che sono in conflitto con la formattazione JSON per racchiudere un'espressione intera. Ad esempio: <p>**Yes**: length('Hello') </br>**No**: length("Hello") <p>Quando si passano matrici o numeri, non è necessario il wrapping della punteggiatura. Ad esempio: <p>**Yes**: length([1, 2, 3]) </br>**No**: length("[1, 2, 3]") |
| [] | Per fare riferimento a un valore in una posizione specifica (indice) di una matrice, usare le parentesi quadre. Ad esempio, per ottenere il secondo elemento in una matrice: <p>`myArray[1]` |
| . | Per fare riferimento a una proprietà in un oggetto, usare l'operatore punto. Ad esempio, per ottenere la proprietà `name` di un oggetto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Per fare riferimento alle proprietà Null di un oggetto senza un errore di runtime, usare l'operatore punto interrogativo. È ad esempio possibile usare questa espressione per gestire gli output di trigger Null: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funzioni

Alcune espressioni ottengono i rispettivi valori dalle azioni di runtime che potrebbero non esistere ancora quando la definizione del flusso di lavoro viene avviato per l'esecuzione. Per usare questi valori o farvi riferimento nelle espressioni, è possibile usare le [funzioni*fornite dal*Linguaggio di definizione del flusso di lavoro](../logic-apps/workflow-definition-language-functions-reference.md).

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Azioni e trigger del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informazioni sulla creazione e la gestione di app per la logica a livello di codice con l'[API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
