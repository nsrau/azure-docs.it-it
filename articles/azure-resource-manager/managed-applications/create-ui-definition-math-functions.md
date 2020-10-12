---
title: Creare funzioni matematiche per la definizione dell'interfaccia utente
description: Descrive le funzioni da usare per l'esecuzione di operazioni matematiche.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098168"
---
# <a name="createuidefinition-math-functions"></a>Funzioni matematiche CreateUiDefinition

Le funzioni consentono di eseguire operazioni matematiche.

## <a name="add"></a>add

Somma due numeri e restituisce il risultato.

L'esempio seguente restituisce `3`:

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

Restituisce il valore intero più alto maggiore o uguale al numero specificato.

L'esempio seguente restituisce `4`:

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Divide il primo numero per il secondo e restituisce il risultato. Il risultato è sempre un numero intero.

L'esempio seguente restituisce `2`:

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

Restituisce il valore intero più alto minore o uguale al numero specificato.

L'esempio seguente restituisce `3`:

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Restituisce il più grande di due numeri.

L'esempio seguente restituisce `2`:

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Restituisce il più piccolo dei due numeri.

L'esempio seguente restituisce `1`:

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

Divide il primo numero per il secondo e restituisce il resto.

L'esempio seguente restituisce `0`:

```json
"[mod(6, 3)]"
```

L'esempio seguente restituisce `2`:

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Moltiplica due numeri e restituisce il risultato.

L'esempio seguente restituisce `6`:

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

Restituisce un numero integrale casuale nell'intervallo specificato. Questa funzione non genera numeri casuali protetti con crittografia.

L'esempio seguente può restituire `42`:

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

Genera una sequenza di numeri integrali nell'intervallo specificato.

L'esempio seguente restituisce `[1,2,3]`:

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

Sottrae il secondo numero dal primo e restituisce il risultato.

L'esempio seguente restituisce `1`:

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).
