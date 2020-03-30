---
title: Elemento dell'interfaccia utente TagsByResource
description: Descrive l'elemento dell'interfaccia utente Microsoft.Common.TagsByResource per il portale di Azure.Describes the Microsoft.Common.TagsByResource UI element for Azure portal. Utilizzare per applicare tag a una risorsa durante la distribuzione.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652203"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Elemento dell'interfaccia utente Microsoft.Common.TagsByResourceMicrosoft.Common.TagsByResource UI element

Controllo per l'associazione di [tag](../management/tag-resources.md) alle risorse in una distribuzione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Osservazioni

- È necessario specificare `resources` almeno un elemento nella matrice.
- Ogni elemento `resources` in deve essere un tipo di risorsa completo. Questi elementi vengono visualizzati nell'elenco a discesa **Risorsa** e sono contrassegnati dall'utente.
- L'output del controllo viene formattato per semplificare l'assegnazione di valori di tag in un modello di Azure Resource Manager.The output of the control is formatted for easy assignment of tag values in an Azure Resource Manager template. Per ricevere l'output del controllo in un modello, includere un parametro nel modello, come illustrato nell'esempio seguente:To receive the control's output in a template, include a parameter in your template as shown in the following example:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Per ogni risorsa a cui è possibile assegnare tag, assegnare la proprietà tags al valore del parametro per quel tipo di risorsa:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Utilizzare la funzione [if](../templates/template-functions-logical.md#if) quando si accede al parametro tagsByResource. Consente di assegnare un oggetto vuoto quando non sono assegnati tag al tipo di risorsa specificato.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
