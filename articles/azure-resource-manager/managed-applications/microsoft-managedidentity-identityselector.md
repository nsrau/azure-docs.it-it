---
title: Elemento IdentitySelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. ManagedIdentity. IdentitySelector dell'interfaccia utente per portale di Azure. Usare per assegnare identità gestite a una risorsa.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063380"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Elemento Microsoft. ManagedIdentity. IdentitySelector dell'interfaccia utente

Controllo per l'assegnazione di [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) per una risorsa in una distribuzione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Il controllo è costituito dagli elementi seguenti:

![Primo passaggio di Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Quando l'utente seleziona **Aggiungi**, viene visualizzato il seguente modulo. L'utente può selezionare una o più identità assegnate dall'utente per la risorsa.

![Secondo passaggio di Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

Le identità selezionate vengono visualizzate nella tabella. L'utente può aggiungere o eliminare elementi da questa tabella.

![Terzo passaggio di Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

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

## <a name="remarks"></a>Commenti

- Utilizzare **defaultValue.systemAssignedIdentity** per impostare un valore iniziale per il controllo delle opzioni di identità assegnato dal sistema. Il valore predefinito è **off**. Sono consentiti i valori seguenti:
  - **On** : un'identità assegnata al sistema viene assegnata alla risorsa.
  - **Disattivato** : un'identità assegnata dal sistema non viene assegnata alla risorsa.
  - **Ononly** : un'identità assegnata a un sistema viene assegnata alla risorsa. Gli utenti non possono modificare questo valore durante la distribuzione.
  - **OffOnly** : un'identità assegnata dal sistema non viene assegnata alla risorsa. Gli utenti non possono modificare questo valore durante la distribuzione.

- Se **options. hideSystemAssignedIdentity** è impostato su **true**, non viene visualizzata l'interfaccia utente per configurare l'identità assegnata dal sistema. Il valore predefinito per questa opzione è **false**.
- Se **options. hideUserAssignedIdentity** è impostato su **true**, l'interfaccia utente per configurare l'identità assegnata dall'utente non viene visualizzata. Alla risorsa non viene assegnata un'identità assegnata dall'utente. Il valore predefinito per questa opzione è **false**.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).