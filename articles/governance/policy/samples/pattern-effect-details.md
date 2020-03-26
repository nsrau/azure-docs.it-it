---
title: 'Criterio: Effetti di una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare i diversi effetti di una definizione di criteri.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: b86a24bc0af6c9bdd7b29bb0a931d6c78865218b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170459"
---
# <a name="azure-policy-pattern-effects"></a>Modello di Criteri di Azure: effetti

Criteri di Azure include una serie di [effetti](../concepts/effects.md) che determinano il modo in cui il servizio reagisce alle risorse non conformi. Alcuni effetti sono semplici e non richiedono proprietà aggiuntive nella definizione di criteri, mentre altri richiedono diverse proprietà.

## <a name="sample-1-simple-effect"></a>Esempio 1. Effetto semplice

Questa definizione di criteri verifica se il tag definito nel parametro **tagName** esiste nella risorsa valutata. Se il tag non esiste ancora, viene attivato l'effetto [modify](../concepts/effects.md#modify) per aggiungere il tag con il valore nel parametro **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Esempio 1. Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="30-34":::

Un effetto **modify** richiede il blocco **policyRule.then.details** che definisce **roleDefinitionIds** e **operations**. Questi parametri indicano a Criteri di Azure quali ruoli sono necessari per aggiungere il tag e correggere la risorsa e quale operazione **modify** eseguire. In questo esempio, **operation** corrisponde a _add_ e i parametri vengono usati per impostare il tag e il relativo valore.

## <a name="sample-2-complex-effect"></a>Esempio 2: Effetto complesso

Questa definizione di criteri controlla ogni macchina virtuale quando un'estensione, definita nei parametri **publisher** e **type**, non esiste. Usa [auditIfNotExists](../concepts/effects.md#auditifnotexists) per controllare una risorsa correlata alla macchina virtuale e verificare se esiste un'istanza che corrisponde ai parametri definiti. Questo esempio controlla il tipo **extensions**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Esempio 2: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Un effetto **auditIfNotExists** richiede il blocco **policyRule.then.details** per definire sia **type** che **existenceCondition** da cercare. **existenceCondition** usa elementi del linguaggio dei criteri, ad esempio [operatori logici](../concepts/definition-structure.md#logical-operators), per determinare se esiste una risorsa correlata corrispondente. In questo esempio i valori controllati per ogni [alias](../concepts/definition-structure.md#aliases) sono definiti nei parametri.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).