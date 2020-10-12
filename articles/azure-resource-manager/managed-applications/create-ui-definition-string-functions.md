---
title: Creare funzioni stringa di definizione dell'interfaccia utente
description: Descrive le funzioni di stringa da usare quando si costruiscono le definizioni dell'interfaccia utente per le applicazioni gestite di Azure
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098158"
---
# <a name="createuidefinition-string-functions"></a>Funzioni stringa CreateUiDefinition

Queste funzioni da usare con le stringhe JSON.

## <a name="concat"></a>concat

Concatena una o più stringhe.

Se ad esempio il valore di output di `element1` è `"Contoso"`, questo esempio restituirà la stringa `"Demo Contoso app"`:

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Determina se una stringa termina con un valore.

Nell'esempio seguente viene restituito true.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Genera una stringa univoca globale (GUID).

Nell'esempio seguente viene restituito un valore come `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Restituisce la prima posizione di un valore all'interno di una stringa oppure-1 se non viene trovato.

L'esempio seguente restituisce 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Restituisce l'ultima posizione di un valore in una stringa oppure-1 se non viene trovato.

Nell'esempio seguente viene restituito 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>replace

Restituisce una stringa in cui tutte le occorrenze della stringa specificata nella stringa corrente vengono sostituite con un'altra stringa.

L'esempio seguente restituisce `"Contoso.com web app"`:

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Determina se una stringa inizia con un valore.

Nell'esempio seguente viene restituito true.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

Restituisce la sottostringa della stringa specificata. La sottostringa inizia in corrispondenza dell'indice specificato e ha la lunghezza specificata.

L'esempio seguente restituisce `"web"`:

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Restituisce una stringa convertita in lettere minuscole.

L'esempio seguente restituisce `"contoso"`:

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Restituisce una stringa convertita in lettere maiuscole.

L'esempio seguente restituisce `"CONTOSO"`:

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).

