---
title: Elemento dell'interfaccia utente InfoBox
description: Descrive l'elemento dell'interfaccia utente Microsoft.Common.InfoBox per il portale di Azure.Describes the Microsoft.Common.InfoBox UI element for Azure portal. Consente di aggiungere testo o avvisi durante la distribuzione di un'applicazione gestita.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652476"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Elemento Microsoft.Common.InfoBox dell'interfaccia utente

Un controllo che aggiunge una casella di informazioni. La casella contiene testo importante o avvisi che consentono agli utenti di conoscere i valori che forniscono. Può anche fornire un collegamento a un URI per altre informazioni.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>SCHEMA

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Output di esempio

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Osservazioni

* Per `icon`, usare **Nessuno**, **Informazioni**, **Avviso** o **Errore**.
* La proprietà `uri` è facoltativa.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
