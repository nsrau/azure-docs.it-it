---
title: Creare funzioni di raccolta delle definizioni dell'interfaccia utente
description: Descrive le funzioni da utilizzare quando si utilizzano raccolte, ad esempio matrici e oggetti.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098198"
---
# <a name="createuidefinition-collection-functions"></a>Funzioni di raccolta CreateUiDefinition

Queste funzioni possono essere usate con le raccolte, ad esempio stringhe, matrici e oggetti JSON.

## <a name="contains"></a>contains

Restituisce `true` se una stringa contiene la sottostringa specificata, una matrice contiene il valore specificato o un oggetto contiene la chiave specificata.

### <a name="example-string-contains"></a>Esempio: stringa contiene

L'esempio seguente restituisce `true`:

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Esempio: matrice contiene

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `false`:

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Esempio: oggetto contiene

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `true`:

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

Restituisce `true` se la stringa, la matrice o l'oggetto sono Null o vuoti.

### <a name="example-string-empty"></a>Esempio: stringa vuota

L'esempio seguente restituisce `true`:

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Esempio: matrice vuota

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `false`:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Esempio: oggetto vuoto

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `false`:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Esempio: null e non definito

Si supponga che `element1` sia `null` o non definito. L'esempio seguente restituisce `true`:

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Restituisce una nuova matrice dopo aver applicato la logica di filtro fornita come funzione lambda. Il primo parametro è la matrice da usare per il filtro. Il secondo parametro è la funzione lambda che specifica la logica di filtro.

Nell'esempio seguente viene restituita la matrice `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

Restituisce il primo carattere della stringa specificata, il primo valore della matrice specificata o la prima chiave e il primo valore dell'oggetto specificato.

### <a name="example-string-first"></a>Esempio: prima stringa

L'esempio seguente restituisce `"c"`:

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Esempio: prima matrice

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `1`:

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Esempio: oggetto per primo

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `{"key1": "Linux"}`:

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

Restituisce l'ultimo carattere della stringa specificata, l'ultimo valore della matrice specificata o l'ultima chiave e l'ultimo valore dell'oggetto specificato.

### <a name="example-string-last"></a>Esempio: stringa Last

L'esempio seguente restituisce `"o"`:

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Esempio: ultima matrice

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `3`:

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Esempio: oggetto Last

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `{"key2": "Windows"}`:

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

Restituisce il numero di caratteri in una stringa, il numero di valori in una matrice o il numero di chiavi in un oggetto.

### <a name="example-string-length"></a>Esempio: lunghezza della stringa

L'esempio seguente restituisce `7`:

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Esempio: lunghezza della matrice

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `3`:

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Esempio: lunghezza dell'oggetto

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `2`:

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

Restituisce una nuova matrice dopo la chiamata di una funzione lambda su una matrice fornita. Il primo parametro è la matrice da usare per la funzione lambda. Il secondo parametro è la funzione lambda.

Nell'esempio seguente viene restituita una nuova matrice con ogni valore raddoppiato. Il risultato è `[2, 4, 6]`.

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

Nell'esempio seguente viene restituita una nuova matrice `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

Ignora un numero specificato di elementi in una raccolta e quindi restituisce gli elementi rimanenti.

### <a name="example-string-skip"></a>Esempio: stringa Skip

L'esempio seguente restituisce `"app"`:

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Esempio: matrice Skip

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `[3]`:

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Esempio: oggetto Skip

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
L'esempio seguente restituisce `{"key2": "Windows"}`:

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>Split

Restituisce una matrice di stringhe contenente le sottostringhe dell'input delimitato dal separatore.

Nell'esempio seguente viene restituita la matrice `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Restituisce un numero specificato di caratteri contigui dall'inizio della stringa, un numero specificato di valori contigui dall'inizio della matrice o un numero di chiavi e valori contigui dall'inizio dell'oggetto.

### <a name="example-string-take"></a>Esempio: stringa Take

L'esempio seguente restituisce `"web"`:

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Esempio: eseguire una matrice

Si supponga che `element1` restituisca `[1, 2, 3]`. L'esempio seguente restituisce `[1, 2]`:

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Esempio: oggetto Take

Si supponga che `element1` restituisca:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

L'esempio seguente restituisce `{"key1": "Linux"}`:

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).
