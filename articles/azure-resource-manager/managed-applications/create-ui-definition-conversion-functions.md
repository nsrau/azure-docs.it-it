---
title: Creare funzioni di conversione della definizione dell'interfaccia utente
description: Descrive le funzioni da utilizzare per la conversione di valori tra tipi di dati e codifiche.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098197"
---
# <a name="createuidefinition-conversion-functions"></a>Funzioni di conversione CreateUiDefinition

Queste funzioni possono essere usate per convertire i valori tra codifiche e tipi di dati JSON.

## <a name="bool"></a>bool

Converte il parametro in un valore booleano. Questa funzione supporta parametri di tipo numero, stringa e booleano. Analogamente ai valori booleani in JavaScript, qualsiasi valore eccetto `0` o `'false'` restituisce `true` .

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

## <a name="decodebase64"></a>decodeBase64

Decodifica il parametro da una stringa Base 64. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"Contoso"`:

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Decodifica il parametro da una stringa con codifica URL. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Codifica il parametro in una stringa Base 64. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"Q29udG9zbw=="`:

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Codifica il parametro in una stringa con codifica URL. Questa funzione supporta solo parametri di tipo stringa.

L'esempio seguente restituisce `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

Converte il parametro in un valore a virgola mobile. Questa funzione supporta parametri di tipo numero e stringa.

L'esempio seguente restituisce `1.0`:

```json
"[float('1.0')]"
```

L'esempio seguente restituisce `2.9`:

```json
"[float(2.9)]"
```

## <a name="int"></a>INT

Converte il parametro in un valore intero. Questa funzione supporta parametri di tipo numero e stringa.

L'esempio seguente restituisce `1`:

```json
"[int('1')]"
```

L'esempio seguente restituisce `2`:

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

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

L'esempio seguente restituisce `{"type":"webapp"}`:

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>string

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

L'esempio seguente restituisce `"{"type":"webapp"}"`:

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).
