---
title: Elemento PasswordBox dell&quot;interfaccia utente di Applicazione gestita di Azure | Microsoft Docs
description: Illustra l&quot;elemento Microsoft.Common.PasswordBox dell&quot;interfaccia utente per le applicazioni gestite di Azure
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
ms.openlocfilehash: 4646521531e839e4751b4f01f7a66876dac96b57
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento Microsoft.Common.PasswordBox dell'interfaccia utente
Controllo che può essere usato per fornire e confermare una password.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- Questo elemento non supporta la proprietà `defaultValue`.
- Per i dettagli sull'implementazione di `constraints`, vedere [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md).
- Se la proprietà `options.hideConfirmation` è impostata su **true**, la seconda casella di testo per la conferma della password dell'utente è nascosta. Il valore predefinito è **false**.

## <a name="sample-output"></a>Output di esempio
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).

