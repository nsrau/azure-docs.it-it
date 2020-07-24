---
title: Creare funzioni logiche per la definizione dell'interfaccia utente
description: Descrive le funzioni per eseguire operazioni logiche.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098178"
---
# <a name="createuidefinition-logical-functions"></a>Funzioni logiche CreateUiDefinition

Queste funzioni possono essere utilizzate nelle espressioni condizionali. Alcune funzioni potrebbero non supportare tutti i tipi di dati JSON.

## <a name="and"></a>e

Restituisce `true` se tutti i parametri restituiscono `true`. Questa funzione supporta due o più parametri solo di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

L'esempio seguente restituisce `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

Restituisce il valore del primo parametro non Null. Questa funzione supporta tutti i tipi di dati JSON.

Si supponga che `element1` e `element2` non siano definiti. L'esempio seguente restituisce `"Contoso"`:

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Questa funzione è particolarmente utile nel contesto della chiamata facoltativa che si verifica a causa dell'azione dell'utente dopo il caricamento della pagina. Un esempio è se i vincoli posizionati su un campo nell'interfaccia utente dipendono dal valore attualmente selezionato di un altro campo **inizialmente non visibile** . In questo caso, `coalesce()` può essere usato per consentire alla funzione di essere sintatticamente valida in fase di caricamento della pagina, con l'effetto desiderato quando l'utente interagisce con il campo.

Si consideri questo `DropDown` , che consente all'utente di scegliere tra diversi tipi di database:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Per condizionare l'azione di un altro campo sul valore selezionato corrente di questo campo, usare `coalesce()` , come illustrato di seguito:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Questa operazione è necessaria perché `databaseType` inizialmente non è visibile e pertanto non dispone di un valore. In questo modo l'intera espressione non viene valutata correttamente.

## <a name="equals"></a>equals

Restituisce `true` se entrambi i parametri hanno lo stesso tipo e valore. Questa funzione supporta tutti i tipi di dati JSON.

L'esempio seguente restituisce `true`:

```json
"[equals(0, 0)]"
```

L'esempio seguente restituisce `true`:

```json
"[equals('web', 'web')]"
```

L'esempio seguente restituisce `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Restituisce `true` se il primo parametro è necessariamente maggiore del secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `false`:

```json
"[greater(1, 2)]"
```

L'esempio seguente restituisce `true`:

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Restituisce `true` se il primo parametro è maggiore o uguale al secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Restituisce un valore in base a un condizione true o false. Il primo parametro è la condizione da testare. Il secondo parametro è il valore da restituire se la condizione è true. Il terzo parametro è il valore da restituire se la condizione è false.

Nell'esempio seguente viene restituito `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Restituisce `true` se il primo parametro è necessariamente minore del secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[less(1, 2)]"
```

L'esempio seguente restituisce `false`:

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Restituisce `true` se il primo parametro è minore o uguale al secondo parametro. Questa funzione supporta solo parametri di tipo numero e stringa.

L'esempio seguente restituisce `true`:

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Restituisce `true` se il parametro restituisce `false`. Questa funzione supporta solo parametri di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[not(false)]"
```

L'esempio seguente restituisce `false`:

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>oppure

Restituisce `true` se almeno uno dei parametri restituisce `true`. Questa funzione supporta solo due o più parametri di tipo booleano.

L'esempio seguente restituisce `true`:

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

L'esempio seguente restituisce `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ad Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../management/overview.md).
