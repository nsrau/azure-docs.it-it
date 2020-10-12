---
title: CheckBox-elemento dell'interfaccia utente
description: Descrive l'elemento Microsoft. Common. CheckBox dell'interfaccia utente per portale di Azure. Consente agli utenti di selezionare o deselezionare un'opzione.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098549"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Elemento Microsoft. Common. CheckBox dell'interfaccia utente

Il controllo CheckBox consente agli utenti di selezionare o deselezionare un'opzione. Il controllo restituisce **true** quando il controllo è selezionato o **false** se non è selezionato.

## <a name="ui-sample"></a>Esempio di interfaccia utente

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>SCHEMA

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Output di esempio

```json
true
```

## <a name="remarks"></a>Commenti

Quando si imposta **obbligatorio** su **true**, l'utente deve selezionare la casella di controllo. Se l'utente non seleziona la casella di controllo, viene visualizzato il messaggio di convalida.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
