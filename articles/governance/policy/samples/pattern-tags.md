---
title: 'Criterio: Uso di tag in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce esempi di come aggiungere tag con parametri o ereditare tag da un gruppo di risorse in una definizione di criteri.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545557"
---
# <a name="azure-policy-pattern-tags"></a>Modello di Criteri di Azure: tag

I [tag](../../..//azure-resource-manager/management/tag-resources.md) rappresentano una parte importante della gestione, dell'organizzazione e del controllo delle risorse di Azure. Criteri di Azure consente di configurare i tag per le risorse nuove ed esistenti su larga scala con l'effetto [modifica](../concepts/effects.md#modify) e le [attività di correzione](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Esempio 1. Parametrizzare i tag

Questa definizione di criteri usa due parametri, **tagName** e **tagValue**, per impostare gli elementi da cercare nei gruppi di risorse con l'assegnazione di criteri. Questo formato consente di usare la definizione dei criteri per un numero qualsiasi di combinazioni di nomi e valori di tag, ma di mantenere una singola definizione di criteri.

> [!NOTE]
> Mentre questo modello di definizione dei criteri è simile a quello di [Criterio: Parametri - Esempio n. 1](./pattern-parameters.md#sample-1-string-parameters), questo esempio usa la **modalità** _All_ e si rivolge ai gruppi di risorse.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Esempio 1. Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

In questo esempio, la **modalità** è impostata su _All_ poiché è destinata a un gruppo di risorse. Nella maggior parte dei casi, è consigliabile impostare la **modalità** su _Indexed_ quando si usano i tag. Per altre informazioni, vedere le [modalità](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

In questa parte della definizione dei criteri, `concat` combina il parametro **tagName** con parametri e il formato `tags['name']` per indicare a **field** di valutare tale tag per il parametro **tagValue**.
Poiché viene usato **notEquals**, se **tags\[tagName\]** non è uguale a **tagValue**, viene attivato l'effetto **modifica**.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

In questo caso, lo stesso formato per l'uso dei valori dei tag con parametri viene usato dall'operazione **addOrReplace** per creare o aggiornare il tag al valore desiderato nel gruppo di risorse valutato.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Esempio 2: Ereditare il valore del tag dal gruppo di risorse

Questa definizione di criteri usa il parametro **tagName** per determinare il valore del tag da ereditare dal gruppo di risorse padre.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Esempio 2: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

In questo esempio, la **modalità** è impostata su _Indexed_ perché non è destinata a un gruppo di risorse o a una sottoscrizione, anche se ottiene il valore da un gruppo di risorse. Per altre informazioni, vedere le [modalità](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**policyRule.if** usa `concat` come [Esempio n. 1](#sample-1-parameterize-tags) per valutare il valore di **tagName**, ma usa la funzione `resourceGroup()` per confrontarlo con il valore dello stesso tag nel gruppo di risorse padre. La seconda clausola verifica che il tag nel gruppo di risorse abbia un valore e non sia Null.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

In questo caso, il valore assegnato al tag **tagName** nella risorsa usa anche la funzione `resourceGroup()` per ottenere il valore dal gruppo di risorse padre. In questo modo, è possibile ereditare i tag dai gruppi di risorse padre. Se la risorsa è già stata creata ma non è stato aggiunto il tag, questa stessa definizione di criteri e un'[attività di correzione](../how-to/remediate-resources.md) possono aggiornare le risorse esistenti.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).