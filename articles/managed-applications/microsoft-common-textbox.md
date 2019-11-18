---
title: Elemento TextBox dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.TextBox dell'interfaccia utente per il portale di Azure. Usare per aggiungere testo non formattato.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a5f28b763023bb60bca8f79bcaa9f2169281e466
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151106"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento Microsoft.Common.TextBox dell'interfaccia utente

Controllo che è possibile usare per modificare il testo non formattato.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"my text value"
```

## <a name="remarks"></a>Osservazioni

- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo la casella di testo deve avere un valore. Il valore predefinito è **false**.
- `constraints.regex` è un modello di espressione regolare di JavaScript. Se specificato, perché la convalida venga abbia esito positivo il valore della casella di testo deve corrispondere al modello. Il valore predefinito è **null**.
- `constraints.validationMessage` è una stringa da visualizzare quando il valore della casella di testo non supera la convalida. Se non specificata, vengono usati i messaggi di convalida predefiniti della casella di testo. Il valore predefinito è **null**.
- È possibile specificare un valore per `constraints.regex` quando `constraints.required` è impostato su **false**. In questo scenario non è richiesto un valore perché la convalida della casella di testo abbia esito positivo. Se viene specificato, deve corrispondere al modello di espressione regolare.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
