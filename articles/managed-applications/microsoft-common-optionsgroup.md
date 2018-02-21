---
title: Elemento OptionsGroup dell'interfaccia utente di Applicazione gestita di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.OptionsGroup dell'interfaccia utente per le applicazioni gestite di Azure
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
ms.openlocfilehash: 0ba820176e0231437e878922cb508cd3b97d46c3
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento Microsoft.Common.OptionsGroup dell'interfaccia utente
Controllo di selezione con una riga di opzioni disponibili. Usare questo elemento quando si [crea un'applicazione Azure gestita](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- L'etichetta per `constraints.allowedValues` è il testo visualizzato per un elemento e il rispettivo valore è il valore di output dell'elemento in caso di selezione.
- Se specificato, il valore predefinito deve essere un'etichetta presente in `constraints.allowedValues`. Se non è specificato, viene selezionato il primo elemento in `constraints.allowedValues` per impostazione predefinita. Il valore predefinito è **null**.
- `constraints.allowedValues` deve contenere almeno un elemento.
- Questo elemento non supporta la proprietà `constraints.required`. Per una convalida corretta, è necessario selezionare un elemento.

## <a name="sample-output"></a>Output di esempio
```json
"Bar"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](overview.md).
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
