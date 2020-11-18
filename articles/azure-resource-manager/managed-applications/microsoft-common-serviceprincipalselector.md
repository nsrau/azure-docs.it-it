---
title: Elemento ServicePrincipalSelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. Common. ServicePrincipalSelector dell'interfaccia utente per portale di Azure. Fornisce un controllo per scegliere un'applicazione e una casella di testo per l'immissione di una password o di un'identificazione personale del certificato.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 9d41e41f110e927f436b38d6291719c138defa53
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94745763"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elemento Microsoft. Common. ServicePrincipalSelector dell'interfaccia utente

Controllo che consente agli utenti di selezionare un' [entità servizio](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) esistente o di registrare una nuova applicazione. Quando si seleziona **Crea nuovo**, seguire i passaggi per registrare una nuova applicazione. Quando si seleziona un'applicazione esistente, il controllo fornisce una casella di testo per l'immissione di una password o di un'identificazione personale del certificato.

## <a name="ui-samples"></a>Esempi di interfaccia utente

È possibile usare un'applicazione predefinita, creare una nuova applicazione o usare un'applicazione esistente.

### <a name="use-default-application-or-create-new"></a>USA applicazione predefinita o Crea nuovo

La visualizzazione predefinita è determinata dai valori nella `defaultValue` proprietà e il tipo di **entità servizio** è impostato su **Crea nuovo**. Se la `principalId` proprietà contiene un identificatore univoco globale (Guid) valido, il controllo Cerca l'oggetto dell'applicazione `objectId` . Il valore predefinito viene applicato se l'utente non effettua una selezione dal controllo.

Se si desidera registrare una nuova applicazione, selezionare **Cambia selezione** e visualizzare la finestra di dialogo **registra un'applicazione** . Immettere il **nome**, il **tipo di account supportato** e selezionare il pulsante **Register (registra** ).

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Visualizzazione iniziale Microsoft. Common. ServicePrincipalSelector.":::

Dopo la registrazione di una nuova applicazione, utilizzare il **tipo di autenticazione** per immettere una password o un'identificazione personale del certificato.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Autenticazione Microsoft. Common. ServicePrincipalSelector.":::

### <a name="use-existing-application"></a>USA applicazione esistente

Per usare un'applicazione esistente, scegliere **Seleziona esistente** , quindi selezionare **Crea selezione**. Utilizzare la finestra di dialogo **Seleziona un'applicazione** per cercare il nome dell'applicazione. Nei risultati selezionare l'applicazione e quindi il pulsante **Seleziona** . Dopo aver selezionato un'applicazione, il controllo Visualizza il **tipo di autenticazione** per immettere una password o un'identificazione personale del certificato.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector selezionare applicazione esistente.":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
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

- Di seguito sono riportate le proprietà obbligatorie:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Specifica i valori predefiniti `principalId` e `name` .

- Le proprietà facoltative sono le seguenti:
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
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
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

`appId`È l'ID della registrazione dell'applicazione selezionata o creata. `objectId`È una matrice di ID oggetto per le entità servizio configurate per la registrazione dell'applicazione selezionata.

Quando non viene effettuata alcuna selezione dal controllo, il `newOrExisting` valore della proprietà è **nuovo**:

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

Quando si seleziona **Crea nuovo** o un'applicazione esistente dal controllo, il `newOrExisting` valore della proprietà è **esistente**:

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
