---
title: Informazioni di riferimento sull'artefatto di definizione della vista applicazione gestita
description: Questo articolo è un riferimento all'elemento della definizione della vista.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: e60f26fe0a7144d768bac020d62c61cb92594914
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336543"
---
# <a name="reference-view-definition-artifact"></a>Riferimenti: Artefatto di definizione delle visualizzazioni

Questo articolo è un riferimento per un elemento *viewDefinition. JSON* nelle applicazioni gestite di Azure. Per ulteriori informazioni sulla creazione della configurazione delle visualizzazioni, vedere l' [elemento della definizione della vista](concepts-view-definition.md).

## <a name="view-definition"></a>Visualizza definizione

Il codice JSON seguente mostra un esempio di file *viewDefinition. JSON* per le applicazioni gestite di Azure:

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

- [Esercitazione: Creare un'applicazione gestita con azioni e risorse personalizzate](tutorial-create-managed-app-with-custom-provider.md)
- [Informazioni di riferimento: Elemento dell'interfaccia utente elementi](reference-createuidefinition-artifact.md)
- [Informazioni di riferimento: Artefatto modello di distribuzione](reference-main-template-artifact.md)