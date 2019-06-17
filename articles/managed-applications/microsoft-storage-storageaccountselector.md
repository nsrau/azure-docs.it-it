---
title: Elemento StorageAccountSelector dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Storage.StorageAccountSelector dell'interfaccia utente per il portale di Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104865"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento Microsoft.Storage.StorageAccountSelector dell'interfaccia utente
Controllo per la selezione di un account di archiviazione nuovo o esistente.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Il controllo visualizza il valore predefinito.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Il controllo consente all'utente di creare un nuovo account di archiviazione o di selezionarne uno esistente.

![Nuovo Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Note
- L'unicità di `defaultValue.name`, se specificato, viene convalidata automaticamente. Se il nome dell'account di archiviazione non è univoco, l'utente deve specificare un nome diverso o scegliere un account di archiviazione esistente.
- Il valore predefinito per `defaultValue.type` è **Premium_LRS**.
- I tipi non specificati in `constraints.allowedTypes` vengono nascosti e i tipi non specificati in `constraints.excludedTypes` vengono visualizzati. `constraints.allowedTypes` e `constraints.excludedTypes` sono entrambi facoltativi, ma non possono essere usati contemporaneamente.
- Se `options.hideExisting` è **true**, l'utente non può scegliere un account di archiviazione esistente. Il valore predefinito è **false**.

## <a name="sample-output"></a>Output di esempio

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
