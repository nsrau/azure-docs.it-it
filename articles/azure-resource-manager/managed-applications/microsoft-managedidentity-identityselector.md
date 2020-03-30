---
title: Elemento dell'interfaccia utente di IdentitySelector
description: Descrive l'elemento dell'interfaccia utente Microsoft.ManagedIdentity.IdentitySelector per il portale di Azure.Describes the Microsoft.ManagedIdentity.IdentitySelector UI element for Azure portal. Utilizzare per assegnare identità gestite a una risorsa.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087544"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Elemento dell'interfaccia utente Microsoft.ManagedIdentity.IdentitySelectorMicrosoft.ManagedIdentity.IdentitySelector UI element

Controllo per l'assegnazione di [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) per una risorsa in una distribuzione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Il controllo è costituito dai seguenti elementi:

![Primo passaggio di Microsoft.ManagedIdentity.IdentitySelector](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Quando l'utente seleziona **Aggiungi**, viene aperto il modulo seguente. L'utente può selezionare una o più identità assegnate dall'utente per la risorsa.

![Passaggio successivo di Microsoft.ManagedIdentity.IdentitySelector](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

Le identità selezionate vengono visualizzate nella tabella. L'utente può aggiungere o eliminare elementi da questa tabella.

![Passaggio terzo di Microsoft.ManagedIdentity.IdentitySelector](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Osservazioni

- Utilizzare **defaultValue.systemAssignedIdentity** per impostare un valore iniziale per il controllo delle opzioni di identità assegnato dal sistema. Il valore predefinito è **Off**. Sono consentiti i seguenti valori:
  - **Attivato:** un'identità assegnata al sistema viene assegnata alla risorsa.
  - **Disattivato:** un'identità assegnata dal sistema non è assegnata alla risorsa.
  - **OnOnly:** un'identità assegnata al sistema viene assegnata alla risorsa. Gli utenti non possono modificare questo valore durante la distribuzione.
  - **OffOnly:** un'identità assegnata dal sistema non viene assegnata alla risorsa. Gli utenti non possono modificare questo valore durante la distribuzione.

- Se **options.hideSystemAssignedIdentity** è impostato su **true**, l'interfaccia utente per configurare l'identità assegnata dal sistema non viene visualizzata. Il valore predefinito per questa opzione è **false**.
- Se **options.hideUserAssignedIdentity** è impostato su **true**, l'interfaccia utente per configurare l'identità assegnata dall'utente non viene visualizzata. Alla risorsa non è assegnata un'identità assegnata dall'utente. Il valore predefinito per questa opzione è **false**.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).