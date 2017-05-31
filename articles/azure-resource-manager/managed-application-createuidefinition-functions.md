---
title: Funzioni per la creazione di definizioni dell&quot;interfaccia utente di Applicazione gestita di Azure | Microsoft Docs
description: Illustra le funzioni da usare durante la creazione di definizioni dell&quot;interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 913674dcbb88f3bad0cc8e41241753eae86b309c
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="createuidefinition-functions"></a>Funzioni di CreateUiDefinition
Questa sezione contiene le firme per tutte le funzioni supportate di CreateUiDefinition.

Per usare una funzione, racchiudere la dichiarazione tra parentesi quadre. ad esempio:

```json
"[function()]"
```

È possibile fare riferimento a stringhe e altre funzioni come parametri per una funzione, ma le stringhe devono essere racchiuse tra virgolette singole. ad esempio:

```json
"[fn1(fn2(), 'foobar')]"
```

Ove applicabile, si può fare riferimento alle proprietà dell'output di una funzione usando l'operatore punto. ad esempio:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funzioni di riferimento
Queste funzioni possono essere usate per fare riferimento a output delle proprietà o del contesto di CreateUiDefinition.

### <a name="basics"></a>basics
Restituisce i valori di output di un elemento che viene definito nel passaggio basics.

L'esempio seguente restituisce l'output dell'elemento denominato `foo` nel passaggio basics:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
Restituisce i valori di output di un elemento che viene definito nel passaggio specificato. Per ottenere i valori di output degli elementi nel passaggio basic, usare invece `basics()`.

L'esempio seguente restituisce l'output dell'elemento denominato `bar` nel passaggio denominato `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Restituisce il percorso selezionato nel passaggio basics o nel contesto corrente.

L'esempio seguente può restituire `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funzioni stringa
Queste funzioni possono essere usate solo con stringhe JSON.

### <a name="concat"></a>concat
Concatena una o più stringhe.

Se ad esempio il valore di output di `element1` è `"bar"`, questo esempio restituirà la stringa `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>substring
Restituisce la sottostringa della stringa specificata. La sottostringa inizia in corrispondenza dell'indice specificato e ha la lunghezza specificata.

L'esempio seguente restituisce `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
Restituisce una stringa in cui tutte le occorrenze della stringa specificata nella stringa corrente vengono sostituite con un'altra stringa.

L'esempio seguente restituisce `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Genera una stringa univoca globale (GUID).

L'esempio seguente può restituire `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Restituisce una stringa convertita in lettere minuscole.

L'esempio seguente restituisce `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Restituisce una stringa convertita in lettere maiuscole.

L'esempio seguente restituisce `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funzioni di raccolta
Queste funzioni possono essere usate con le raccolte, ad esempio stringhe, matrici e oggetti JSON.

### <a name="contains"></a>contains
Restituisce `true` se una stringa contiene la sottostringa specificata, una matrice contiene il valore specificato o un oggetto contiene la chiave specificata.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L'esempio seguente restituisce `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Restituisce il numero di caratteri in una stringa, il numero di valori in una matrice o il numero di chiavi in un oggetto.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L'esempio seguente restituisce `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Restituisce `true` se la stringa, la matrice o l'oggetto sono Null o vuoti.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L'esempio seguente restituisce `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Esempio 4: Null e non definito
Si supponga che `element1` sia `null` o non definito. L'esempio seguente restituisce `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
Restituisce il primo carattere della stringa specificata, il primo valore della matrice specificata o la prima chiave e il primo valore dell'oggetto specificato.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
L'esempio seguente restituisce `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
Restituisce l'ultimo carattere della stringa specificata, l'ultimo valore della matrice specificata o l'ultima chiave e l'ultimo valore dell'oggetto specificato.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L'esempio seguente restituisce `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Restituisce un numero specificato di caratteri contigui dall'inizio della stringa, un numero specificato di valori contigui dall'inizio della matrice o un numero di chiavi e valori contigui dall'inizio dell'oggetto.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L'esempio seguente restituisce `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Ignora un numero specificato di elementi in una raccolta e quindi restituisce gli elementi rimanenti.

#### <a name="example-1-string"></a>Esempio 1: stringa
L'esempio seguente restituisce `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Esempio 2: matrice
Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Esempio 3: oggetto
Si supponga che `element1` restituisca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
L'esempio seguente restituisce `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funzioni logiche
Queste funzioni possono essere usate nelle istruzioni condizionali. Alcune funzioni potrebbero non supportare tutti i tipi di dati JSON.

### <a name="equals"></a>equals
Restituisce `true` se entrambi i parametri hanno lo stesso tipo e valore. Questa funzione supporta tutti i tipi di dati JSON.

L'esempio seguente restituisce `true`:

```json
"[equals(0, 0)]"
```

L'esempio seguente restituisce `true`:

```json
"[equals('foo', 'foo')]"
```

L'esempio seguente restituisce `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Restituisce `true` se il primo parametro è necessariamente minore del secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[less(1, 2)]"
```

L'esempio seguente restituisce `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Restituisce `true` se il primo parametro è minore o uguale al secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Restituisce `true` se il primo parametro è necessariamente maggiore del secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `false`:

```json
"[greater(1, 2)]"
```

L'esempio seguente restituisce `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Restituisce `true` se il primo parametro è maggiore o uguale al secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Restituisce `true` se tutti i parametri restituiscono `true`. Questa funzione supporta solo due o più parametri di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

L'esempio seguente restituisce `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>oppure
Restituisce `true` se almeno uno dei parametri restituisce `true`. Questa funzione supporta solo due o più parametri di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

L'esempio seguente restituisce `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Restituisce `true` se il parametro restituisce `false`. Questa funzione supporta solo parametri di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[not(false)]"
```

L'esempio seguente restituisce `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Restituisce il valore del primo parametro non Null. Questa funzione supporta tutti i tipi di dati JSON.

Si supponga che `element1` e `element2` non siano definiti. L'esempio seguente restituisce `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funzioni di conversione
Queste funzioni possono essere usate per convertire i valori tra codifiche e tipi di dati JSON.

### <a name="int"></a>int
Converte il parametro in un valore intero. Questa funzione supporta parametri di tipo numero e stringa.

L'esempio seguente restituisce `1`:

```json
"[int('1')]"
```

L'esempio seguente restituisce `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Converte il parametro in un valore a virgola mobile. Questa funzione supporta parametri di tipo numero e stringa.

L'esempio seguente restituisce `1.0`:

```json
"[float('1.0')]"
```

L'esempio seguente restituisce `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Converte il parametro in una stringa. Questa funzione supporta parametri di tutti i tipi di dati JSON.

L'esempio seguente restituisce `"1"`:

```json
"[string(1)]"
```

L'esempio seguente restituisce `"2.9"`:

```json
"[string(2.9)]"
```

L'esempio seguente restituisce `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

L'esempio seguente restituisce `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte il parametro in un valore booleano. Questa funzione supporta parametri di tipo numero, stringa e booleano. Analogamente ai valori booleani in JavaScript, qualsiasi valore eccetto `0` o `'false'` restituisce `true`.

L'esempio seguente restituisce `true`:

```json
"[bool(1)]"
```

L'esempio seguente restituisce `false`:

```json
"[bool(0)]"
```

L'esempio seguente restituisce `true`:

```json
"[bool(true)]"
```

L'esempio seguente restituisce `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Converte il parametro in un tipo nativo. In altre parole, questa funzione è l'inverso di `string()`. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `1`:

```json
"[parse('1')]"
```

L'esempio seguente restituisce `true`:

```json
"[parse('true')]"
```

L'esempio seguente restituisce `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

L'esempio seguente restituisce `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica il parametro in una stringa Base 64. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Decodifica il parametro da una stringa Base 64. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica il parametro in una stringa con codifica URL. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodifica il parametro da una stringa con codifica URL. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"https://portal.azure.com/"`:

```json
"[encodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funzioni matematiche
### <a name="add"></a>add
Somma due numeri e restituisce il risultato.

L'esempio seguente restituisce `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Sottrae il secondo numero dal primo e restituisce il risultato.

L'esempio seguente restituisce `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Moltiplica due numeri e restituisce il risultato.

L'esempio seguente restituisce `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide il primo numero per il secondo e restituisce il risultato. Il risultato è sempre un numero intero.

L'esempio seguente restituisce `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide il primo numero per il secondo e restituisce il resto.

L'esempio seguente restituisce `0`:

```json
"[mod(6, 3)]"
```

L'esempio seguente restituisce `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>Min
Restituisce il più piccolo di due numeri.

L'esempio seguente restituisce `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Restituisce il più grande di due numeri.

L'esempio seguente restituisce `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Genera una sequenza di numeri integrali nell'intervallo specificato.

L'esempio seguente restituisce `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Restituisce un numero integrale casuale nell'intervallo specificato. Questa funzione non genera numeri casuali protetti con crittografia.

L'esempio seguente può restituire `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
Restituisce il valore intero più alto minore o uguale al numero specificato.

L'esempio seguente restituisce `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Restituisce il valore intero più alto maggiore o uguale al numero specificato.

L'esempio seguente restituisce `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funzioni di data
### <a name="utcnow"></a>utcNow
Restituisce una stringa in formato ISO 8601 della data e ora corrente del computer locale.

L'esempio seguente può restituire `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Aggiunge un numero integrale di secondi a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Aggiunge un numero integrale di minuti a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Aggiunge un numero integrale di ore a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).


