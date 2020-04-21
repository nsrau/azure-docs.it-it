---
title: 'Criterio: Operatori logici in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare gli operatori logici in una definizione di criteri.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272509"
---
# <a name="azure-policy-pattern-logical-operators"></a>Modello di Criteri di Azure: operatori logici

Una definizione di criteri può contenere diverse istruzioni condizionali. Potrebbe essere necessario che ogni istruzione sia vera o che lo siano solo alcune. Per supportare queste esigenze, il linguaggio include gli [operatori logici](../concepts/definition-structure.md#logical-operators) **not**, **allOf**e **anyOf**. Sono facoltativi e possono essere annidati per creare scenari complessi.

## <a name="sample-1-one-logical-operator"></a>Esempio 1. Un unico operatore logico

Questa definizione di criteri valuta gli account CosmosDB per verificare se sono configurati i failover automatici e più percorsi di scrittura. Se non lo sono, viene attivato l'effetto [audit](../concepts/effects.md#audit) che crea una voce di log quando la risorsa non conforme viene creata o aggiornata.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Esempio 1. Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

Il blocco **policyRule.if** usa un singolo operatore **allOf** per assicurar che tutte e tre le condizioni siano vere.
Solo se tutte queste condizioni restituiscono true, viene attivato l'effetto **audit**.

## <a name="sample-2-multiple-logical-operators"></a>Esempio 2: Più operatori logici

Questa definizione di criteri valuta le risorse in base a un modello di denominazione. Se una risorsa non corrisponde, viene [negata](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Esempio 2: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Anche questo blocco **policyRule.if** include un singolo operatore **allOf**, ma ogni condizione è racchiusa nell'operatore logico **not**. Viene valutata prima l'istruzione condizionale all'interno dell'operatore logico **not** e quindi viene valutato l'operatore logico **not** per determinare se l'intera clausola è vera o falsa. Se entrambi gli operatori logici **not** operatori logici restituiscono true, viene attivato l'effetto dei criteri.

## <a name="sample-3-combining-logical-operators"></a>Esempio 3: Combinazione di operatori logici

Questa definizione dei criteri valuta gli account Java Spring per verificare se la traccia non è abilitata o se la traccia non si trova in uno stato di esito positivo.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Esempio 3: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Questo blocco **policyRule.if** include entrambi gli operatori logici **allOf** e **anyOf**. L'operatore logico **anyOf** restituisce true finché una condizione inclusa è true. Poiché _type_ è alla base dell'operatore **allOf**, deve sempre restituire true. Se _type_ e una delle condizioni nell'operatore **anyOf** sono true, viene attivato l'effetto dei criteri.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).