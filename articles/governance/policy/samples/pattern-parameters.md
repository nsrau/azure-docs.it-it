---
title: 'Criterio: Parametri in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare i parametri in una definizione di criteri.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 6d938cdf2f31d30932d3631e99254b7d833f2941
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545574"
---
# <a name="azure-policy-pattern-parameters"></a>Modello di Criteri di Azure: parametri

Una definizione di criteri può essere resa dinamica tramite [parametri](../concepts/definition-structure.md#parameters) per ridurre il numero di definizioni necessarie. Il parametro viene definito durante l'assegnazione dei criteri. I parametri includono un set di proprietà predefinite che ne descrivono le caratteristiche e il modo in cui vengono usati.

## <a name="sample-1-string-parameters"></a>Esempio 1. Parametri stringa

Questa definizione di criteri usa due parametri, **tagName** e **tagValue**, per impostare gli elementi da cercare nelle risorse con l'assegnazione di criteri. Questo formato consente di usare la definizione dei criteri per un numero qualsiasi di combinazioni di nomi e valori di tag, ma di mantenere una singola definizione di criteri.

> [!NOTE]
> Per un esempio di tag che usa la **modalità** _All_ e funziona con un gruppo di risorse, vedere [Criterio: Tag - Esempio n. 1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Esempio 1. Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

In questa parte della definizione dei criteri, il parametro **tagName** viene definito come _stringa_ e viene fornita una descrizione del relativo uso.

Il parametro viene quindi usato nel blocco **policyRule.if** per rendere dinamico il criterio. Qui viene usato per definire il campo valutato, che è un tag con il valore **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Esempio 2: Parametri di matrice

Questa definizione di criteri usa un singolo parametro, **listOfBandwidthinMbps**, per verificare se la risorsa Express Route Circuit ha configurato l'impostazione della larghezza di banda su uno dei valori approvati. In caso di mancata corrispondenza, la creazione o l'aggiornamento della risorsa viene [negato](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Esempio 2: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

In questa parte della definizione dei criteri, il parametro **listOfBandwidthinMbps** viene definito come _matrice_ e viene fornita una descrizione del relativo uso. Essendo una _matrice_, include più valori per cui trovare una corrispondenza.

Il parametro viene quindi usato nel blocco **policyRule.if**. Come parametro di _matrice_, è necessario usare la 
[condizione](../concepts/definition-structure.md#conditions) di _matrice_ **in** o **notIn**.
Qui viene usato per l'alias **serviceProvider.bandwidthInMbps** come uno dei valori definiti.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Esempio 3: Effetto con parametri

Un modo comune per rendere riutilizzabili le definizioni dei criteri è parametrizzare l'effetto stesso. Questo esempio usa un solo parametro, **effect**. La parametrizzazione dell'effetto rende possibile assegnare la stessa definizione a ambiti diversi con effetti diversi.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Esempio 3: Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

In questa parte della definizione dei criteri, il parametro **effect** viene definito come _stringa_. La definizione dei criteri imposta il valore predefinito per un'assegnazione su _audit_ e limita le altre opzioni a _disabled_ e _deny_.

Il parametro viene quindi usato nel blocco **policyRule.then** per l'_effetto_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).