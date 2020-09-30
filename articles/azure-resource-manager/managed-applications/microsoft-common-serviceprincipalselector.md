---
title: Elemento ServicePrincipalSelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. Common. ServicePrincipalSelector dell'interfaccia utente per portale di Azure. Fornisce un elenco a discesa per scegliere un identificatore dell'applicazione e una casella di testo per inserire una password o un'identificazione personale del certificato.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575997"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elemento Microsoft. Common. ServicePrincipalSelector dell'interfaccia utente

Controllo che consente agli utenti di selezionare un'entità servizio esistente o di registrarne una nuova. Quando si seleziona **Crea nuovo**, vengono eseguiti i passaggi per registrare una nuova applicazione. Quando si seleziona un'applicazione esistente, il controllo fornisce una casella di testo per l'immissione di una password o di un'identificazione personale del certificato.

## <a name="ui-sample"></a>Esempio di interfaccia utente

La visualizzazione predefinita è determinata dai valori nella `defaultValue` Proprietà. Se la `principalId` proprietà contiene un identificatore univoco globale (Guid) valido, il controllo Cerca l'ID oggetto dell'applicazione. Il valore predefinito viene applicato se l'utente non effettua una selezione dall'elenco a discesa.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Visualizzazione iniziale Microsoft. Common. ServicePrincipalSelector":::

Quando si seleziona **Crea nuovo** o un identificatore dell'applicazione esistente nell'elenco a discesa, viene visualizzato il **tipo di autenticazione** per immettere una password o un'identificazione personale del certificato nella casella di testo.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Visualizzazione iniziale Microsoft. Common. ServicePrincipalSelector":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Commenti

- Le proprietà obbligatorie sono:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Specifica i valori predefiniti `principalId` e `name` .

- Le proprietà facoltative sono:
  - `toolTip`: Connette una descrizione comando `infoBalloon` a ogni etichetta.
  - `visible`: Nascondere o visualizzare il controllo.
  - `options`: Specifica se l'opzione di identificazione personale del certificato deve essere resa disponibile.
  - `constraints`: Vincoli Regex per la convalida della password.

## <a name="example"></a>Esempio

Di seguito è riportato un esempio del `Microsoft.Common.ServicePrincipalSelector` controllo. La `defaultValue` proprietà imposta `principalId` su `<default guid>` come segnaposto per un GUID dell'identificatore dell'applicazione predefinito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Output di esempio

`appId`È l'ID della registrazione dell'applicazione selezionata o creata. `objectId`È una matrice di ObjectID per le entità servizio configurate per la registrazione dell'applicazione selezionata.

Quando non viene effettuata alcuna selezione dall'elenco a discesa, il `newOrExisting` valore della proprietà è **nuovo**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Quando si seleziona **Crea nuovo** o un identificatore applicazione esistente dall'elenco a discesa `newOrExisting` , il valore della proprietà è **esistente**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
