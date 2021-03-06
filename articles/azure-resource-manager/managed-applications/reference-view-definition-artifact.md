---
title: Informazioni di riferimento sugli elementi della definizione di visualizzazione
description: Viene fornito un esempio di elemento della definizione della vista per le applicazioni gestite di Azure. Il nome del file è viewDefinition.js.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651202"
---
# <a name="reference-view-definition-artifact"></a>Riferimento: elemento della definizione della visualizzazione

Questo articolo è un riferimento per un *viewDefinition.jssull'* artefatto nelle applicazioni gestite di Azure. Per ulteriori informazioni sulla creazione della configurazione delle visualizzazioni, vedere l' [elemento della definizione della vista](concepts-view-definition.md).

## <a name="view-definition"></a>Visualizzare la definizione

Il codice JSON seguente mostra un esempio di *viewDefinition.jssu* file per le applicazioni gestite di Azure:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un'applicazione gestita con azioni e risorse personalizzate](tutorial-create-managed-app-with-custom-provider.md)
- [Riferimento: artefatto degli elementi dell'interfaccia utente](reference-createuidefinition-artifact.md)
- [Riferimento: artefatto modello di distribuzione](reference-main-template-artifact.md)