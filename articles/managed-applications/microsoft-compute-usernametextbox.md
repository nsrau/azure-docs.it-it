---
title: Elemento UserNameTextBox dell'interfaccia utente dell'applicazione gestita di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Compute.UserNameTextBox dell'interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6a395915af274750eb57a085ee51b55fdd392615
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento Microsoft.Compute.UserNameTextBox dell'interfaccia utente
Controllo casella di testo con convalida predefinita per i nomi utente di Windows e Linux. Usare questo elemento quando si [crea un'applicazione Azure gestita](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo la casella di testo deve contenere un valore. Il valore predefinito è **true**.
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**.
- `constraints.regex` è un modello di espressione regolare di JavaScript. Se specificato, perché la convalida venga abbia esito positivo il valore della casella di testo deve corrispondere al modello. Il valore predefinito è **null**.
- `constraints.validationMessage` è una stringa da visualizzare quando il valore della casella di testo non supera la convalida specificata da `constraints.regex`. Se non specificata, vengono usati i messaggi di convalida predefiniti della casella di testo. Il valore predefinito è **null**.
- La convalida predefinita di questo elemento su basa sul valore specificato per `osPlatform`. È possibile usare la convalida predefinita insieme a un'espressione regolare personalizzata.
Se si specifica un valore per `constraints.regex`, viene attivata sia la convalida predefinita che quella personalizzata.

## <a name="sample-output"></a>Output di esempio
```json
"tabrezm"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](overview.md).
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
