---
title: Creare funzioni di data di definizione dell'interfaccia utente
description: Descrive le funzioni da utilizzare quando si utilizzano i valori di data.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098188"
---
# <a name="createuidefinition-date-functions"></a>Funzioni di data CreateUiDefinition

Funzioni da utilizzare quando si utilizzano valori di data.

## <a name="addhours"></a>addHours

Aggiunge un numero integrale di ore a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Aggiunge un numero integrale di minuti a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Aggiunge un numero integrale di secondi a un timestamp specificato.

L'esempio seguente restituisce `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Restituisce una stringa in formato ISO 8601 della data e ora corrente del computer locale.

L'esempio seguente può restituire `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).
