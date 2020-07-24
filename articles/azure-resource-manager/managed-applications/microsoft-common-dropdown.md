---
title: Elemento DropDown dell'interfaccia utente
description: Illustra l'elemento Microsoft.Common.DropDown dell'interfaccia utente per il portale di Azure. Consente di selezionare le opzioni disponibili durante la distribuzione di un'applicazione gestita.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004462"
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento Microsoft.Common.DropDown dell'interfaccia utente

Controllo di selezione con elenco a discesa. È possibile consentire la selezione di un solo elemento o più elementi. Facoltativamente, è anche possibile includere una descrizione con gli elementi.

## <a name="ui-sample"></a>Esempio di interfaccia utente

L'elemento DropDown presenta opzioni diverse che ne determinano l'aspetto nel portale.

Quando per la selezione è consentito un solo elemento, il controllo viene visualizzato come segue:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Selezione singola Microsoft. Common. DropDown":::

Quando vengono incluse le descrizioni, il controllo viene visualizzato come:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Selezione singola di Microsoft. Common. DropDown con le descrizioni":::

Quando è abilitata la funzionalità di selezione più, il controllo aggiunge un'opzione **Seleziona tutto** e le caselle di controllo per selezionare più elementi:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Selezione multifunzione Microsoft. Common. DropDown":::

Le descrizioni possono essere incluse con la selezione MultiSelect abilitata.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft. Common. DropDown MultiSelect con le descrizioni":::

Quando il filtro è abilitato, il controllo include una casella di testo per l'aggiunta del valore di filtro.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. DropDown MultiSelect con le descrizioni":::

## <a name="schema"></a>SCHEMA

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"two"
```

## <a name="remarks"></a>Osservazioni

- Consente `multiselect` di specificare se gli utenti possono selezionare più di un elemento.
- Per impostazione predefinita, il valore di `selectAll` è `true` quando è abilitata la selezione MultiSelect.
- La `filter` proprietà consente agli utenti di eseguire la ricerca all'interno di un lungo elenco di opzioni.
- L'etichetta per `constraints.allowedValues` è il testo visualizzato per un elemento e il rispettivo valore è il valore di output dell'elemento in caso di selezione.
- Se specificato, il valore predefinito deve essere un'etichetta presente in `constraints.allowedValues`. Se non è specificato, viene selezionato il primo elemento in `constraints.allowedValues`. Il valore predefinito è **null**.
- `constraints.allowedValues` deve contenere almeno un elemento.
- Per emulare un valore non richiesto, aggiungere un elemento con un'etichetta e un valore `""` (stringa vuota) a `constraints.allowedValues`.
- La `defaultDescription` proprietà viene utilizzata per gli elementi che non dispongono di una descrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
