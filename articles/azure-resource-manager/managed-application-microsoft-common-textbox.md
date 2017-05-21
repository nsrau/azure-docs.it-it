---
title: Elemento TextBox dell&quot;interfaccia utente dell&quot;applicazione gestita di Azure | Microsoft Docs
description: Illustra l&quot;elemento Microsoft.Common.TextBox dell&quot;interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Elemento Microsoft.Common.TextBox dell'interfaccia utente
Controllo che è possibile usare per modificare il testo non formattato.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo la casella di testo deve contenere un valore. Il valore predefinito è **false**.
- `constraints.regex` è un modello di espressione regolare di JavaScript. Se specificato, perché la convalida venga abbia esito positivo il valore della casella di testo deve corrispondere al modello. Il valore predefinito è **null**.
- `constraints.validationMessage` è una stringa da visualizzare quando il valore della casella di testo non supera la convalida. Se non specificata, vengono usati i messaggi di convalida predefiniti della casella di testo. Il valore predefinito è **null**.
- È possibile specificare un valore per `constraints.regex` quando `constraints.required` è impostato su **false**. In questo scenario non è richiesto un valore perché la convalida della casella di testo abbia esito positivo. Se viene specificato, deve corrispondere al modello di espressione regolare.

## <a name="sample-output"></a>Output di esempio

```json
"foobar"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).

