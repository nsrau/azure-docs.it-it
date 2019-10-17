---
title: Informazioni di riferimento sull'artefatto createUiDefinition dell'applicazione gestita di Azure
description: Viene illustrato come creare l'elemento createUiDefinition per un'applicazione gestita di Azure. Il file è denominato createUiDefinition. JSON.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 09364a849926fc829a890bfcdc8b760c7c7e189c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330187"
---
# <a name="reference-user-interface-elements-artifact"></a>Riferimento: artefatto degli elementi dell'interfaccia utente

Questo articolo è un riferimento per un elemento *createUiDefinition. JSON* nelle applicazioni gestite di Azure. Per ulteriori informazioni sulla creazione di elementi dell'interfaccia utente, vedere [creare elementi dell'interfaccia utente](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Elementi dell'interfaccia utente

Il codice JSON seguente mostra un esempio di file *createUiDefinition. JSON* per le applicazioni gestite di Azure:

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

- [Esercitazione: creare un'applicazione gestita con azioni e risorse personalizzate](tutorial-create-managed-app-with-custom-provider.md)
- [Riferimento: artefatto modello di distribuzione](reference-main-template-artifact.md)
- [Riferimento: elemento della definizione della visualizzazione](reference-view-definition-artifact.md)