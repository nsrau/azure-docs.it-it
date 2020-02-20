---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474199"
---
### <a name="query-the-storage-queue"></a>Eseguire una query sulla coda di archiviazione

È possibile usare il comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) per visualizzare le code di archiviazione dell'account, come indicato nell'esempio seguente:

```azurecli-interactive
az storage queue list --output tsv
```

L'output di questo comando include una coda denominata `outqueue`, che è la coda creata quando è stata eseguita la funzione.

Usare quindi il comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) per visualizzare i messaggi inclusi in questa coda, come indicato in questo esempio:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

La stringa restituita dovrebbe essere la stessa del messaggio inviato per testare la funzione.

> [!NOTE]  
> L'esempio precedente decodifica la stringa restituita da base64. Il motivo è che i binding di archiviazione della coda scrivono e leggono i dati da Archiviazione di Azure sotto forma di [stringhe base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).