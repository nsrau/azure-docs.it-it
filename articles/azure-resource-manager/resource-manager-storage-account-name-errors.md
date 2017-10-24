---
title: Errori relativi al nome dell'account di archiviazione di Azure | Microsoft Docs
description: Descrive gli errori che possono verificarsi quando si specifica un nome per un account di archiviazione.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Risolvere gli errori relativi ai nomi degli account di archiviazione

Questo articolo descrive gli errori di denominazione che possono verificarsi durante la distribuzione di un account di archiviazione.

## <a name="symptom"></a>Sintomo

Se il nome dell'account di archiviazione include caratteri non consentiti, viene visualizzato un errore simile al seguente:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Per gli account di archiviazione, il nome della risorsa specificato deve essere univoco in Azure. Se non si specifica un nome univoco, verrà visualizzato un errore come il seguente:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se si distribuisce un account di archiviazione con lo steso nome di un account di archiviazione esistente nella sottoscrizione specificando però una diversa località, verrà visualizzato un errore che indica che l'account di archiviazione esiste già in un'altra località. Eliminare l'account di archiviazione esistente oppure specificare la stessa località di tale account.

## <a name="cause"></a>Causa

I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. Il nome deve essere univoco.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Assicurarsi che il nome dell'account di archiviazione sia univoco. È possibile creare un nome univoco concatenando la convenzione di denominazione con il risultato della funzione [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Soluzione 2

Assicurarsi che il nome dell'account di archiviazione non superi i 24 caratteri. La funzione [uniqueString](resource-group-template-functions-string.md#uniquestring) restituisce 13 caratteri. Se si concatena un prefisso o un suffisso al risultato di **uniqueString**, specificare un valore composto al massimo da 11 caratteri.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>Soluzione 3

Assicurarsi che il nome dell'account di archiviazione non includa lettere maiuscole o caratteri speciali.