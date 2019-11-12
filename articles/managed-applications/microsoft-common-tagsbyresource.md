---
title: Elemento dell'interfaccia utente di Azure TagsByResource
description: Descrive l'elemento Microsoft. Common. TagsByResource dell'interfaccia utente per portale di Azure. Usare per applicare tag a una risorsa durante la distribuzione.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933142"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Elemento Microsoft. Common. TagsByResource dell'interfaccia utente

Controllo per l'associazione dei [tag](../azure-resource-manager/resource-group-using-tags.md) alle risorse in una distribuzione.

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

- È necessario specificare almeno un elemento nella matrice `resources`.
- Ogni elemento in `resources` deve essere un tipo di risorsa completo. Questi elementi vengono visualizzati nell'elenco a discesa **delle risorse** e sono taggable dall'utente.
- L'output del controllo è formattato per facilitare l'assegnazione di valori di tag in un modello di Azure Resource Manager. Per ricevere l'output del controllo in un modello, includere un parametro nel modello, come illustrato nell'esempio seguente:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Per ogni risorsa che può essere contrassegnata, assegnare la proprietà Tags al valore del parametro per il tipo di risorsa:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Usare la funzione [if](../azure-resource-manager/resource-group-template-functions-logical.md#if) per accedere al parametro tagsByResource. Consente di assegnare un oggetto vuoto quando nessun tag viene assegnato al tipo di risorsa specificato.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
