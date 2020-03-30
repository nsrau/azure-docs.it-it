---
title: Elemento CreateUiDefinition
description: Viene illustrato come creare l'elemento createUiDefinition per un'applicazione gestita di Azure.Shows how to create the createUiDefinition artifact for an Azure Managed Application. Il file è denominato createUiDefinition.json.The file is named createUiDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651488"
---
# <a name="reference-user-interface-elements-artifact"></a>Riferimento: elemento degli elementi dell'interfaccia utenteReference: User interface elements artifact

Questo articolo è un riferimento per un elemento createUiDefinition.json nelle applicazioni gestite di Azure.This article is a reference for a *createUiDefinition.json* artifact in Azure Managed Applications. Per ulteriori informazioni sulla creazione di elementi dell'interfaccia utente, vedere [Creare elementi dell'interfaccia utente](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Elementi dell'interfaccia utente

Il codice JSON seguente mostra un esempio di file createUiDefinition.json per le applicazioni gestite di Azure:The following JSON shows an example of *createUiDefinition.json* file for Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare un'applicazione gestita con risorse e azioni personalizzateTutorial: Create managed application with custom actions and resources](tutorial-create-managed-app-with-custom-provider.md)
- [Riferimento: Elemento del modello di distribuzioneReference: Deployment template artifact](reference-main-template-artifact.md)
- [Riferimento: Elemento di definizione della vistaReference: View definition artifact](reference-view-definition-artifact.md)