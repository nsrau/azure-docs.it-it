---
title: 'Criterio: Distribuire le risorse con una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come distribuire risorse con una definizione di criteri.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77169989"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Modello di Criteri di Azure: distribuire risorse

L'effetto [deployIfNotExists](../concepts/effects.md#deployifnotexists) consente di distribuire un [modello di Azure Resource Manager](../../../azure-resource-manager/templates/overview.md) quando si crea o si aggiorna una risorsa non conforme. Questo approccio può essere preferibile all'uso dell'effetto [deny](../concepts/effects.md#deny), perché consente di continuare a creare risorse, ma assicura che vengano apportate modifiche per renderle conformi.

## <a name="sample-policy-definition"></a>Definizione di criteri di esempio

Questa definizione di criteri usa l'operatore **field** per valutare l'oggetto `type` della risorsa creata o aggiornata. Quando tale risorsa è _Microsoft.Network/virtualNetworks_, il criterio cerca un network watcher nella posizione della risorsa nuova o aggiornata. Se non viene trovato un network watcher corrispondente, viene distribuito il modello di Resource Manager per creare la risorsa mancante.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Spiegazione

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Il blocco **properties.policyRule.then.details** indica a Criteri di Azure cosa cercare in relazione alla risorsa creata o aggiornata nel blocco **properties.policyRule.if**. In questo esempio è necessario che esista un network watcher nel gruppo di risorse **networkWatcherRG** con **field** `location` equivalente alla posizione della risorsa nuova o aggiornata. L'uso della funzione `field()` consente a **existenceCondition** di accedere alle proprietà della risorsa nuova o aggiornata, in particolare la proprietà `location`.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

La proprietà della _matrice_ **roleDefinitionIds** nel blocco **properties.policyRule.then.details** indica alla definizione di criteri quali diritti sono necessari all'identità gestita per distribuire il modello di Resource Manager incluso. Questa proprietà deve essere impostata in modo da includere i ruoli con le autorizzazioni necessarie per la distribuzione del modello, ma deve basarsi sul concetto del 'principio dei privilegi minimi' e avere solo le operazioni necessarie e nient'altro.

#### <a name="deployment-template"></a>Modello di distribuzione

La parte **deployment** della definizione di criteri include un blocco **properties** che definisce i tre componenti principali:

- **mode**: questa proprietà imposta la [modalità di distribuzione](../../../azure-resource-manager/templates/deployment-modes.md) del modello.

- **template**: questa proprietà include il modello stesso. In questo esempio, il parametro modello **location** imposta la posizione della nuova risorsa network watcher.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters**: questa proprietà definisce i parametri forniti a **template**. I nomi dei parametri devono corrispondere a quanto definito in **template**. In questo esempio il parametro è denominato **location** per la corrispondenza. Il valore di **location** usa di nuovo la funzione `field()` per ottenere il valore della risorsa valutata, ovvero la rete virtuale nel blocco **policyRule.if**.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).