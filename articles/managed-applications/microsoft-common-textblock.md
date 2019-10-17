---
title: Elemento TextBlock dell'interfaccia utente di Azure | Microsoft Docs
description: Descrive l'elemento Microsoft.Common.TextBlock dell'interfaccia utente per il portale di Azure. Utilizzare per aggiungere testo all'interfaccia.
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
ms.openlocfilehash: da2453889f04352dbabe182772312d70deec4464
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331617"
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
