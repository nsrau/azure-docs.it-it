---
title: Elemento TextBlock dell'interfaccia utente
description: Descrive l'elemento Microsoft.Common.TextBlock dell'interfaccia utente per il portale di Azure. Utilizzare per aggiungere testo all'interfaccia.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063885"
---
# <a name="microsoftcommontextblock-ui-element"></a>Elemento Microsoft.Common.TextBlock dell'interfaccia utente

Controllo che può essere usato per aggiungere testo all'interfaccia del portale.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

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
