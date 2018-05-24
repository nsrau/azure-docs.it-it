---
title: Elemento DropDown dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.DropDown dell'interfaccia utente per il portale di Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: c8966c70fba1cbb17d377223ed5348075ebb7adc
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento Microsoft.Common.DropDown dell'interfaccia utente
Controllo di selezione con elenco a discesa.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
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
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- L'etichetta per `constraints.allowedValues` è il testo visualizzato per un elemento e il rispettivo valore è il valore di output dell'elemento in caso di selezione.
- Se specificato, il valore predefinito deve essere un'etichetta presente in `constraints.allowedValues`. Se non è specificato, viene selezionato il primo elemento in `constraints.allowedValues`. Il valore predefinito è **null**.
- `constraints.allowedValues` deve contenere almeno un elemento.
- Questo elemento non supporta la proprietà `constraints.required`. Per emulare questo comportamento, aggiungere un elemento con un'etichetta e un valore `""` (stringa vuota) a `constraints.allowedValues`.

## <a name="sample-output"></a>Output di esempio
```json
"Bar"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
