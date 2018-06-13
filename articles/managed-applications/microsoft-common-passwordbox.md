---
title: Elemento PasswordBox dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.PasswordBox dell'interfaccia utente per il portale di Azure.
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
ms.openlocfilehash: eab4bea750c28ac3b156363f5f63d8c7c8f4075a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260987"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento Microsoft.Common.PasswordBox dell'interfaccia utente
Controllo che può essere usato per fornire e confermare una password.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>SCHEMA
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
- Per i dettagli sull'implementazione di `constraints`, vedere [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se la proprietà `options.hideConfirmation` è impostata su **true**, la seconda casella di testo per la conferma della password dell'utente è nascosta. Il valore predefinito è **false**.

## <a name="sample-output"></a>Output di esempio
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
