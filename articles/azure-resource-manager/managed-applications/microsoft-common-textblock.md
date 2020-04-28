---
title: Elemento TextBlock dell'interfaccia utente
description: Descrive l'elemento Microsoft.Common.TextBlock dell'interfaccia utente per il portale di Azure. Utilizzare per aggiungere testo all'interfaccia.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652216"
---
# <a name="microsoftcommontextblock-ui-element"></a>Elemento Microsoft.Common.TextBlock dell'interfaccia utente

Controllo che può essere usato per aggiungere testo all'interfaccia del portale.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Output di esempio

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
