---
title: Errori relativi al nome dell'account di archiviazione
description: Descrive gli errori che possono verificarsi quando si specifica un nome per un account di archiviazione.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 6af75470ebab61a1eaf7afd0bf946564c5300611
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149417"
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