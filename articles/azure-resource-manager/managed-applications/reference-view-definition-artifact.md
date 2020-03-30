---
title: Riferimento elemento definizione vista
description: Fornisce un esempio di elemento di definizione della visualizzazione per le applicazioni gestite di Azure.Provides an example of view definition artifact for Azure Managed Applications. Il nome del file è viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651202"
---
# <a name="reference-view-definition-artifact"></a>Riferimento: Elemento di definizione della vistaReference: View definition artifact

Questo articolo è un riferimento per un elemento viewDefinition.json nelle applicazioni gestite di Azure.This article is a reference for a *viewDefinition.json* artifact in Azure Managed Applications. Per ulteriori informazioni sulla configurazione delle viste di creazione, consultate Elemento di [definizione delle viste.](concepts-view-definition.md)

## <a name="view-definition"></a>Visualizzare la definizione

Il codice JSON seguente mostra un esempio di file viewDefinition.json per le applicazioni gestite di Azure:The following JSON shows an example of *viewDefinition.json* file for Azure Managed Applications:

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

- [Esercitazione: Creare un'applicazione gestita con risorse e azioni personalizzateTutorial: Create managed application with custom actions and resources](tutorial-create-managed-app-with-custom-provider.md)
- [Riferimento: elemento degli elementi dell'interfaccia utenteReference: User interface elements artifact](reference-createuidefinition-artifact.md)
- [Riferimento: Elemento del modello di distribuzioneReference: Deployment template artifact](reference-main-template-artifact.md)