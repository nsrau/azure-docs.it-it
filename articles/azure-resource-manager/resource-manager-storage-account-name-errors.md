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
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c266e1073722733ec8b7353c6fdddc3ae341ab20
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
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

Assicurarsi che il nome dell'account di archiviazione sia univoco. È possibile creare un nome univoco concatenando la convenzione di denominazione con il risultato della funzione [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

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

Assicurarsi che il nome dell'account di archiviazione non includa lettere maiuscole o caratteri speciali.