---
title: Elemento OptionsGroup dell'interfaccia utente
description: Illustra l'elemento Microsoft.Common.OptionsGroup dell'interfaccia utente per il portale di Azure. Consente agli utenti di selezionare le opzioni disponibili durante la distribuzione di un'applicazione gestita.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87004192"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento Microsoft.Common.OptionsGroup dell'interfaccia utente

Il controllo OptionsGroup consente agli utenti di selezionare un'opzione da due o più opzioni. Un utente può selezionare una sola opzione.

> [!NOTE]
> In passato, questo controllo eseguiva il rendering orizzontale delle opzioni. A questo punto, il controllo Visualizza le opzioni verticalmente come pulsanti di opzione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
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

## <a name="remarks"></a>Commenti

- L'etichetta per `constraints.allowedValues` è il testo visualizzato per un elemento e il rispettivo valore è il valore di output dell'elemento in caso di selezione.
- Se specificato, il valore predefinito deve essere un'etichetta presente in `constraints.allowedValues`. Se non è specificato, viene selezionato il primo elemento in `constraints.allowedValues` per impostazione predefinita. Il valore predefinito è **null**.
- `constraints.allowedValues` deve contenere almeno un elemento.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
