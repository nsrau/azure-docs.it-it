---
title: 'Criterio: Definizioni di criteri di gruppo con iniziative'
description: Questo modello di Criteri di Azure fornisce un esempio di come raggruppare le definizioni di criteri in un'iniziativa
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170249"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Modello di Criteri di Azure: definizioni di criteri di gruppo

Un'iniziativa è un gruppo di definizioni di criteri. Raggruppando definizioni di criteri correlate in un singolo oggetto, è possibile creare una singola assegnazione invece di più di una.

## <a name="sample-initiative-definition"></a>Definizione di iniziativa di esempio

Questa iniziativa distribuisce due definizioni di criteri, ognuna delle quali accetta i parametri **tagName** e **tagValue**. L'iniziativa stessa include due parametri, **costCenterValue** e **productNameValue**.
Ognuno di questi parametri dell'iniziativa viene fornito a ognuna delle definizioni di criteri raggruppate. Questo schema ottimizza il riutilizzo delle definizioni di criteri esistenti, limitando al tempo stesso il numero di assegnazioni create per implementarle come necessario.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Spiegazione

#### <a name="initiative-parameters"></a>Parametri delle iniziative

Un'iniziativa può definire i propri parametri che vengono quindi passati alle definizioni di criteri raggruppate.
In questo esempio, sia **costCenterValue** che **productNameValue** vengono definiti come parametri dell'iniziativa. I valori vengono forniti quando l'iniziativa viene assegnata.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Definizioni di criteri incluse

Ogni definizione di criteri inclusa deve fornire il parametro **policyDefinitionId** e una matrice di **parametri** se la definizione di criteri accetta parametri. Nel frammento di codice seguente la definizione di criteri inclusa accetta due parametri, **tagName** e **tagValue**. **tagName** viene definito con un valore letterale, ma **tagValue** usa il parametro **costCenterValue** definito dall'iniziativa. Il pass-through dei valori migliora il riutilizzo.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).