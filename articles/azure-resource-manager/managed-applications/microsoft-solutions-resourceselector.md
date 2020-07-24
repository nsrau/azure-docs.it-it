---
title: Elemento ResourceSelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. Solutions. ResourceSelector dell'interfaccia utente per portale di Azure. Utilizzato per ottenere un elenco di risorse esistenti.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099054"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Elemento Microsoft. Solutions. ResourceSelector dell'interfaccia utente

ResourceSelector consente agli utenti di selezionare una risorsa esistente da una sottoscrizione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>SCHEMA

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Output di esempio

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Osservazioni

Nella `resourceType` Proprietà specificare lo spazio dei nomi del provider di risorse e il nome del tipo di risorsa per la risorsa che si desidera visualizzare nell'elenco.

La `filter` proprietà limita le opzioni disponibili per le risorse. È possibile limitare i risultati in base alla posizione o alla sottoscrizione. Per visualizzare solo le risorse che corrispondono alla selezione in Nozioni di base, usare `onBasics` . Per visualizzare tutte le risorse, usare `all` . Il valore predefinito è `all`.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
