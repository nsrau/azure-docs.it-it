---
title: Funzioni del linguaggio di definizione del flusso di lavoro - App per la logica di Azure | Microsoft Docs
description: Informazioni sulle funzioni che è possibile usare nelle definizioni del flusso di lavoro delle app per la logica
services: logic-apps
author: ecfan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 0155e35641a0407fe48c4da07400fa188152b0af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182251"
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Informazioni di riferimento sulle funzioni del linguaggio di definizione del flusso di lavoro per App per la logica di Azure

Questo articolo descrive le funzioni che è possibile usare quando si creano flussi di lavoro con [App per la logica di Azure](../logic-apps/logic-apps-overview.md). Per altre informazioni sulle definizioni delle app per la logica, vedere [Linguaggio di definizione del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Nella sintassi per le definizioni dei parametri, un punto interrogativo (?) visualizzato dopo un parametro indica che il parametro è facoltativo. Vedere, ad esempio, [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>action

Restituisce l'output dell'azione *corrente* in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione. Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto azione, ma è possibile specificare una proprietà con il valore desiderato. Vedere anche [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

È possibile usare la funzione `action()` solo in queste posizioni: 

* Proprietà `unsubscribe` per un'azione webhook, in modo che sia possibile accedere al risultato dalla richiesta `subscribe` originale
* Proprietà `trackedProperties` per un'azione
* Condizione di ciclo `do-until` per un'azione

```
action()
action().outputs.body.<property> 
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | No  | string | Nome della proprietà dell'oggetto azione con il valore desiderato: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. Nel portale di Azure è possibile trovare queste proprietà esaminando i dettagli di una cronologia di esecuzione specifica. Per altre informazioni, vedere [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get) (API REST - Azioni di esecuzione del flusso di lavoro). | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | -----| ----------- | 
| <*action-output*> | string | Output della proprietà o dell'azione corrente | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione. Sintassi abbreviata per `actions('<actionName>').outputs.body`. Vedere [body()](#body) e [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Nome per l'oggetto `body` di output dell'azione desiderato | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | -----| ----------- | 
| <*action-body-output*> | string | Oggetto `body` di output dell'azione specificata | 
|||| 

*Esempio*

Questo esempio ottiene l'oggetto `body` di output dell'azione Twitter `Get user`: 

```
actionBody('Get_user')
```

E viene restituito questo risultato:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

## <a name="actionoutputs"></a>actionOutputs

Restituisce l'output di un'azione in fase di esecuzione. Sintassi abbreviata per `actions('<actionName>').outputs`. Vedere [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Nome per l'output dell'azione desiderato | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | -----| ----------- | 
| <*output*> | string | Output dell'azione specificata | 
|||| 

*Esempio*

Questo esempio ottiene l'output dell'azione Twitter `Get user`: 

```
actionOutputs('Get_user')
```

E viene restituito questo risultato:

```json
{ 
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

## <a name="actions"></a>Azioni

Restituisce l'output di un'azione in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione. Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto azione, ma è possibile specificare una proprietà con il valore desiderato. Per le versioni a sintassi abbreviata, vedere [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) e [body()](#body). Per l'azione corrente, vedere [action()](#action).

> [!NOTE] 
> In precedenza, era possibile usare la funzione `actions()` o l'elemento `conditions` per specificare che un'azione veniva eseguita in base all'output di un'altra azione. Tuttavia, per dichiarare in modo esplicito le dipendenze tra le azioni, ora è necessario usare la proprietà `runAfter` dell'azione dipendente. Per altre informazioni sulla proprietà `runAfter`, vedere [Rilevare e gestire gli errori con la proprietà RunAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Nome per l'oggetto azione di cui si vuole ottenere l'output  | 
| <*property*> | No  | string | Nome della proprietà dell'oggetto azione con il valore desiderato: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. Nel portale di Azure è possibile trovare queste proprietà esaminando i dettagli di una cronologia di esecuzione specifica. Per altre informazioni, vedere [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get) (API REST - Azioni di esecuzione del flusso di lavoro). | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | -----| ----------- | 
| <*action-output*> | string | Output dell'azione o della proprietà specificata | 
|||| 

*Esempio*

Questo esempio ottiene il valore della proprietà `status` dall'azione Twitter `Get user` in fase di esecuzione: 

```
actions('Get_user').outputs.body.status 
```

E viene restituito questo risultato: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>add

Restituisce il risultato della somma di due numeri.

```
add(<summand_1>, <summand_2>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Sì | Integer, float o misto | Numeri da sommare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="adddays"></a>addDays

Aggiunge un numero di giorni a un timestamp.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*days*> | Sì | Integer | Numero positivo o negativo di giorni da aggiungere | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di giorni specificato  | 
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

## <a name="addhours"></a>addHours

Aggiunge un numero di ore a un timestamp.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*hours*> | Sì | Integer | Numero positivo o negativo di ore da aggiungere | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di ore specificato  | 
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

## <a name="addminutes"></a>addMinutes

Aggiunge un numero di minuti a un timestamp.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*minutes*> | Sì | Integer | Numero positivo o negativo di minuti da aggiungere | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di minuti specificato | 
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

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

Aggiunge una proprietà e il suo valore o una coppia nome-valore a un oggetto JSON e restituisce l'oggetto aggiornato. Se l'oggetto esiste già in fase di esecuzione, la funzione genera un errore.

```
addProperty(<object>, '<property>', <value>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Sì | Oggetto | Oggetto JSON a cui si vuole aggiungere una proprietà | 
| <*property*> | Sì | string | Nome della proprietà da aggiungere | 
| <*value*> | Sì | Qualsiasi | Valore per la proprietà |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato con la proprietà specificata | 
|||| 

*Esempio*

Questo esempio aggiunge la proprietà `accountNumber` all'oggetto `customerProfile`, di cui viene eseguita la conversione in JSON con la funzione [JSON()](#json). La funzione assegna un valore generato dalla funzione [guid()](#guid) e restituisce l'oggetto aggiornato:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>addSeconds

Aggiunge un numero di secondi a un timestamp.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*seconds*> | Sì | Integer | Numero positivo o negativo di secondi da aggiungere | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di secondi specificato  | 
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

## <a name="addtotime"></a>addToTime

Aggiunge un numero di unità di tempo a un timestamp. Vedere anche [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da aggiungere | 
| <*timeUnit*> | Sì | string | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di unità di tempo specificato  | 
|||| 

*Esempio 1*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

E viene restituito questo risultato: `"2018-01-02T00:00:00:0000000Z"`

*Esempio 2*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E restituisce il risultato usando il formato "D" facoltativo: `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>e

Verifica se tutte le espressioni sono true. Restituisce true se tutte le espressioni sono true o false se almeno un'espressione è false.

```
and(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>, ... | Sì | boolean | Espressioni da verificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="array"></a>array

Restituisce una matrice da un singolo input specificato. Per più input, vedere [createArray()](#createArray). 

```
array('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa per la creazione di una matrice | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="base64"></a>base64

Restituisce la versione di una stringa con codifica base64.

```
base64('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa di input | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*base64-string*> | string | Versione della stringa di input con codifica base64 | 
|||| 

*Esempio*

Questo esempio converte la stringa "hello" in una stringa con codifica base64:

```
base64('hello')
```

E viene restituito questo risultato: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Restituisce la versione binaria di una stringa con codifica base64.

```
base64ToBinary('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con codifica base64 da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*binary-for-base64-string*> | string | Versione binaria della stringa con codifica base64 | 
|||| 

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una stringa binaria:

```
base64ToBinary('aGVsbG8=')
```

E viene restituito questo risultato: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Restituisce la versione stringa di una stringa con codifica base64, decodificando in modo efficace la stringa base64. Usare questa funzione al posto di [decodeBase64()](#decodeBase64). Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `base64ToString()`.

```
base64ToString('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con codifica base64 da decodificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*decoded-base64-string*> | string | Versione stringa di una stringa con codifica base64 | 
|||| 

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una semplice stringa:

```
base64ToString('aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>binary 

Restituisce la versione binaria di una stringa.

```
binary('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*binary-for-input-value*> | string | Versione binaria della stringa specificata | 
|||| 

*Esempio*

Questo esempio converte la stringa "hello" in una stringa binaria:

```
binary('hello')
```

E viene restituito questo risultato: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>Corpo

Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione. Sintassi abbreviata per `actions('<actionName>').outputs.body`. Vedere [actionBody()](#actionBody) e [actions()](#actions).

```
body('<actionName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Nome per l'oggetto `body` di output dell'azione desiderato | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | -----| ----------- | 
| <*action-body-output*> | string | Oggetto `body` di output dell'azione specificata | 
|||| 

*Esempio*

Questo esempio ottiene l'oggetto `body` di output dell'azione Twitter `Get user`: 

```
body('Get_user')
```

E viene restituito questo risultato: 

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

## <a name="bool"></a>bool

Restituisce la versione booleana di un valore.

```
bool(<value>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Qualsiasi | Valore da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="coalesce"></a>coalesce

Restituisce il primo valore non Null da uno o più parametri. Stringhe vuote, matrici vuote e oggetti vuoti sono non null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>, ... | Sì | Qualsiasi, è possibile una combinazione di tipi | Uno o più elementi da verificare per determinare se sono Null | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="concat"></a>concat

Combina due o più stringhe e restituisce la stringa combinata. 

```
concat('<text1>', '<text2>', ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text1*>, <*text2*>, ... | Sì | string | Almeno due stringhe da combinare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | string | Stringa creata dalle stringhe di input combinate | 
|||| 

*Esempio*

Questo esempio combina le stringhe "Hello" e "World":

```
concat('Hello', 'World')
```

E viene restituito questo risultato: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>contains

Verifica se una raccolta include un elemento specifico. Restituisce true se l'elemento viene trovato o false se non viene trovato. Questa funzione fa distinzione tra maiuscole e minuscole.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

In particolare, questa funzione può essere usata con questi tipi di raccolta: 

* Una *stringa* per trovare una *sottostringa*
* Una *matrice* per trovare un *valore*
* Un *dizionario* per trovare una *chiave*

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String, array o dictionary | Raccolta da verificare | 
| <*value*> | Sì | String, array o dictionary, rispettivamente | Elemento da trovare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="convertfromutc"></a>convertFromUtc

Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*destinationTimeZone*> | Sì | string | Nome del fuso orario di destinazione. Per altre informazioni, vedere [Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)) (ID dei fusi orari). | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | string | Timestamp convertito nel fuso orario di destinazione | 
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

## <a name="converttimezone"></a>convertTimeZone

Converte un timestamp dal fuso orario di origine al fuso orario di destinazione.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*sourceTimeZone*> | Sì | string | Nome del fuso orario di origine. Per altre informazioni, vedere [Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)) (ID dei fusi orari). | 
| <*destinationTimeZone*> | Sì | string | Nome del fuso orario di destinazione. Per altre informazioni, vedere [Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)) (ID dei fusi orari). | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | string | Timestamp convertito nel fuso orario di destinazione | 
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

## <a name="converttoutc"></a>convertToUtc

Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*sourceTimeZone*> | Sì | string | Nome del fuso orario di origine. Per altre informazioni, vedere [Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)) (ID dei fusi orari). | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | string | Timestamp convertito nell'ora UTC | 
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

## <a name="createarray"></a>createArray

Restituisce una matrice da più input. Per le matrici con input singolo, vedere [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*>, ... | Sì | Qualsiasi, ma non sono possibili combinazioni di tipi | Almeno due elementi per creare la matrice | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="datauri"></a>dataUri

Restituisce un URI (Uniform Resource Identifier) di dati per una stringa. 

```
dataUri('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*data-uri*> | string | URI di dati per la stringa di input | 
|||| 

*Esempio*

Questo esempio crea un URI di dati per la stringa "hello":

```
dataUri('hello') 
```

E viene restituito questo risultato: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati. Usare questa funzione al posto di [decodeDataUri()](#decodeDataUri). Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `decodeDataUri()`.

```
dataUriToBinary('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | URI di dati da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*binary-for-data-uri*> | string | Versione binaria dell'URI di dati | 
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

## <a name="datauritostring"></a>dataUriToString

Restituisce la versione stringa di un URI (Uniform Resource Identifier) di dati.

```
dataUriToString('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | URI di dati da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*string-for-data-uri*> | string | Versione stringa dell'URI di dati | 
|||| 

*Esempio*

Questo esempio crea una stringa per questo URI di dati:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>dayOfMonth

Restituisce il giorno del mese da un timestamp. 

```
dayOfMonth('<timestamp>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="dayofweek"></a>dayOfWeek

Restituisce il giorno della settimana da un timestamp.  

```
dayOfWeek('<timestamp>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="dayofyear"></a>dayOfYear

Restituisce il giorno dell'anno da un timestamp. 

```
dayOfYear('<timestamp>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="decodebase64"></a>decodeBase64

Restituisce la versione stringa di una stringa con codifica base64, decodificando in modo efficace la stringa base64. Usare [base64ToString()](#base64ToString) al posto di `decodeBase64()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `base64ToString()`.

```
decodeBase64('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con codifica base64 da decodificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*decoded-base64-string*> | string | Versione stringa di una stringa con codifica base64 | 
|||| 

*Esempio*

Questo esempio crea una stringa per una stringa con codifica base64:

```
decodeBase64('aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati. Usare [dataUriToBinary()](#dataUriToBinary) al posto di `decodeDataUri()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `dataUriToBinary()`.

```
decodeDataUri('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa dell'URI di dati da decodificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*binary-for-data-uri*> | string | Versione binaria di una stringa di URI di dati | 
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

## <a name="decodeuricomponent"></a>decodeUriComponent

Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate. 

```
decodeUriComponent('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con i caratteri di escape da decodificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*decoded-uri*> | string | Stringa aggiornata con i caratteri di escape decodificati | 
|||| 

*Esempio*

Questo esempio sostituisce i caratteri di escape in questa stringa con le versioni decodificate:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

Restituisce il risultato intero della divisione di due numeri. Per ottenere il resto, vedere [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | Sì | Integer o float | Numero da dividere per l'oggetto *divisor* | 
| <*divisor*> | Sì | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0 | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="encodeuricomponent"></a>encodeUriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape. Usare [uriComponent()](#uriComponent) al posto di `encodeUriComponent()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

```
encodeUriComponent('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa da convertire nel formato con codifica URI | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*encoded-uri*> | string | Stringa con codifica URI con i caratteri di escape | 
|||| 

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
encodeUriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>empty

Verifica se una raccolta è vuota. Restituisce true se la raccolta è vuota o false se non lo è.

```
empty('<collection>')
empty([<collection>])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String, array o object | Raccolta da verificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="endswith"></a>endsWith

Verifica se una stringa termina con una sottostringa specifica. Restituisce true se la sottostringa viene trovata o false se non viene trovata. Questa funzione non fa distinzione tra maiuscole e minuscole.

```
endsWith('<text>', '<searchText>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da verificare | 
| <*searchText*> | Sì | string | Sottostringa finale da trovare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="equals"></a>equals

Verifica se entrambi i valori, le espressioni o gli oggetti sono equivalenti. Restituisce true se entrambi gli elementi sono equivalenti o false se non lo sono.

```
equals('<object1>', '<object2>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*> | Sì | Vari | Valori, espressioni o oggetti da confrontare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="first"></a>first

Restituisce il primo elemento di una stringa o una matrice.

```
first('<collection>')
first([<collection>])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String o array | Raccolta in cui trovare il primo elemento |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*first-collection-item*> | Qualsiasi | Primo elemento nella raccolta | 
|||| 

*Esempio*

Questi esempi trovano il primo elemento in queste raccolte:

```
first('hello')
first([0, 1, 2])
```

E vengono restituiti questi risultati: 

* Primo esempio: `"h"`
* Secondo esempio: `0`

<a name="float"></a>

## <a name="float"></a>float

Converte una versione stringa per un numero a virgola mobile in un numero a virgola mobile effettivo. È possibile usare questa funzione solo quando si passano parametri personalizzati a un'app, ad esempio un'app per la logica.

```
float('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con un numero a virgola mobile valido da convertire |
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="formatdatetime"></a>formatDateTime

Restituisce un timestamp nel formato specificato.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*reformatted-timestamp*> | string | Timestamp aggiornato nel formato specificato | 
|||| 

*Esempio*

Questo esempio converte un timestamp nel formato specificato:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E viene restituito questo risultato: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Restituisce una matrice con i valori corrispondenti a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un'azione. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Azione il cui output include il valore della chiave desiderato | 
| <*key*> | Sì | string | Nome della chiave di cui si vuole ottenere il valore | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | Array | Matrice con tutti i valori che corrispondono alla chiave specificata | 
|||| 

*Esempio* 

Questo esempio crea una matrice dal valore della chiave "Subject" nell'output di dati del modulo o codifica per il modulo dell'azione specificata:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

E restituisce il testo dell'oggetto in una matrice, ad esempio: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Restituisce un singolo valore corrispondente a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un'azione. Se la funzione trova più corrispondenze, genera un errore.

```
formDataValue('<actionName>', '<key>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Azione il cui output include il valore della chiave desiderato | 
| <*key*> | Sì | string | Nome della chiave di cui si vuole ottenere il valore |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*key-value*> | string | Valore nella chiave specificata  | 
|||| 

*Esempio* 

Questo esempio crea una stringa dal valore della chiave "Subject" nell'output di dati del modulo o codifica per il modulo dell'azione specificata:  

```
formDataValue('Send_an_email', 'Subject')
```

E restituisce il testo dell'oggetto come stringa, ad esempio: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Restituisce il timestamp corrente più le unità di tempo specificate.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da sottrarre | 
| <*timeUnit*> | Sì | string | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp corrente con l'aggiunta del numero di unità di tempo specificato | 
|||| 

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z". Questo esempio aggiunge cinque giorni al timestamp:

```
getFutureTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-03-06T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z". Questo esempio aggiunge cinque giorni e converte il risultato nel formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Restituisce il timestamp corrente meno le unità di tempo specificate.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da sottrarre | 
| <*timeUnit*> | Sì | string | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp corrente meno il numero di unità di tempo specificato | 
|||| 

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z". Questo esempio sottrae cinque giorni dal timestamp:

```
getPastTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-01-27T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z". Questo esempio sottrae cinque giorni e converte il risultato nel formato "D":

```
getPastTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>greater

Verifica se il primo valore è maggiore del secondo valore. Restituisce true se il primo valore è maggiore o false se è minore.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è maggiore del secondo | 
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Valore di confronto | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="greaterorequals"></a>greaterOrEquals

Verifica se il primo valore è maggiore o uguale al secondo valore.
Restituisce true se il primo valore è maggiore o uguale o false se è minore.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è maggiore o uguale al secondo | 
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Valore di confronto | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="guid"></a>GUID

Genera un identificatore univoco globale (GUID) sotto forma di stringa, ad esempio "c2ecc88d-88c8-4096-912c-d6f2e2b138ce": 

```
guid()
```

È anche possibile specificare un formato per il GUID diverso da quello predefinito, "D", che è costituito da 32 cifre separate da trattini.

```
guid('<format>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | No  | string | [Identificatore di formato](https://msdn.microsoft.com/library/97af8hh4) singolo per il GUID restituito. Per impostazione predefinita, il formato è "D", ma è possibile usare "N", "D", "B", "P" o "X". | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*GUID-value*> | string | GUID generato in modo casuale | 
|||| 

*Esempio* 

Questo esempio genera lo stesso GUID, ma in formato a 32 cifre, separate da trattini e racchiuse tra parentesi: 

```
guid('P')
```

E viene restituito questo risultato: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>if

Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | Sì | boolean | Espressione da verificare | 
| <*valueIfTrue*> | Sì | Qualsiasi | Valore da restituire se l'espressione è true | 
| <*valueIfFalse*> | Sì | Qualsiasi | Valore da restituire se l'espressione è false | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*specified-return-value*> | Qualsiasi | Valore specificato restituito a seconda del fatto che l'espressione sia true o false | 
|||| 

*Esempio* 

Questo esempio restituisce `"yes"` perché l'espressione specificata restituisce true. In caso contrario, l'esempio restituisce `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Restituisce la posizione iniziale o il valore di indice di una sottostringa. Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0. 

```
indexOf('<text>', '<searchText>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa che contiene la sottostringa da trovare | 
| <*searchText*> | Sì | string | Sottostringa da trovare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*index-value*>| Integer | Posizione iniziale o valore di indice per la sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. </br>Se la stringa è vuota, viene restituito 0. | 
|||| 

*Esempio* 

Questo esempio trova il valore di indice iniziale della sottostringa "world" nella stringa "hello world":

```
indexOf('hello world', 'world')
```

E viene restituito questo risultato: `6`

<a name="int"></a>

## <a name="int"></a>int

Restituisce la versione integer di una stringa.

```
int('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*integer-result*> | Integer | Versione integer della stringa specificata | 
|||| 

*Esempio* 

Questo esempio crea una versione integer per la stringa "10":

```
int('10')
```

E viene restituito questo risultato: `10`

<a name="item"></a>

## <a name="item"></a>item

Quando la funzione viene usata all'interno di un'azione ripetuta su una matrice, restituisce l'elemento corrente nella matrice durante l'iterazione corrente dell'azione. È anche possibile ottenere i valori dalle proprietà dell'elemento. 

```
item()
```

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*current-array-item*> | Qualsiasi | Elemento corrente nella matrice per l'iterazione corrente dell'azione | 
|||| 

*Esempio* 

Questo esempio ottiene l'elemento `body` dal messaggio corrente per l'azione "Send_an_email" all'interno dell'iterazione corrente di un ciclo for-each:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>items

Restituisce l'elemento corrente da ogni ciclo in un ciclo for-each. Usare questa funzione all'interno del ciclo for-each.

```
items('<loopName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sì | string | Nome del ciclo for-each | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*item*> | Qualsiasi | Elemento del ciclo corrente nel ciclo for-each specificato | 
|||| 

*Esempio* 

Questo esempio ottiene l'elemento corrente dal ciclo for-each specificato:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>json

Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML.

```
json('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Stringa o elemento XML | Stringa o elemento XML da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="intersection"></a>intersezione

Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate. Per essere incluso nel risultato, un elemento deve essere presente in tutte le raccolte passate alla funzione. Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>, ... | Sì | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *solo* gli elementi comuni | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*common-items*> | Array o object, rispettivamente | Raccolta che contiene solo gli elementi comuni alle raccolte specificate | 
|||| 

*Esempio* 

Questo esempio trova gli elementi comuni tra queste matrici:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

E restituisce una matrice con *solo* questi elementi: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Restituisce una stringa con tutti gli elementi di una matrice, in cui ogni carattere è separato da un *delimitatore*.

```
join([<collection>], '<delimiter>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | Array | Matrice contenente gli elementi da aggiungere |  
| <*delimiter*> | Sì | string | Separatore visualizzato tra ogni carattere nella stringa risultante | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*char1*><*delimiter*><*char2*><*delimiter*>... | string | Stringa risultante creata da tutti gli elementi nella matrice specificata |
|||| 

*Esempio* 

Questo esempio crea una stringa da tutti gli elementi in questa matrice usando il carattere specificato come delimitatore:

```
join([a, b, c], '.')
```

E viene restituito questo risultato: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>last

Restituisce l'ultimo elemento di una raccolta.

```
last('<collection>')
last([<collection>])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String o array | Raccolta dove trovare l'ultimo elemento | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*last-collection-item*> | String o array, rispettivamente | Ultimo elemento nella raccolta | 
|||| 

*Esempio* 

Questi esempi trovano l'ultimo elemento in queste raccolte:

```
last('abcd')
last([0, 1, 2, 3])
```

E vengono restituiti questi risultati: 

* Primo esempio: `"d"`
* Secondo esempio: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Restituisce la posizione finale o il valore di indice di una sottostringa. Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa che contiene la sottostringa da trovare | 
| <*searchText*> | Sì | string | Sottostringa da trovare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*ending-index-value*> | Integer | Posizione finale o valore di indice per la sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. </br>Se la stringa è vuota, viene restituito 0. | 
|||| 

*Esempio* 

Questo esempio trova il valore di indice finale della sottostringa "world" nella stringa "hello world":

```
lastIndexOf('hello world', 'world')
```

E viene restituito questo risultato: `10`

<a name="length"></a>

## <a name="length"></a>length

Restituisce il numero di elementi in una raccolta.

```
length('<collection>')
length([<collection>])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String o array | Raccolta con gli elementi da contare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*length-or-count*> | Integer | Numero di elementi nella raccolta | 
|||| 

*Esempio*

Questi esempi contano il numero di elementi in queste raccolte: 

```
length('abcd')
length([0, 1, 2, 3])
```

E viene restituito questo risultato: `4`

<a name="less"></a>

## <a name="less"></a>less

Verifica se il primo valore è minore del secondo valore.
Restituisce true se il primo valore è minore o false se è maggiore.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è minore del secondo | 
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Elemento di confronto | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="lessorequals"></a>lessOrEquals

Verifica se il primo valore è minore o uguale al secondo valore.
Restituisce true se il primo valore è minore o uguale o false se è maggiore.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è minore o uguale al secondo | 
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Elemento di confronto | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Restituisce l'"URL di callback" che chiama un trigger o un'azione. Questa funzione può essere usata solo con i trigger e le azioni per i tipi di connettore **HttpWebhook** e **ApiConnectionWebhook**, ma non con i tipi **Manual**, **Recurrence**, **HTTP** e **APIConnection**. 

```
listCallbackUrl()
```

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*callback-URL*> | string | URL di callback per un trigger o un'azione |  
|||| 

*Esempio*

Questo esempio mostra un URL di callback di esempio che questa funzione potrebbe restituire:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

Restituisce il valore più alto di un elenco o una matrice con numeri che includono gli estremi. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>, ... | Sì | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più alto | 
| [<*number1*>, <*number2*>, ...] | Sì | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più alto | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*max-value*> | Integer o float | Valore più alto nella matrice o nel set di numeri specificato | 
|||| 

*Esempio* 

Questi esempi ottengono il valore più alto dal set di numeri e dalla matrice:

```
max(1, 2, 3)
max([1, 2, 3])
```

E viene restituito questo risultato: `3`

<a name="min"></a>

## <a name="min"></a>Min

Restituisce il valore più basso di un set di numeri o una matrice.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>, ... | Sì | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più basso | 
| [<*number1*>, <*number2*>, ...] | Sì | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più basso | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*min-value*> | Integer o float | Valore più basso nella matrice o nel set di numeri specificato | 
|||| 

*Esempio* 

Questi esempi ottengono il valore più basso nel set di numeri e nella matrice:

```
min(1, 2, 3)
min([1, 2, 3])
```

E viene restituito questo risultato: `1`

<a name="mod"></a>

## <a name="mod"></a>mod

Restituisce il resto della divisione di due numeri. Per ottenere il risultato intero, vedere [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | Sì | Integer o float | Numero da dividere per l'oggetto *divisor* | 
| <*divisor*> | Sì | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0. | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="mul"></a>mul

Restituisce il prodotto della moltiplicazione di due numeri.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Sì | Integer o float | Numero da moltiplicare per *multiplicand2* | 
| <*multiplicand2*> | Sì | Integer o float | Numero che moltiplica *multiplicand1* | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

Restituisce il corpo per una parte specifica dell'output di un'azione con più parti.

```
multipartBody('<actionName>', <index>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | Sì | string | Nome per l'azione che ha un output con più parti | 
| <*index*> | Sì | Integer | Valore di indice per la parte desiderata | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*body*> | string | Corpo per la parte specificata | 
|||| 

<a name="not"></a>

## <a name="not"></a>not

Verifica se un'espressione è false. Restituisce true se l'espressione è false o false se è true.

```
not(<expression>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | Sì | boolean | Espressione da verificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| true o false | boolean | Restituisce true se l'espressione è false. Restituisce false se l'espressione è true. |  
|||| 

*Esempio 1*

Questi esempi verificano se le espressioni specificate sono false: 

```
not(false)
not(true)
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se le espressioni specificate sono false: 

```
not(equals(1, 2))
not(equals(1, 1))
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

<a name="or"></a>

## <a name="or"></a>oppure

Verifica se almeno un'espressione è true. Restituisce true se almeno un'espressione è true o false se tutte le espressioni sono false.

```
or(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>, ... | Sì | boolean | Espressioni da verificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| true o false | boolean | Restituisce true se almeno un'espressione è true. Restituisce false se tutte le espressioni sono false. |  
|||| 

*Esempio 1*

Questi esempi verificano se almeno un'espressione è true:

```
or(true, false)
or(false, false)
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se almeno un'espressione è true:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

<a name="parameters"></a>

## <a name="parameters"></a>Parametri

Restituisce il valore per un parametro descritto nella definizione dell'app per la logica. 

```
parameters('<parameterName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*parameterName*> | Sì | string | Nome del parametro di cui si vuole ottenere il valore | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*parameter-value*> | Qualsiasi | Valore del parametro specificato | 
|||| 

*Esempio* 

Si supponga di avere questo valore JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Questo esempio ottiene il valore per il parametro specificato:

```
parameters('fullName')
```

E viene restituito questo risultato: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>rand

Restituisce un valore intero casuale da un intervallo specificato, che include solo l'estremo inziale.

```
rand(<minValue>, <maxValue>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*minValue*> | Sì | Integer | Valore intero più basso nell'intervallo | 
| <*maxValue*> | Sì | Integer | Valore intero che segue il valore intero più alto nell'intervallo che la funzione può restituire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="range"></a>range

Restituisce una matrice di valori interi che inizia da un valore intero specificato.

```
range(<startIndex>, <count>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*startIndex*> | Sì | Integer | Valore intero che rappresenta il primo elemento da cui inizia la matrice | 
| <*count*> | Sì | Integer | Numero di valori interi nella matrice | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="replace"></a>replace

Sostituisce una sottostringa con la stringa specificata e restituisce la stringa risultante. Questa funzione fa distinzione tra maiuscole e minuscole.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa che contiene la sottostringa da sostituire | 
| <*oldText*> | Sì | string | Sottostringa da sostituire | 
| <*newText*> | Sì | string | Stringa da usare per la sostituzione | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-text*> | string | Stringa aggiornata dopo la sostituzione della sottostringa <p>Se la sottostringa non viene trovata, viene restituita la stringa originale. | 
|||| 

*Esempio* 

Questo esempio trova la sottostringa "old" in "the old string" e sostituisce "old" con "new": 

```
replace('the old string', 'old', 'new')
```

E viene restituito questo risultato: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Rimuove una proprietà da un oggetto e restituisce l'oggetto aggiornato.

```
removeProperty(<object>, '<property>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Sì | Oggetto | Oggetto JSON da cui si vuole rimuovere una proprietà | 
| <*property*> | Sì | string | Nome della proprietà da rimuovere | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato senza la proprietà specificata | 
|||| 

*Esempio*

Questo esempio rimuove la proprietà `"accountLocation"` da un oggetto `"customerProfile"`, di cui viene eseguita la conversione in JSON con la funzione [JSON()](#json) e restituisce l'oggetto aggiornato:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>setProperty

Imposta il valore per la proprietà di un oggetto e restituisce l'oggetto aggiornato. Per aggiungere una nuova proprietà, è possibile usare questa funzione o la funzione [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Sì | Oggetto | Oggetto JSON di cui si vuole impostare la proprietà | 
| <*property*> | Sì | string | Nome per la proprietà nuova o esistente da impostare | 
| <*value*> | Sì | Qualsiasi | Valore da impostare per la proprietà specificata |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato di cui si imposta la proprietà | 
|||| 

*Esempio*

Questo esempio imposta la proprietà `"accountNumber"` in un oggetto `"customerProfile"`, di cui viene eseguita la conversione in JSON con la funzione [JSON()](#json). La funzione assegna un valore generato dalla funzione [guid()](#guid) e restituisce l'oggetto JSON aggiornato:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>skip

Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi.

```
skip([<collection>], <count>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | Array | Raccolta da cui rimuovere gli elementi | 
| <*count*> | Sì | Integer | Valore intero positivo per il numero di elementi da rimuovere nella parte iniziale | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| [<*updated-collection*>] | Array | Raccolta aggiornata dopo la rimozione degli elementi specificati | 
|||| 

*Esempio*

Questo esempio rimuove un elemento, il numero 0, dall'inizio della matrice specificata: 

```
skip([0, 1, 2, 3], 1)
```

E restituisce la matrice con gli elementi rimanenti: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Restituisce una matrice con tutti i caratteri di una stringa, con ogni carattere separato da un *delimitatore*.

```
split('<text>', '<separator>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa che contiene i caratteri da dividere |  
| <*separator*> | Sì | string | Separatore visualizzato tra ogni carattere nella matrice risultante | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| [<*char1*><*separator*><*char2*><*separator*>...] | Array | Matrice risultante creata da tutti gli elementi nella stringa specificata |
|||| 

*Esempio* 

Questo esempio crea una matrice dalla stringa specificata, separando ogni carattere con una virgola come delimitatore:

```
split('abc', ',')
```

E viene restituito questo risultato: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Restituisce l'inizio del giorno per un timestamp. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp specificato, che inizia in corrispondenza della mezzanotte esatta per il giorno | 
|||| 

*Esempio* 

Questo esempio trova l'inizio del giorno per questo timestamp:

```
startOfDay('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Restituisce l'inizio dell'ora per un timestamp. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp specificato, che inizia in corrispondenza dei minuti esatti per l'ora | 
|||| 

*Esempio* 

Questo esempio trova l'inizio dell'ora per questo timestamp:

```
startOfHour('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Restituisce l'inizio del mese per un timestamp. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp specificato, che inizia il primo giorno del mese in corrispondenza della mezzanotte esatta | 
|||| 

*Esempio* 

Questo esempio restituisce l'inizio del mese per questo timestamp:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startsWith

Verifica se una stringa inizia con una sottostringa specifica. Restituisce true se la sottostringa viene trovata o false se non viene trovata. Questa funzione non fa distinzione tra maiuscole e minuscole.

```
startsWith('<text>', '<searchText>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da verificare | 
| <*searchText*> | Sì | string | Stringa iniziale da trovare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="string"></a>stringa

Restituisce la versione stringa di un valore.

```
string(<value>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | Qualsiasi | Valore da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*string-value*> | string | Versione stringa del valore specificato | 
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

## <a name="sub"></a>sub

Restituisce il risultato della sottrazione del secondo numero dal primo.

```
sub(<minuend>, <subtrahend>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | Sì | Integer o float | Numero da cui sottrarre l'oggetto *subtrahend* | 
| <*subtrahend*> | Sì | Integer o float | Numero da sottrarre dall'oggetto *minuend* | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
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

## <a name="substring"></a>substring

Restituisce i caratteri di una stringa, partendo dalla posizione o dall'indice specificato. I valori di Indice iniziano con il numero 0. 

```
substring('<text>', <startIndex>, <length>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da cui ottenere i caratteri | 
| <*startIndex*> | Sì | Integer | Numero positivo per la posizione iniziale o valore di indice | 
| <*length*> | Sì | Integer | Numero positivo di caratteri da includere nella sottostringa | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*substring-result*> | string | Sottostringa con il numero di caratteri specificato, che inizia in corrispondenza della posizione di indice specificata nella stringa di origine | 
|||| 

*Esempio* 

Questo esempio crea una sottostringa di cinque caratteri dalla stringa specificata, a partire dal valore di indice 6:

```
substring('hello world', 6, 5)
```

E viene restituito questo risultato: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Sottrae un numero di unità di tempo da un timestamp. Vedere anche [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa contenente il timestamp | 
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da sottrarre | 
| <*timeUnit*> | Sì | string | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | string | Timestamp meno il numero di unità di tempo specificato | 
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

## <a name="take"></a>take

Restituisce gli elementi dall'inizio di una raccolta. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | Sì | String o array | Raccolta da cui ottenere gli elementi | 
| <*count*> | Sì | Integer | Valore intero positivo per il numero di elementi da ottenere dalla parte iniziale | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*subset*> o [<*subset*>] | String o array, rispettivamente | Stringa o matrice con il numero specificato di elementi presi dall'inizio della raccolta originale | 
|||| 

*Esempio*

Questi esempi ottengono il numero specificato di elementi presi dall'inizio di queste raccolte:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

E vengono restituiti questi risultati:

* Primo esempio: `"abc"`
* Secondo esempio: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>ticks

Restituisce il valore della proprietà `ticks` per un timestamp specificato. Un *tick* è un intervallo di 100 nanosecondi.

```
ticks('<timestamp>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | Sì | string | Stringa per un timestamp | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*ticks-number*> | Integer | Numero di tick dal timestamp specificato | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Restituisce una stringa in formato minuscolo. Se un carattere nella stringa non ha un equivalente minuscolo, viene incluso senza modifiche nella stringa restituita.

```
toLower('<text>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da restituire in formato minuscolo | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*lowercase-text*> | string | Stringa originale in formato minuscolo | 
|||| 

*Esempio* 

Questo esempio converte questa stringa in caratteri minuscoli: 

```
toLower('Hello World')
```

E viene restituito questo risultato: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Restituisce una stringa in formato maiuscolo. Se un carattere nella stringa non ha un equivalente maiuscolo, viene incluso senza modifiche nella stringa restituita.

```
toUpper('<text>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da restituire in formato maiuscolo | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*uppercase-text*> | string | Stringa originale in formato maiuscolo | 
|||| 

*Esempio* 

Questo esempio converte questa stringa in caratteri maiuscoli:

```
toUpper('Hello World')
```

E viene restituito questo risultato: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>trigger

Restituisce l'output di un trigger in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione. 

* All'interno degli input di un trigger, questa funzione restituisce l'output dell'esecuzione precedente. 

* All'interno di una condizione di un trigger, questa funzione restituisce l'output dell'esecuzione precedente. 

Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto trigger, ma è possibile specificare una proprietà con il valore desiderato. Per questa funzione sono anche disponibili le versioni a sintassi abbreviata. Vedere [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody). 

```
trigger()
```

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | string | Output di un trigger in fase di esecuzione | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Restituisce l'oggetto `body` di output di un trigger in fase di esecuzione. Sintassi abbreviata per `trigger().outputs.body`. Vedere [trigger()](#trigger). 

```
triggerBody()
```

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*trigger-body-output*> | string | Oggetto `body` di output del trigger | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Restituisce una matrice con i valori corrispondenti a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un trigger. 

```
triggerFormDataMultiValues('<key>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | Sì | string | Nome della chiave di cui si vuole ottenere il valore | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | Array | Matrice con tutti i valori che corrispondono alla chiave specificata | 
|||| 

*Esempio* 

Questo esempio crea una matrice dal valore della chiave "feedUrl" nell'output di dati del modulo o codifica per il modulo di un trigger RSS: 

```
triggerFormDataMultiValues('feedUrl')
```

E restituisce questa matrice come risultato di esempio: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Restituisce una stringa con un singolo valore corrispondente a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un trigger. Se la funzione trova più corrispondenze, genera un errore.

```
triggerFormDataValue('<key>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | Sì | string | Nome della chiave di cui si vuole ottenere il valore |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*key-value*> | string | Valore nella chiave specificata | 
|||| 

*Esempio* 

Questo esempio crea una stringa dal valore della chiave "feedUrl" nell'output di dati del modulo o codifica per il modulo di un trigger RSS:

```
triggerFormDataValue('feedUrl')
```

E restituisce questa stringa come risultato di esempio: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

Restituisce il corpo per una parte specifica dell'output di un trigger con più parti. 

```
triggerMultipartBody(<index>)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*index*> | Sì | Integer | Valore di indice per la parte desiderata |
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*body*> | string | Corpo della parte specificata di un output a più parti di un trigger | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie nome-valore JSON. Sintassi abbreviata per `trigger().outputs`. Vedere [trigger()](#trigger). 

```
triggerOutputs()
```

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | string | Output di un trigger in fase di esecuzione  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>Trim

Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata.

```
trim('<text>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | Sì | string | Stringa da cui rimuovere gli spazi iniziali e finali | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | string | Versione aggiornata della stringa originale senza spazi iniziali o finali | 
|||| 

*Esempio* 

Questo esempio rimuove gli spazi iniziali e finali dalla stringa " Hello World  ":  

```
trim(' Hello World  ')
```

E viene restituito questo risultato: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>union

Restituisce una raccolta che contiene *tutti* gli elementi delle raccolte specificate. Per essere incluso nel risultato, un elemento può essere presente in qualsiasi raccolta passata alla funzione. Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>, ...  | Sì | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *tutti* gli elementi | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Array o object, rispettivamente | Raccolta con tutti gli elementi delle raccolte specificate e senza duplicati | 
|||| 

*Esempio* 

Questo esempio ottiene *tutti* gli elementi da queste raccolte: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

E viene restituito questo risultato: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape. Usare questa funzione al posto di [encodeUriComponent()](#encodeUriComponent). Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

```
uriComponent('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa da convertire nel formato con codifica URI | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*encoded-uri*> | string | Stringa con codifica URI con i caratteri di escape | 
|||| 

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
uriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Restituisce la versione binaria di un componente di un URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con codifica URI da convertire | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*binary-for-encoded-uri*> | string | Versione binaria della stringa con codifica URI. Il contenuto binario è con codifica base64 e rappresentato da `$content`. | 
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

## <a name="uricomponenttostring"></a>uriComponentToString

Restituisce la versione stringa di una stringa con codifica URI (Uniform Resource Identifier), decodificando in modo efficace la stringa con codifica URI.

```
uriComponentToString('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con codifica URI da decodificare | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*decoded-uri*> | string | Versione decodificata della stringa con codifica URI | 
|||| 

*Esempio*

Questo esempio crea la versione stringa decodificata per questa stringa con codifica URI: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Restituisce il valore `host` per un URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire il valore `host` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*host-value*> | string | Valore `host` per l'URI specificato | 
|||| 

*Esempio*

Questo esempio trova il valore `host` per questo URI: 

```
uriHost('https://www.localhost.com:8080')
```

E viene restituito questo risultato: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Restituisce il valore `path` per un URI (Uniform Resource Identifier). 

```
uriPath('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire il valore `path` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*path-value*> | string | Valore `path` per l'URI specificato. Se `path` non ha un valore, viene restituito il carattere "/". | 
|||| 

*Esempio*

Questo esempio trova il valore `path` per questo URI: 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Restituisce i valori `path` e `query` per un URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire i valori `path` e `query` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*path-query-value*> | string | Valori `path` e `query` per l'URI specificato. Se `path` non specifica un valore, viene restituito il carattere "/". | 
|||| 

*Esempio*

Questo esempio trova i valori `path` e `query` per questo URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Restituisce il valore `port` per un URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire il valore `port` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*port-value*> | Integer | Valore `port` per l'URI specificato. Se `port` non specifica un valore, viene restituita la porta predefinita per il protocollo. | 
|||| 

*Esempio*

Questo esempio restituisce il valore `port` per questo URI:

```
uriPort('http://www.localhost:8080')
```

E viene restituito questo risultato: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Restituisce il valore `query` per un URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire il valore `query` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*query-value*> | string | Valore `query` per l'URI specificato | 
|||| 

*Esempio*

Questo esempio restituisce il valore `query` per questo URI: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>uriScheme

Restituisce il valore `scheme` per un URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | Sì | string | URI di cui restituire il valore `scheme` | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*scheme-value*> | string | Valore `scheme` per l'URI specificato | 
|||| 

*Esempio*

Questo esempio restituisce il valore `scheme` per questo URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Restituisce il timestamp corrente. 

```
utcNow('<format>')
```

Facoltativamente, è possibile specificare un formato diverso con il parametro <*format*>. 


| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | No  | string | [Identificatore di formato singolo](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddT:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*current-timestamp*> | string | Data e ora correnti | 
|||| 

*Esempio 1*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00. Questo esempio ottiene il timestamp corrente: 

```
utcNow()
```

E viene restituito questo risultato: `"2018-04-15T13:00:00.0000000Z"`

*Esempio 2*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00. Questo esempio ottiene il timestamp corrente usando il formato "D" facoltativo:

```
utcNow('D')
```

E viene restituito questo risultato: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>variables

Restituisce il valore per una variabile specificata. 

```
variables('<variableName>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*variableName*> | Sì | string | Nome della variabile di cui si vuole ottenere il valore | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*variable-value*> | Qualsiasi | Valore della variabile specificata | 
|||| 

*Esempio*

Si supponga che il valore corrente per una variabile "numItems" sia 20. Questo esempio ottiene il valore intero per questa variabile:

```
variables('numItems')
```

E viene restituito questo risultato: `20`

<a name="workflow"></a>

## <a name="workflow"></a>flusso di lavoro

Restituisce tutti i dettagli del flusso di lavoro stesso in fase di esecuzione. 

```
workflow().<property>
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | No  | string | Nome della proprietà del flusso di lavoro di cui si vuole ottenere il valore <p>Un oggetto flusso di lavoro ha queste proprietà: **name**, **type**, **id**, **location** e **run**. Il valore della proprietà **run** è anch'esso un oggetto con le proprietà seguenti: **name**, **type** e **id**. | 
||||| 

*Esempio*

Questo esempio restituisce il nome dell'esecuzione corrente di un flusso di lavoro:

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Restituisce la versione XML di una stringa contenente un oggetto JSON. 

```
xml('<value>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | Sì | string | Stringa con l'oggetto JSON da convertire <p>L'oggetto JSON deve avere una sola proprietà radice. <br>Il carattere barra rovesciata (\\) viene usato come carattere di escape per le virgolette doppie ("). | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*xml-version*> | Oggetto | Elemento XML codificato per la stringa o l'oggetto JSON specificato | 
|||| 

*Esempio 1*

Questo esempio crea la versione XML per questa stringa, che contiene un oggetto JSON: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

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

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

E viene restituito questo elemento XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>xpath

Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o i valori corrispondenti. Un'espressione XPath, o semplicemente "XPath", aiuta a spostarsi nella struttura del documento XML in modo che sia possibile selezionare i nodi o i valori di calcolo nel contenuto XML.

```
xpath('<xml>', '<xpath>')
```

| Parametro | Obbligatoria | type | DESCRIZIONE | 
| --------- | -------- | ---- | ----------- | 
| <*xml*> | Sì | Qualsiasi | Stringa XML per eseguire la ricerca di nodi o valori che corrispondono a un valore dell'espressione XPath | 
| <*xpath*> | Sì | Qualsiasi | Espressione XPath usata per trovare i valori o i nodi XML corrispondenti | 
||||| 

| Valore restituito | type | DESCRIZIONE | 
| ------------ | ---- | ----------- | 
| <*xml-node*> | XML | Nodo XML, quando solo un singolo nodo corrisponde all'espressione XPath specificata | 
| <*value*> | Qualsiasi | Valore di un nodo XML, quando solo un singolo valore corrisponde all'espressione XPath specificata | 
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-oppure- </br>[<*value1*>, <*value2*>, ...] | Array | Matrice con tutti i valori o i nodi XML che corrispondono all'espressione XPath specificata | 
|||| 

*Esempio 1*

Questo esempio trova i nodi che corrispondono al nodo `<name></name>` negli argomenti specificati e restituisce una matrice con i valori di tali nodi: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Ecco gli argomenti:

* Stringa "items" che contiene questo codice XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  L'esempio usa la funzione [parameters()](#parameters) per ottenere la stringa XML dall'argomento "items", ma deve anche convertire la stringa in formato XML usando la funzione [xml()](#xml). 

* Questa espressione XPath, che viene passata come stringa:

  `"/produce/item/name"`

Ecco la matrice di risultati con i nodi corrispondenti a `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Esempio 2*

Dopo l'esempio 1, questo esempio trova i nodi che corrispondono al nodo `<count></count>` e somma i valori di tali nodi con la funzione `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E viene restituito questo risultato: `30`

*Esempio 3*

Per questo esempio, entrambe le espressioni trovano i nodi che corrispondono al nodo `<location></location>`, negli argomenti specificati, che includono codice XML con uno spazio dei nomi. Le espressioni usano il carattere barra rovesciata (\\) come carattere di escape per le virgolette doppie (").

* *Espressione 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Espressione 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Ecco gli argomenti:

* Codice XML, che include lo spazio dei nomi del documento XML, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Una di queste espressioni XPath:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Ecco il nodo del risultato che corrisponde al nodo `<location></location`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Esempio 4*

Dopo l'esempio 3, questo esempio trova il valore nel nodo `<location></location>`: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E viene restituito questo risultato: `"Paris"`

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sul [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md)
