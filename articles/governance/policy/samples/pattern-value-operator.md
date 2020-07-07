---
title: 'Criterio: Operatore value in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare l'operatore value in una definizione di criteri.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: e246e3a5e2517fa80626081227070bcb2f967784
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565658"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Modello di Criteri di Azure: operatore value

L'operatore [value](../concepts/definition-structure.md#value) valuta i [parametri](../concepts/definition-structure.md#parameters), le [funzioni modello supportate](../concepts/definition-structure.md#policy-functions) o i valori letterali in base a un valore fornito per una determinata [condizione](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri non riesce. Una valutazione non riuscita è un risultato **deny** implicito. Per altre informazioni, vedere come [evitare errori dei modelli](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Definizione di criteri di esempio

Questa definizione di criteri aggiunge o sostituisce il tag specificato nel parametro **tagName** (_stringa_) nelle risorse ed eredita il valore per **tagName** dal gruppo di risorse in cui si trova la risorsa. Questa valutazione si verifica quando la risorsa viene creata o aggiornata. Come effetto [modify](../concepts/effects.md#modify), la correzione può essere eseguita su risorse esistenti tramite un'[attività di correzione](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

L'operatore **value** viene usato all'interno del blocco **policyRule.if** all'interno di **properties**. In questo esempio viene usato l'[operatore logico](../concepts/definition-structure.md#logical-operators) **allOf** per dichiarare che entrambe le istruzioni condizionali devono essere vere perché venga implementato l'effetto **modify**.

**value** valuta il risultato della funzione modello [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) in base alla condizione **notEquals** di un valore vuoto. Se il nome del tag fornito in **tagName** nel gruppo di risorse padre esiste, l'istruzione condizionale restituisce true.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).