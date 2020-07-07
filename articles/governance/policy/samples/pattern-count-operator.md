---
title: 'Criterio: Operatore count in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare l'operatore count in una definizione di criteri.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 807890b7fb08d790deff6e0be9e08ad91c4ec44d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565750"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Modello di Criteri di Azure: operatore count

L'operatore [count](../concepts/definition-structure.md#count) valuta i membri di un alias \[\*\].

## <a name="sample-policy-definition"></a>Definizione di criteri di esempio

Questa definizione di criterio [controlla](../concepts/effects.md#audit) i gruppi di sicurezza di rete configurati per consentire il traffico RDP (Remote Desktop Protocol) in ingresso.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Spiegazione

I componenti principali dell'operatore **count** sono _field_, _where_ e la condizione. Ogni componente è evidenziato nel frammento di codice seguente.

- _field_ indica a count i membri di quale [alias](../concepts/definition-structure.md#aliases) valutare. In questo articolo viene esaminata la _matrice_ di alias **securityRules\[\*\]** del gruppo di sicurezza di rete.
- _where_ usa il linguaggio dei criteri per definire quali membri della _matrice_ soddisfano i criteri. In questo esempio l'operatore logico **allOf** raggruppa tre diverse condizioni di valutazione delle proprietà della _matrice_ di alias: _direction_, _access_ e _destinationPortRange_.
- La condizione count in questo esempio è **greater**. Count restituisce true se uno o più membri della _matrice_ di alias soddisfano la clausola _where_.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).